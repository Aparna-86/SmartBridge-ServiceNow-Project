# Server Scripts — Smart Library Request Workflow

**Scope:** `x_univ_library`  
**Platform:** ServiceNow Washington DC

---

## Scheduled Job: Daily Overdue Detection

| Property | Value |
| --- | --- |
| **Name** | Library Daily Overdue Detection |
| **Schedule** | Daily at 06:00 |
| **Condition** | — |
| **Application** | `x_univ_library` |

### Description

Runs every morning at 06:00 to detect issuance records where the due date has passed and the book has not been returned. Updates each overdue record's status from "Issued" to "Overdue", increments the student's `u_total_fines` by the configured daily fine rate multiplied by overdue days, and triggers the "Library Overdue Reminder" notification to the student and the "Library Overdue Notice Librarian" notification to the librarian group.

### Script

```javascript
var LibraryDailyOverdueDetection = Class.create();
LibraryDailyOverdueDetection.prototype = {
    initialize: function() {},

    run: function() {
        var fineRate = this._getFineRate();
        var overdueRecords = this._getOverdueIssuanceRecords();

        while (overdueRecords.next()) {
            this._processOverdue(overdueRecords, fineRate);
        }
    },

    _getFineRate: function() {
        var config = new GlideRecord('u_library_configuration');
        config.addQuery('u_config_key', 'daily_fine_rate');
        config.query();
        return config.next() ? parseFloat(config.u_config_value) : 0.50;
    },

    _getOverdueIssuanceRecords: function() {
        var gr = new GlideRecord('u_library_issuance_records');
        gr.addQuery('u_status', 'Issued');
        gr.addQuery('u_due_date', '<', gs.daysAgoStart(0));
        gr.query();
        return gr;
    },

    _processOverdue: function(issuanceRec, fineRate) {
        var overdueDays = gs.dateDiff(issuanceRec.u_due_date, gs.now(), true) / 86400;
        overdueDays = Math.ceil(Math.max(overdueDays, 0));

        issuanceRec.u_status = 'Overdue';
        issuanceRec.u_fine_amount = overdueDays * fineRate;
        issuanceRec.setWorkflow(false);
        issuanceRec.update();

        var studentGr = new GlideRecord('u_library_students');
        if (studentGr.get(issuanceRec.u_student)) {
            studentGr.u_total_fines = parseFloat(studentGr.u_total_fines) + issuanceRec.u_fine_amount;
            studentGr.update();
        }

        var notificationMgr = new LibraryNotificationManager();
        notificationMgr.sendOverdueNotification(issuanceRec);
        notificationMgr.sendOverdueLibrarianAlert(issuanceRec);
    },

    type: 'LibraryDailyOverdueDetection'
};
```

### Execution

Instantiated and executed by the Scheduled Job Definition:

```javascript
var job = new LibraryDailyOverdueDetection();
job.run();
```

---

## Scheduled Job: Weekly Report Delivery

| Property | Value |
| --- | --- |
| **Name** | Library Weekly Report Delivery |
| **Schedule** | Every Monday at 07:00 |
| **Condition** | — |
| **Application** | `x_univ_library` |

### Description

Runs every Monday at 07:00 to generate a comprehensive weekly statistics report. Aggregates data from the past week (Monday 00:00 to Sunday 23:59) across all application tables. Generates a PDF report containing: total borrow requests submitted, approved, rejected; books issued and returned; overdue statistics; approval SLA performance; top borrowed books; and category distribution. Emails the PDF to all users with the `snc_library_manager` role.

### Script

```javascript
var LibraryWeeklyReportDelivery = Class.create();
LibraryWeeklyReportDelivery.prototype = {
    initialize: function() {},

    run: function() {
        var reportEngine = new LibraryReportEngine();
        var weekStart = gs.beginOfLastWeek();
        var weekEnd = gs.endOfLastWeek();

        var reportData = this._collectReportData(weekStart, weekEnd);
        var pdfContent = reportEngine.generatePDFReport('Weekly Statistics', reportData);
        var attachmentSysId = this._attachReport(pdfContent);

        this._emailReport(attachmentSysId, weekStart, weekEnd);
    },

    _collectReportData: function(weekStart, weekEnd) {
        return {
            totalRequests: this._countRecords('u_library_borrow_requests', weekStart, weekEnd),
            approvedRequests: this._countByStatus('u_library_borrow_requests', 'Approved', weekStart, weekEnd),
            rejectedRequests: this._countByStatus('u_library_borrow_requests', 'Rejected', weekStart, weekEnd),
            booksIssued: this._countRecords('u_library_issuance_records', weekStart, weekEnd),
            booksReturned: this._countRecords('u_library_return_records', weekStart, weekEnd),
            overdueCount: this._getOverdueCount(),
            slaPerformance: this._getSLAPerformance(weekStart, weekEnd),
            topBooks: this._getTopBorrowedBooks(weekStart, weekEnd),
            categoryDistribution: this._getCategoryDistribution()
        };
    },

    _countRecords: function(table, start, end) {
        var gr = new GlideRecord(table);
        gr.addQuery('sys_created_on', '>=', start);
        gr.addQuery('sys_created_on', '<=', end);
        gr.query();
        return gr.getRowCount();
    },

    _countByStatus: function(table, status, start, end) {
        var gr = new GlideRecord(table);
        gr.addQuery('u_status', status);
        gr.addQuery('sys_created_on', '>=', start);
        gr.addQuery('sys_created_on', '<=', end);
        gr.query();
        return gr.getRowCount();
    },

    _getOverdueCount: function() {
        var gr = new GlideRecord('u_library_issuance_records');
        gr.addQuery('u_status', 'Overdue');
        gr.query();
        return gr.getRowCount();
    },

    _getSLAPerformance: function(start, end) {
        var breached = 0;
        var total = 0;
        var gr = new GlideRecord('u_library_approvals');
        gr.addQuery('sys_created_on', '>=', start);
        gr.addQuery('sys_created_on', '<=', end);
        gr.query();
        while (gr.next()) {
            total++;
            if (gr.u_sla_breached == true || gr.u_escalation_date != '') {
                breached++;
            }
        }
        return {
            total: total,
            breached: breached,
            complianceRate: total > 0 ? Math.round(((total - breached) / total) * 100) : 100
        };
    },

    _getTopBorrowedBooks: function(start, end) {
        var bookCounts = {};
        var gr = new GlideRecord('u_library_issuance_records');
        gr.addQuery('sys_created_on', '>=', start);
        gr.addQuery('sys_created_on', '<=', end);
        gr.query();
        while (gr.next()) {
            var bookId = gr.getValue('u_book');
            bookCounts[bookId] = (bookCounts[bookId] || 0) + 1;
        }
        var sorted = Object.keys(bookCounts).sort(function(a, b) {
            return bookCounts[b] - bookCounts[a];
        });
        var top5 = sorted.slice(0, 5);
        return top5.map(function(id) {
            var book = new GlideRecord('u_library_books');
            book.get(id);
            return {
                title: book.getDisplayValue('u_title'),
                count: bookCounts[id]
            };
        });
    },

    _getCategoryDistribution: function() {
        var dist = {};
        var gr = new GlideRecord('u_library_books');
        gr.addNotNullQuery('u_category');
        gr.query();
        while (gr.next()) {
            var catId = gr.getValue('u_category');
            dist[catId] = (dist[catId] || 0) + 1;
        }
        var result = [];
        for (var catId in dist) {
            var cat = new GlideRecord('u_library_categories');
            cat.get(catId);
            result.push({
                category: cat.getDisplayValue('u_name'),
                count: dist[catId]
            });
        }
        return result;
    },

    _attachReport: function(pdfContent) {
        var gr = new GlideRecord('u_library_configuration');
        gr.addQuery('u_config_key', 'weekly_report_container');
        gr.query();
        var containerSysId = gr.next() ? gr.getUniqueValue() : '';

        var attachment = new GlideSysAttachment();
        var sysId = attachment.write(
            containerSysId,
            'Weekly_Report_' + gs.beginOfLastWeek().substring(0, 10) + '.pdf',
            'application/pdf',
            pdfContent
        );
        return sysId;
    },

    _emailReport: function(attachmentSysId, weekStart, weekEnd) {
        var mgrRole = new GlideRecord('sys_user_has_role');
        mgrRole.addQuery('role', 'snc_library_manager');
        mgrRole.query();

        var recipients = [];
        while (mgrRole.next()) {
            recipients.push(mgrRole.user.getRefRecord().getValue('email'));
        }

        var notificationMgr = new LibraryNotificationManager();
        notificationMgr.sendWeeklyReport(recipients, attachmentSysId);
    },

    type: 'LibraryWeeklyReportDelivery'
};
```

### Execution

```javascript
var job = new LibraryWeeklyReportDelivery();
job.run();
```

---

## Scheduled Job: Audit Log Cleanup

| Property | Value |
| --- | --- |
| **Name** | Library Audit Log Cleanup |
| **Schedule** | 1st day of every month at 02:00 |
| **Condition** | — |
| **Application** | `x_univ_library` |

### Description

Runs on the 1st of each month at 02:00 to archive and purge audit log records older than 90 days. Records are moved from `u_library_audit_log` to `u_library_audit_archive` (a dedicated archive table with identical structure). After successful archival, purged records are deleted from the primary table. Performance is ensured by processing in batches of 500 records. A summary of archived records (count, date range) is written to `u_library_audit_log` as an informational entry.

### Script

```javascript
var LibraryAuditLogCleanup = Class.create();
LibraryAuditLogCleanup.prototype = {
    initialize: function() {},

    BATCH_SIZE: 500,
    RETENTION_DAYS: 90,

    run: function() {
        var cutoffDate = gs.daysAgo(this.RETENTION_DAYS);
        var totalArchived = 0;
        var oldestDate = null;
        var newestDate = null;
        var batch = [];

        var gr = new GlideRecord('u_library_audit_log');
        gr.addQuery('u_change_date', '<', cutoffDate);
        gr.orderByAsc('u_change_date');
        gr.query();

        while (gr.next()) {
            batch.push(gr.getUniqueValue());
            oldestDate = oldestDate || gr.getValue('u_change_date');
            newestDate = gr.getValue('u_change_date');

            if (batch.length >= this.BATCH_SIZE) {
                totalArchived += this._archiveBatch(batch);
                batch = [];
            }
        }

        if (batch.length > 0) {
            totalArchived += this._archiveBatch(batch);
        }

        this._logCleanupSummary(totalArchived, oldestDate, newestDate);
    },

    _archiveBatch: function(sysIds) {
        var archived = 0;
        for (var i = 0; i < sysIds.length; i++) {
            var source = new GlideRecord('u_library_audit_log');
            if (source.get(sysIds[i])) {
                var archive = new GlideRecord('u_library_audit_archive');
                archive.initialize();
                this._copyFields(source, archive);
                var archiveId = archive.insert();

                if (archiveId) {
                    source.deleteRecord();
                    archived++;
                }
            }
        }
        return archived;
    },

    _copyFields: function(source, target) {
        target.u_table_name = source.u_table_name;
        target.u_record_sys_id = source.u_record_sys_id;
        target.u_field_name = source.u_field_name;
        target.u_old_value = source.u_old_value;
        target.u_new_value = source.u_new_value;
        target.u_action = source.u_action;
        target.u_changed_by = source.u_changed_by;
        target.u_change_date = source.u_change_date;
        target.u_ip_address = source.u_ip_address;
        target.u_session_id = source.u_session_id;
    },

    _logCleanupSummary: function(totalArchived, oldestDate, newestDate) {
        if (totalArchived === 0) {
            return;
        }

        var log = new GlideRecord('u_library_audit_log');
        log.initialize();
        log.u_table_name = 'SYSTEM';
        log.u_record_sys_id = 'AUDIT_CLEANUP';
        log.u_field_name = 'BATCH_ARCHIVE';
        log.u_old_value = '';
        log.u_new_value = JSON.stringify({
            archived: totalArchived,
            dateRange: oldestDate + ' to ' + newestDate,
            runDate: gs.nowDateTime()
        });
        log.u_action = 'Archive';
        log.u_changed_by = gs.getUserID();
        log.u_change_date = gs.nowDateTime();
        log.insert();
    },

    type: 'LibraryAuditLogCleanup'
};
```

### Execution

```javascript
var job = new LibraryAuditLogCleanup();
job.run();
```

---

## Scheduled Job Definitions Summary

| Job | Schedule | Last Run | Next Run | Status |
| --- | --- | --- | --- | --- |
| Library Daily Overdue Detection | Daily 06:00 | 2026-07-04 06:00 | 2026-07-05 06:00 | Active |
| Library Weekly Report Delivery | Monday 07:00 | 2026-06-29 07:00 | 2026-07-06 07:00 | Active |
| Library Audit Log Cleanup | 1st of Month 02:00 | 2026-07-01 02:00 | 2026-08-01 02:00 | Active |

All three jobs are defined in the `sysauto` table with the `x_univ_library` application scope and execute under the system scheduler account.
