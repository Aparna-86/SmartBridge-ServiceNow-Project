# Maintenance Guide

# Smart Library Request Workflow — ServiceNow Enterprise Solution

> **Document Type:** Maintenance Guide  
> **Version:** 2.0.0  
> **Application Scope:** `x_univ_library`  
> **Status:** Final — Complete  
> **Platform:** ServiceNow Washington DC

---

## 1. Maintenance Schedule Overview

| Frequency | Tasks | Responsible |
| ----------- | ------- | ------------- |
| Daily | Operations checklist | Library IT Support |
| Weekly | Report delivery review, overdue escalation check | Library Manager |
| Monthly | Audit log review, PA data validation | Library Admin |
| Quarterly | User role audit, data retention cleanup | Library Admin + IT |
| Annually | FERPA compliance review, full system audit | IT Security + Library Admin |

---

## 2. Daily Operations Checklist

### 2.1 Morning Check

| Time | Task | Tool/Location | Expected State |
| ------ | ------ | --------------- | ---------------- |
| 8:00 AM | Verify scheduled jobs ran overnight | **Scheduled Jobs** → **Execution History** | All jobs marked Success |
| 8:15 AM | Check overdue detection results | `u_library_borrow_requests` → filter: status = overdue | Records marked as expected |
| 8:30 AM | Verify portal is accessible | Navigate to `/library` in browser | Page loads without errors |
| 8:45 AM | Check notification logs for failures | `u_library_notification_log` → filter: status = failed | Zero failed notifications |

### 2.2 End-of-Day Check

| Time | Task | Tool/Location | Expected State |
| ------ | ------ | --------------- | ---------------- |
| 4:30 PM | Review new borrow requests | `u_library_borrow_requests` → filter: status = pending_approval | Requests visible, unblocked |
| 4:45 PM | Check for stuck Flow Designer flows | **Flow Designer** → **Flow Results** → filter: status = Error | Zero error flows |
| 5:00 PM | Verify system performance | **System Diagnostics** → **Status** | Normal performance metrics |

### 2.3 Automated Alerts

Configure the following alerts to notify the library IT team:

| Alert Condition | Notification Channel | Recipient |
| ----------------- | --------------------- | ----------- |
| Scheduled job fails 3 consecutive times | Email + Platform notification | Library Admin |
| Flow Designer flow errors > 5 in an hour | Email | Library Admin |
| Portal 404 errors > 10 in an hour | Email | Library Admin |
| Notification failure rate > 5% | Email | Library Admin |

---

## 3. Weekly Tasks

### 3.1 Report Delivery Review

| Task | Details |
| ------ | --------- |
| **When** | Monday at 9:00 AM |
| **Duration** | 30 minutes |
| **Steps** | 1. Navigate to **Reports** → **Scheduled Reports**. 2. Check execution history for the previous week. 3. Verify all scheduled reports were delivered successfully. 4. Resend any failed reports manually. 5. Check report data accuracy by comparing with table data. |

### 3.2 Overdue Escalation Check

| Task | Details |
| ------ | --------- |
| **When** | Monday at 9:30 AM |
| **Duration** | 30 minutes |
| **Steps** | 1. Navigate to `u_library_borrow_requests` → filter: status = overdue. 2. Review the list of overdue books. 3. Verify overdue reminders were sent (check notification log). 4. Escalate any overdue > 14 days to Library Manager for follow-up. 5. Manually trigger reminders for any overdue records that did not auto-notify. |

### 3.3 Pending Approvals Review

| Task | Details |
| ------ | --------- |
| **When** | Wednesday at 10:00 AM |
| **Duration** | 15 minutes |
| **Steps** | 1. Navigate to `u_library_borrow_requests` → filter: status = pending_approval. 2. Review requests pending for more than 24 hours. 3. Contact the assigned librarian group about aged approvals. 4. Verify the escalation SLA timer is running correctly. |

---

## 4. Monthly Tasks

### 4.1 Audit Log Review

| Task | Details |
| ------ | --------- |
| **When** | 1st of month at 10:00 AM |
| **Duration** | 1 hour |
| **Steps** | 1. Navigate to `u_library_audit_log` table. 2. Review audit log entries for the previous month. 3. Investigate any `access_denied` events for potential security issues. 4. Check for unusual patterns (e.g., bulk deletes, after-hours access). 5. Generate the Audit Log Summary report (RPT-007) and file for compliance. 6. Archive a copy of the audit log report. |

**Audit Log Retention:** Records are retained for 3 years. The `[LIB] Audit Log Cleanup` scheduled job runs on the 1st of each month and automatically purges records older than 1095 days. Verify this job executed successfully each month.

### 4.2 Performance Analytics Data Validation

| Task | Details |
| ------ | --------- |
| **When** | 1st of month at 11:00 AM |
| **Duration** | 30 minutes |
| **Steps** | 1. Navigate to **Performance Analytics** → **Indicators**. 2. Open each library indicator. 3. Verify data collection completed for the previous month. 4. Check indicator values against raw table data for accuracy. 5. Run data collection manually for any indicators that did not auto-collect. 6. Refresh the dashboards and verify widgets display current data. |

### 4.3 Configuration Parameter Audit

| Task | Details |
| ------ | --------- |
| **When** | 1st of month at 11:30 AM |
| **Duration** | 30 minutes |
| **Steps** | 1. Navigate to `u_library_configuration` table. 2. Review all 8 parameter records. 3. Verify values are still correct. 4. Check the `updated_at` and `updated_by` fields for unauthorized changes. 5. Update parameter values if business requirements have changed. |

### 4.4 Notification Log Review

| Task | Details |
| ------ | --------- |
| **When** | 2nd week of month |
| **Duration** | 30 minutes |
| **Steps** | 1. Navigate to `u_library_notification_log` table. 2. Generate the Notification Log Summary report (RPT-006). 3. Review delivery success rates. 4. Investigate any recurring delivery failures. 5. Check email mailbox configuration if delivery failure rate exceeds 5%. |

---

## 5. Quarterly Tasks

### 5.1 User Role Audit

| Task | Details |
| ------ | --------- |
| **When** | First week of quarter |
| **Duration** | 2 hours |
| **Steps** | 1. Navigate to **User Administration** → **Roles**. 2. Export the list of users with each library role. 3. For `student_library` role: Verify all users are currently enrolled students. Deactivate profiles for graduated/withdrawn students. 4. For `librarian_library` role: Verify all users are current library staff. 5. For `library_manager` role: Verify only authorized managers have the role. 6. For `library_admin` role: Verify only IT administrators have the role. 7. Remove any unauthorized role assignments. 8. Document audit results and file for compliance. |

**Deactivating Student Profiles:**

```bash
# Update query for inactive students
u_library_students table: Set u_active = false for graduated students
u_library_borrow_requests: Verify no active borrows before deactivation
```

### 5.2 Data Retention Cleanup

| Task | Details |
| ------ | --------- |
| **When** | Second week of quarter |
| **Duration** | 1 hour |
| **Steps** | 1. Verify the `[LIB] Audit Log Cleanup` job is running correctly. 2. Check `u_library_notification_log` for old records. Clean up if retention policy applies. 3. Archive old borrow request records (status = returned, date older than 1 year) to a separate instance or export. 4. Review system logs for disk space warnings. 5. Purge temporary data (import set staging tables) if applicable. |

### 5.3 SLA Performance Review

| Task | Details |
| ------ | --------- |
| **When** | Third week of quarter |
| **Duration** | 1 hour |
| **Steps** | 1. Navigate to **SLA** → **SLA Definitions**. 2. Generate the Approval Processing Time report (RPT-008). 3. Review SLA breach statistics for the quarter. 4. Identify requests that breached SLA and investigate root causes. 5. Adjust SLA duration or business hours schedule if needed. 6. Document SLA performance metrics for reporting. |

### 5.4 Portal Performance Check

| Task | Details |
| ------ | --------- |
| **When** | Fourth week of quarter |
| **Duration** | 1 hour |
| **Steps** | 1. Navigate to each portal page and verify load times are acceptable (< 3 seconds). 2. Check widget server-side logs for performance warnings. 3. Review portal usage statistics in **Service Portal** → **Analytics**. 4. Optimize any widgets with slow queries (add indexes, reduce data payload). 5. Test portal on multiple browsers (Chrome, Firefox, Edge). |

---

## 6. Annual Tasks

### 6.1 FERPA Compliance Review

| Task | Details |
| ------ | --------- |
| **When** | January (before spring semester) |
| **Duration** | 4 hours |
| **Steps** | 1. Review all ACL configurations for student data protection. 2. Verify that student profile data is only accessible to authorized roles. 3. Check `u_library_audit_log` for any unauthorized access attempts. 4. Review the `student_library` role assignments. 5. Verify that no student can access another student's borrow history or personal information. 6. Verify that audit log records are immutable and cannot be deleted. 7. Document findings and submit compliance report to IT Security. |

### 6.2 Full System Audit

| Task | Details |
| ------ | --------- |
| **When** | January (after FERPA review) |
| **Duration** | 8 hours |
| **Steps** | 1. **Application Audit:** Verify all artifacts (tables, scripts, flows, etc.) match the documented design. 2. **Data Integrity Check:** Run data validation queries to verify referential integrity. 3. **Performance Baseline:** Run performance tests and compare with baseline from initial deployment. 4. **Security Audit:** Review all role assignments, ACL configurations, and access logs. 5. **Backup Verification:** Test the full backup and restore procedure. 6. **Documentation Review:** Update all documentation to reflect any changes made during the year. 7. **Vulnerability Scan:** Run ServiceNow instance security scan if available. 8. **Remediation Plan:** Create an action plan for any issues found during the audit. |

### 6.3 Disaster Recovery Drill

| Task | Details |
| ------ | --------- |
| **When** | July (before fall semester) |
| **Duration** | 4 hours |
| **Steps** | 1. Simulate a full instance failure scenario. 2. Restore the application from the latest backup to a test environment. 3. Verify all tables, scripts, flows, and portal pages are functional. 4. Test data integrity after restoration. 5. Measure recovery time and compare with RTO targets. 6. Document drill results and update the disaster recovery plan. |

---

## 7. Backup and Recovery Procedures

### 7.1 Backup Schedule

| Backup Type | Frequency | Retention | Method |
| ------------ | ----------- | ----------- | -------- |
| Full instance backup | Weekly | 4 weeks | ServiceNow automatic backup |
| Daily incremental | Daily | 7 days | ServiceNow automatic backup |
| Update set export | On change | Indefinite | Manual export from Studio |
| Configuration export | Monthly | 1 year | Manual export |
| Report data export | Quarterly | 3 years | Manual CSV export |

### 7.2 Backup Verification

On a monthly basis:

1. Navigate to **System Diagnostics** → **Backup & Restore**.
2. Verify the most recent backup completed successfully.
3. Check backup file size is consistent with previous backups.
4. Log a support ticket with ServiceNow if backups are failing.

### 7.3 Recovery Procedure

**Full Instance Restore:**

1. Contact ServiceNow support to initiate a full instance restore.
2. Provide the target date/time for restoration.
3. Verify the backup exists for the requested date.
4. Schedule the restore during a maintenance window.
5. After restore completes, verify:
   - All library tables contain the expected data
   - All scripts are present and functional
   - Portal pages load correctly
   - Scheduled jobs are active

**Selective Recovery (Update Set):**

For recovering specific application artifacts without a full restore:

1. Export the current production update set.
2. Create a new update set in the target environment.
3. Manually recreate or import the affected artifacts.
4. Test the recovered artifacts before putting into production.

### 7.4 Disaster Recovery Contacts

| Role | Contact | Responsibility |
| ------ | --------- | ---------------- |
| ServiceNow Admin | Instance Administrator | Coordinate with ServiceNow support |
| IT Security | Security Officer | Security verification after recovery |
| Library Manager | Business Owner | Verify application functionality |
| IT Operations | Infrastructure Lead | Network and access verification |

---

## 8. Performance Monitoring

### 8.1 Key Performance Metrics

| Metric | Warning Threshold | Critical Threshold | Check Frequency |
| -------- | ------------------ | ------------------- | ----------------- |
| Portal page load time | > 3 seconds | > 5 seconds | Weekly |
| Flow Designer flow execution time | > 10 seconds | > 30 seconds | Daily |
| Scheduled job execution time | > 5 minutes | > 15 minutes | Daily |
| Report generation time | > 30 seconds | > 60 seconds | Weekly |
| API response time | > 2 seconds | > 5 seconds | Monthly |
| ACL evaluation time | > 500ms | > 1 second | Monthly |

### 8.2 Performance Optimization

If performance issues are detected:

1. **Identify slow queries:** Use **System Diagnostics** → **Transaction Logs** to identify slow database queries.
2. **Add indexes:** Use **System Definition** → **Tables** → **Indexes** to add indexes on frequently queried fields.
3. **Optimize GlideRecord queries:** Add `addQuery()` filters, limit returned fields, use `setLimit()`.
4. **Cache frequently accessed data:** Use GlideAggregate or configuration cache for lookup data.
5. **Archive old data:** Move historical records to a separate archival table.

---

*References: [DeploymentGuide.md](DeploymentGuide.md) | [Troubleshooting.md](Troubleshooting.md)*
*See also: [AdministratorGuide.md](../guides/AdministratorGuide.md)*
