# Administrator Guide

# Smart Library Request Workflow — ServiceNow Enterprise Solution

> **Document Type:** Administrator Guide  
> **Version:** 2.0.0  
> **Application Scope:** `x_univ_library`  
> **Audience:** System Administrators, Library Administrators  
> **Platform:** ServiceNow Washington DC

---

## 1. Configuration Management

### 1.1 Configuration Table

All library system parameters are stored in the `u_library_configuration` table.

| Parameter Key | Value | Data Type | Description |
| --------------- | ------- | ----------- | ------------- |
| `default_loan_period_days` | `14` | Integer | Default book loan period in days |
| `max_borrow_limit_default` | `5` | Integer | Default maximum concurrent borrows per student |
| `approval_escalation_hours` | `48` | Integer | Hours before approval escalates to Library Manager |
| `overdue_reminder_days` | `1,3,7,14` | List | Comma-separated overdue reminder day thresholds |
| `notifications_enabled` | `true` | Boolean | Master toggle for all notifications |
| `email_notifications_enabled` | `true` | Boolean | Toggle for email delivery channel |
| `portal_search_enabled` | `true` | Boolean | Toggle for full-text portal search |
| `damage_report_notification_enabled` | `true` | Boolean | Toggle for damage report notifications |

### 1.2 Modifying Configuration

1. Navigate to **All** → **Library Configuration** module.
2. Find the parameter record by the `u_parameter_key` value.
3. Update the `u_parameter_value` field.
4. Click **Update**.

> **Important:** When modifying configuration values, use the correct data type:
>
> - **Integer:** Whole numbers only (e.g., `14`, not `14.0`)
> - **Boolean:** `true` or `false` (lowercase)
> - **List:** Comma-separated values (e.g., `1,3,7,14`)
> - **String:** Any text value

### 1.3 Adding a New Configuration Parameter

1. Navigate to **Library Configuration** module.
2. Click **New**.
3. Enter:
   - **Parameter Key:** Unique key name (lowercase, underscore-separated)
   - **Parameter Value:** Initial value
   - **Data Type:** Select the appropriate type
   - **Description:** Clear documentation of what this parameter controls
4. Click **Submit**.

> **Note:** Adding a new configuration parameter does not automatically integrate it with application logic. The parameter must be referenced in the relevant Script Include or Business Rule.

---

## 2. User and Role Management

### 2.1 Role Definitions

| Role | System Name | Description |
| ------ | ------------- | ------------- |
| Student | `x_univ_library.student_library` | University student with registered library profile |
| Librarian | `x_univ_library.librarian_library` | Library staff managing daily operations |
| Library Manager | `x_univ_library.library_manager` | Senior staff overseeing all operations |
| Administrator | `x_univ_library.library_admin` | IT/system administrator with full access |

### 2.2 Assigning Roles to Users

1. Navigate to **User Administration** → **Users**.
2. Search for the user by name or username.
3. Open the user record.
4. Click the **Roles** tab.
5. Click **Edit**.
6. Add the appropriate library role(s).
7. Click **Save**.

### 2.3 Creating a Student Profile

When a student is assigned the `x_univ_library.student_library` role, a corresponding profile in `u_library_students` is created automatically. The profile includes:

- **User:** Reference to the sys_user record
- **University ID:** Must be manually entered (not auto-generated)
- **Max Borrow Limit:** Defaults to the value in `max_borrow_limit_default` configuration
- **Active Borrow Count:** Defaults to 0

If the profile is not created automatically:

1. Navigate to **Library Students** module.
2. Click **New**.
3. Select the user.
4. Enter the **University ID**.
5. Set the **Max Borrow Limit**.
6. Click **Submit**.

### 2.4 Creating a Librarian Profile

1. Navigate to **Library Librarians** module.
2. Click **New**.
3. Select the user.
4. Enter the **Staff ID**.
5. Optionally assign **Department Assignments** (categories).
6. Click **Submit**.

### 2.5 Deactivating Users

When a student graduates or a librarian leaves:

1. Open the library profile record (student or librarian).
2. Set **Active** to `false`.
3. Save the record.
4. Optionally remove the role assignment from the user record.

> **Note:** Deactivating the profile does not affect active borrows. Ensure all books are returned before deactivation.

### 2.6 Bulk Role Management

For bulk role assignments:

1. Navigate to **User Administration** → **Roles**.
2. Click the role name (e.g., `x_univ_library.student_library`).
3. Click the **Users** tab.
4. Click **Edit** to add or remove multiple users at once.

---

## 3. System Monitoring

### 3.1 Scheduled Jobs

| Job Name | Schedule | Purpose | Status Monitoring |
| ---------- | ---------- | --------- | ------------------- |
| `[LIB] Overdue Detection` | Daily at 2:00 AM | Detect overdue books | **Scheduled Jobs** → **Execution History** |
| `[LIB] Overdue Notification — Day 1` | Daily at 6:00 AM | Send first overdue reminder | **Scheduled Jobs** → **Execution History** |
| `[LIB] Overdue Notification — Day 3` | Daily at 6:00 AM | Send 3-day overdue reminder | **Scheduled Jobs** → **Execution History** |
| `[LIB] Overdue Notification — Day 7` | Daily at 6:00 AM | Send 7-day overdue reminder | **Scheduled Jobs** → **Execution History** |
| `[LIB] Overdue Notification — Day 14` | Daily at 6:00 AM | Send 14-day overdue reminder | **Scheduled Jobs** → **Execution History** |
| `[LIB] Report Delivery` | Per report schedule | Trigger report generation | **Reports** → **Scheduled Reports** |
| `[LIB] Audit Log Cleanup` | Monthly on 1st at 1:00 AM | Purge audit logs > 3 years | **Scheduled Jobs** → **Execution History** |
| `[LIB] Configuration Cache Refresh` | Weekly on Monday at 5:00 AM | Refresh configuration cache | **Scheduled Jobs** → **Execution History** |

### 3.2 Monitoring Job Executions

1. Navigate to **System Definition** → **Scheduled Jobs**.
2. Open the job record.
3. Click the **Execution History** related list.
4. Review the status, start time, end time, and output of each execution.

### 3.3 Troubleshooting Failed Jobs

If a scheduled job fails:

1. Check the **Execution History** for error details.
2. Review the **System Log** for error messages related to the job.
3. Common issues:
   - **Timeout:** Job exceeded the maximum execution time. Optimize the script.
   - **Data error:** A data record caused an unhandled exception. Fix the data or add error handling.
   - **Cross-scope access:** The job tried to access a record outside its scope. Check scope rules.

### 3.4 Notification Logs

1. Navigate to **Library Notification Log** table.
2. View all sent notifications with:
   - Recipient name
   - Event type
   - Template ID
   - Sent timestamp
   - Delivery status (Sent/Failed)
   - Retry count

### 3.5 Monitoring Notification Delivery

1. Navigate to **System Notification** → **Email** → **Email Logs**.
2. Filter by sender or recipient.
3. Review email delivery status and error messages.

### 3.6 Audit Logs

1. Navigate to **Library Audit Log** table.
2. View all audited events with:
   - Table name
   - Record Sys ID
   - User who performed the action
   - Action type (Created/Updated/Deleted/Access Denied)
   - Field name and old/new values (for updates)
   - Timestamp

### 3.7 Audit Log Retention

- Audit logs are retained for **3 years** (1095 days).
- The `[LIB] Audit Log Cleanup` scheduled job runs monthly to purge records older than the retention period.
- Audit log records are **immutable** — they cannot be modified or deleted by any user, including administrators.
- To extend retention, modify the job script parameter or adjust the retention period in the `LibraryAuditLogger` Script Include.

---

## 4. Report Scheduling

### 4.1 Viewing Scheduled Reports

1. Navigate to **Reports** → **Scheduled Reports**.
2. View the list of all scheduled reports.
3. Click a report to view its schedule, recipients, and last run time.

### 4.2 Report Inventory

| Report ID | Name | Schedule | Recipients | Format |
| ----------- | ------ | ---------- | ------------ | -------- |
| RPT-001 | Books Borrowed per Month | 1st of month at 8:00 AM | Library Manager | PDF |
| RPT-002 | Most Borrowed Books | On demand | — | — |
| RPT-003 | Overdue Books | Daily at 7:00 AM | Librarian Group | CSV |
| RPT-004 | Student Activity | Weekly on Monday at 8:00 AM | Library Manager | PDF |
| RPT-005 | Inventory Status | Quarterly on 1st at 9:00 AM | Library Manager, Admin | PDF |
| RPT-006 | Notification Log Summary | Weekly on Sunday at 10:00 PM | Admin | CSV |
| RPT-007 | Audit Log Summary | Monthly on 1st at 11:00 PM | Admin | CSV |
| RPT-008 | Approval Processing Time | On demand | — | — |

### 4.3 Modifying Report Schedule

1. Open the report in the report designer.
2. Click **Schedule**.
3. Modify the frequency, time, recipients, or format.
4. Click **Save**.

### 4.4 Adding New Recipients

1. Open the scheduled report.
2. In the **Recipients** field, add email addresses (comma-separated) or select groups/users.
3. Click **Save**.

### 4.5 Manually Running a Report

1. Open the report.
2. Click **Run** to generate the report immediately.
3. Click **Export** to download in the desired format (PDF, CSV, XLSX).

---

## 5. SLA Configuration

### 5.1 SLA Definitions

| SLA Name | Table | Condition | Duration | Breach Action |
| ---------- | ------- | ----------- | ---------- | --------------- |
| `[LIB] Approval SLA — 48 Hours` | u_library_borrow_requests | Status = Pending Approval | 48 hours (business hours) | Escalate to Library Manager |
| `[LIB] Manager Escalation SLA — 72 Hours` | u_library_borrow_requests | Status = Escalated | 72 hours (business hours) | Create admin follow-up task |
| `[LIB] Book Loan Period` | u_library_issuance_records | Always (on record creation) | 14 calendar days | Set overdue flag |

### 5.2 Modifying SLA Duration

1. Navigate to **SLA** → **SLA Definitions**.
2. Open the SLA definition.
3. Modify the **Duration** field.
4. Click **Update**.

### 5.3 Business Hours Schedule

The approval SLAs use the university library business hours:

| Day | Hours |
| ----- | ------- |
| Monday – Friday | 8:00 AM – 8:00 PM |
| Saturday | 10:00 AM – 6:00 PM |
| Sunday | Closed (non-business hours) |
| Holidays | Closed (configured in holiday schedule) |

To modify business hours:

1. Navigate to **SLA** → **Schedules**.
2. Open the library business hours schedule.
3. Modify the time slots or add exceptions for holidays.
4. Click **Save**.

### 5.4 Monitoring SLA Timers

1. Navigate to **SLA** → **SLA Timers**.
2. Filter by the `u_library_borrow_requests` or `u_library_issuance_records` table.
3. View timer status (Running/Paused/Completed/Breached).
4. Click on a timer to view its detailed timeline.

### 5.5 Manually Stopping an SLA Timer

In rare cases where an SLA timer needs to be stopped manually:

1. Open the SLA timer record.
2. Click **Stop Timer**.
3. Enter a reason for stopping.
4. Confirm.

---

## 6. Performance Analytics Management

### 6.1 PA Indicators

| Indicator | Table | Metric | Aggregate | Schedule |
| ----------- | ------- | -------- | ----------- | ---------- |
| Active Borrow Requests | u_library_borrow_requests | Count where status = issued | Count | Daily |
| Overdue Books Count | u_library_borrow_requests | Count where status = overdue | Count | Daily |
| Pending Approvals | u_library_borrow_requests | Count where status = pending_approval | Count | Daily |
| Books Returned On Time | u_library_return_records | Count where condition = on_time | Count | Daily |
| Books Returned Overdue | u_library_return_records | Count where condition = overdue | Count | Daily |
| Average Approval Time | u_library_approvals | Average time creation to decision | Average | Daily |

### 6.2 Running Manual Data Collection

1. Navigate to **Performance Analytics** → **Indicators**.
2. Open the indicator.
3. Click **Collect Data Now**.
4. Verify the job completes successfully in the job history.

### 6.3 Viewing Indicator Data

1. Navigate to **Performance Analytics** → **Indicator Data**.
2. Filter by indicator name.
3. View the collected data points in the table.

### 6.4 Dashboard Management

**Library Operations Dashboard:**

Navigate to **Dashboards** → **Library Operations Dashboard** to view:

- Active requests count
- Overdue books count
- Available books count
- Pending approvals count
- Books borrowed by category chart
- Daily borrow volume chart
- Recent activity feed

**Student Self-Service Dashboard:**

Viewable through the student portal at `/library/my-requests`:

- My active requests
- My borrowing history
- Currently issued books
- My profile summary

### 6.5 Modifying Dashboard Widgets

1. Navigate to **Dashboards** → open the dashboard.
2. Click **Configure** (pencil icon) on the widget.
3. Modify the data source, visualization type, or filters.
4. Click **Save**.

---

## 7. Data Management

### 7.1 Importing Data

Use Import Sets for bulk data import:

1. Navigate to **System Import Sets** → **Import Sets** → **New**.
2. Upload a CSV file.
3. Map columns to target table fields.
4. Run the import transform.
5. Verify records in the target table.

### 7.2 Exporting Data

1. Navigate to the target table list view.
2. Right-click the column header → **Export** → select format (CSV, Excel, PDF).
3. Alternatively, use a report with scheduled delivery.

### 7.3 Data Archival

For long-term data archival:

1. Navigate to the table.
2. Filter records to archive (e.g., returned requests older than 1 year).
3. Click **Export to CSV**.
4. Store the exported file in a secure location.
5. Delete the records from the live table if necessary.

---

## 8. System Properties

The following system properties are relevant to the library application:

| Property | Value | Description |
| ---------- | ------- | ------------- |
| `glide.security.acl.debug` | `false` | Set to `true` temporarily for ACL debugging |
| `glide.email.debug` | `false` | Set to `true` temporarily for email debugging |
| `com.glide.workflow.debug` | `false` | Set to `true` temporarily for flow debugging |

> **Warning:** Debug properties should only be enabled temporarily in development or testing environments. Always disable them after debugging.

---

*References: [UserManual.md](UserManual.md) | [DeveloperGuide.md](DeveloperGuide.md)*
*See also: [MaintenanceGuide.md](../implementation/MaintenanceGuide.md) | [Troubleshooting.md](../implementation/Troubleshooting.md)*
