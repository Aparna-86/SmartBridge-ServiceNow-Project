# Technical Blueprint — Smart Library Request Workflow

**Application Scope:** `x_univ_library`  
**Platform:** ServiceNow Washington DC  
**Status:** Deployed and operational  
**Last Updated:** June 2026

---

## 1. Naming Conventions

| Element | Convention | Example |
| --- | --- | --- |
| Tables | `u_library_<plural_name>` | `u_library_books` |
| Fields | `u_<descriptive_name>` | `u_isbn`, `u_borrow_date` |
| Script Includes | `Library<Purpose>` | `LibraryBookManager` |
| Business Rules | `Library <Table> <Event> <Action>` | `Library Books Before Insert Set Status` |
| Client Scripts | `Library <Table> <Type> <Purpose>` | `Library Borrow Requests onChange Show SLA` |
| UI Policies | `Library <Table> <Field> <Behavior>` | `Library Borrow Requests Status Read Only` |
| UI Actions | `Library <Table> <Action>` | `Library Books Create Book` |
| Flows | `Library <Workflow>` | `Library Borrow Request Process` |
| Notifications | `Library <Recipient> <Event>` | `Library Student Request Submitted` |
| Roles | `snc_library_<role>` | `snc_library_librarian` |
| ACLs | `<operation>_u_library_<table>` | `read_u_library_books` |

## 2. Application Tables (11)

| # | Table Name | Label | Extends | Primary Role |
| --- | --- | --- | --- | --- |
| 1 | `u_library_books` | Books | Task | Book inventory |
| 2 | `u_library_categories` | Categories | — | Book category taxonomy |
| 3 | `u_library_students` | Students | User | Student profile |
| 4 | `u_library_librarians` | Librarians | User | Librarian profile |
| 5 | `u_library_borrow_requests` | Borrow Requests | Task | Borrow request lifecycle |
| 6 | `u_library_approvals` | Approvals | Task | Approval chain records |
| 7 | `u_library_issuance_records` | Issuance Records | — | Book checkout records |
| 8 | `u_library_return_records` | Return Records | — | Book return records |
| 9 | `u_library_notification_log` | Notification Log | — | Notification audit |
| 10 | `u_library_configuration` | Configuration | — | System settings |
| 11 | `u_library_audit_log` | Audit Log | — | Change tracking |

## 3. Script Includes (5)

| # | Name | Description |
| --- | --- | --- |
| 1 | `LibraryBookManager` | CRUD operations for books, availability checks, category assignment |
| 2 | `LibraryBorrowRequestManager` | Borrow request lifecycle (submit, approve, issue, return, cancel) |
| 3 | `LibraryApprovalManager` | Approval routing, escalation logic, SLA calculations |
| 4 | `LibraryNotificationManager` | Notification dispatch via email, in-app banner, and SMS |
| 5 | `LibraryReportEngine` | Report generation (aggregation, CSV/PDF export, dashboard data) |

## 4. Business Rules (15)

| # | Name | Table | When | Order | Description |
| --- | --- | --- | --- | --- | --- |
| 1 | Library Books Before Insert Set Status | `u_library_books` | Before Insert | 100 | Sets `u_status = 'Available'` and generates `u_book_id` |
| 2 | Library Books Before Update Check Availability | `u_library_books` | Before Update | 200 | Prevents marking Available if copies are issued |
| 3 | Library Categories Before Delete Protect | `u_library_categories` | Before Delete | 100 | Blocks deletion if books reference category |
| 4 | Library Students Before Insert Set Defaults | `u_library_students` | Before Insert | 100 | Sets `u_max_borrow = 5`, `u_borrow_count = 0` |
| 5 | Library Borrow Requests Before Insert Validate | `u_library_borrow_requests` | Before Insert | 100 | Validates student eligibility (max borrow, fines) |
| 6 | Library Borrow Requests After Insert Notify | `u_library_borrow_requests` | After Insert | 100 | Triggers notification to student and librarian |
| 7 | Library Borrow Requests Before Write Set Timestamp | `u_library_borrow_requests` | Before Write | 50 | Updates `u_last_action_at` on any change |
| 8 | Library Approvals After Insert Route | `u_library_approvals` | After Insert | 100 | Routes approval to next group based on amount |
| 9 | Library Approvals Before Update Check Decision | `u_library_approvals` | Before Update | 100 | Validates approver, enforces `u_decision_date` |
| 10 | Library Issuance Records After Insert Update Book | `u_library_issuance_records` | After Insert | 100 | Decrements available copies, updates book status |
| 11 | Library Return Records After Insert Update Status | `u_library_return_records` | After Insert | 100 | Increments available copies, closes borrow request |
| 12 | Library Notification Log Before Insert Set Priority | `u_library_notification_log` | Before Insert | 100 | Assigns priority (High/Medium/Low) based on category |
| 13 | Library Configuration Before Update Validate | `u_library_configuration` | Before Update | 100 | Validates configuration key uniqueness and value types |
| 14 | Library Audit Log After Insert Archive | `u_library_audit_log` | After Insert | 200 | Archives records older than 90 days to `u_library_audit_archive` |
| 15 | Library Books After Update Sync External | `u_library_books` | After Update | 300 | Pushes book status changes to external catalog webhook |

## 5. Client Scripts (12)

| # | Name | Table | Type | Description |
| --- | --- | --- | --- | --- |
| 1 | Library Books onChange Category Show Fields | `u_library_books` | onChange | Shows/hides fields based on selected category |
| 2 | Library Books onSubmit Validate ISBN | `u_library_books` | onSubmit | Validates ISBN-10/13 format before submit |
| 3 | Library Books onLoad Set Default Due Date | `u_library_books` | onLoad | Pre-populates `u_due_days` from configuration |
| 4 | Library Borrow Requests onChange Status Show SLA | `u_library_borrow_requests` | onChange | Displays SLA timer when status is Pending Approval |
| 5 | Library Borrow Requests onLoad Check Eligibility | `u_library_borrow_requests` | onLoad | Calls `LibraryBorrowRequestManager` to validate student |
| 6 | Library Borrow Requests onSubmit Prevent Duplicate | `u_library_borrow_requests` | onSubmit | Blocks submit if duplicate active request exists |
| 7 | Library Approvals onChange Decision Hide Fields | `u_library_approvals` | onChange | Hides approval group field after decision is made |
| 8 | Library Approvals onLoad Set Approver | `u_library_approvals` | onLoad | Pre-populates approver from user session |
| 9 | Library Students onChange Max Borrow Warn | `u_library_students` | onChange | Warns if `u_max_borrow` exceeds system limit |
| 10 | Library Return Records onLoad Prefill Issuance | `u_library_return_records` | onLoad | Auto-fills fields from related issuance record |
| 11 | Library Issuance Records onSubmit Validate Date | `u_library_issuance_records` | onSubmit | Ensures due date is after issue date |
| 12 | Library Notification Log onLoad Mark Read | `u_library_notification_log` | onLoad | Marks notification as read when opened |

## 6. UI Policies (8)

| # | Name | Table | Field | Action |
| --- | --- | --- | --- | --- |
| 1 | Library Books Status Read Only | `u_library_books` | `u_status` | Read-only when status is Issued or Lost |
| 2 | Library Books ISBN Mandatory | `u_library_books` | `u_isbn` | Mandatory when category is Textbook |
| 3 | Library Borrow Requests Status Read Only | `u_library_borrow_requests` | `u_status` | Read-only after submission |
| 4 | Library Borrow Requests Student Read Only | `u_library_borrow_requests` | `u_student` | Read-only after record is saved |
| 5 | Library Approvals Decision Mandatory | `u_library_approvals` | `u_decision` | Mandatory when assigned to current user |
| 6 | Library Approvals Comments Mandatory | `u_library_approvals` | `u_comments` | Mandatory when decision is Rejected |
| 7 | Library Students Email Mandatory | `u_library_students` | `u_email` | Mandatory on insert |
| 8 | Library Issuance Records Due Date Mandatory | `u_library_issuance_records` | `u_due_date` | Mandatory on submit |

## 7. UI Actions (10)

| # | Name | Table | Type | Condition | Role |
| --- | --- | --- | --- | --- | --- |
| 1 | Create Book | `u_library_books` | List | `g_user.hasRole('snc_library_librarian')` | snc_library_librarian |
| 2 | Deactivate Book | `u_library_books` | Form | `current.u_status != 'Inactive'` | snc_library_librarian |
| 3 | Activate Book | `u_library_books` | Form | `current.u_status == 'Inactive'` | snc_library_librarian |
| 4 | Cancel Request | `u_library_borrow_requests` | Form | `current.u_status == 'Pending'` | snc_library_student |
| 5 | Process Return | `u_library_issuance_records` | List | `current.u_status == 'Issued'` | snc_library_librarian |
| 6 | Issue Book | `u_library_borrow_requests` | Form | `current.u_status == 'Approved'` | snc_library_librarian |
| 7 | Escalate Approval | `u_library_approvals` | Form | `current.u_approval_group == 'librarian_library'` | snc_library_manager |
| 8 | Override Decision | `u_library_approvals` | Form | `current.u_decision != ''` | snc_library_admin |
| 9 | Export Report | `u_library_books` | List | `g_user.hasRole('snc_library_librarian')` | snc_library_librarian |
| 10 | Refresh Dashboard | — | List | `g_user.hasRole('snc_library_librarian')` | snc_library_librarian |

See [UIActions.md](./UIActions.md) for detailed action scripts.

## 8. Flow Designer Flows (3)

| # | Name | Trigger | Description |
| --- | --- | --- | --- |
| 1 | Library Borrow Request Process | Record Created: `u_library_borrow_requests` | Routes request through approval chain, triggers SLA, dispatches notifications |
| 2 | Library Overdue Book Reminder | Scheduled (Daily 08:00) | Queries overdue issuance records, sends reminder to student and librarian |
| 3 | Library Weekly Statistics Report | Scheduled (Monday 07:00) | Aggregates weekly metrics, generates PDF report, emails to library_manager |

## 9. Notifications (13)

| # | Name | When | Recipient | Medium |
| --- | --- | --- | --- | --- |
| 1 | Library Student Request Submitted | Borrow Request created | Student | Email, In-app |
| 2 | Library Librarian New Request | Borrow Request created | `snc_library_librarian` group | Email, In-app |
| 3 | Library Request Approved | Borrow Request approved | Student | Email, In-app |
| 4 | Library Request Rejected | Borrow Request rejected | Student | Email |
| 5 | Library Book Issued | Issuance Record created | Student | Email, In-app |
| 6 | Library Book Returned | Return Record created | Student | Email |
| 7 | Library Overdue Reminder | Daily at 08:00 | Student | Email, SMS |
| 8 | Library Overdue Notice Librarian | Daily at 08:00 | `snc_library_librarian` | Email |
| 9 | Library Approval Needed | Approval Record created | Assigned approver | Email, In-app |
| 10 | Library Approval Escalated | Approval escalated | `snc_library_manager` | Email, In-app |
| 11 | Library SLA Breach Warning | SLA at 80% | Assigned approver | Email, In-app |
| 12 | Library SLA Breach Alert | SLA breached | `snc_library_admin` | Email |
| 13 | Library Weekly Report Ready | Weekly flow completes | `snc_library_manager` | Email (with PDF) |

## 10. Reports (8)

| # | Name | Type | Table |
| --- | --- | --- | --- |
| 1 | Books by Category | Pie Chart | `u_library_books` |
| 2 | Books by Status | Bar Chart | `u_library_books` |
| 3 | Borrow Requests by Month | Line Chart | `u_library_borrow_requests` |
| 4 | Approval Decision Distribution | Pie Chart | `u_library_approvals` |
| 5 | Overdue Books List | List Report | `u_library_issuance_records` |
| 6 | Top Borrowed Books | Bar Chart | `u_library_issuance_records` |
| 7 | Student Borrow History | List Report | `u_library_borrow_requests` |
| 8 | SLA Performance | Bar Chart | `u_library_approvals` |

## 11. Dashboards (2)

| # | Name | Tabs |
|---|---|---|
| 1 | Library Operations Dashboard | Books Overview, Pending Requests, Approvals SLA, Overdue Tracker |
| 2 | Library Management Dashboard | Monthly Trends, Top Books, Approval Metrics, Report Export |

## 12. Roles (4)

| # | Name | Description |
| --- | --- | --- |
| 1 | `snc_library_student` | Submit borrow requests, view own history, receive notifications |
| 2 | `snc_library_librarian` | Manage books, process issuances/returns, view all requests |
| 3 | `snc_library_manager` | Escalate approvals, view reports, manage librarians |
| 4 | `snc_library_admin` | Full system access, override approval decisions, manage configuration |

## 13. ACL Rules

| # | Name | Operation | Type | Condition |
| --- | --- | --- | --- | --- |
| 1 | read_u_library_books | Read | record | `g_user.hasRole('snc_library_librarian') OR g_user.hasRole('snc_library_manager') OR g_user.hasRole('snc_library_admin')` |
| 2 | write_u_library_books | Write | record | `g_user.hasRole('snc_library_librarian') OR g_user.hasRole('snc_library_admin')` |
| 3 | read_u_library_borrow_requests_own | Read | record | `current.u_student == gs.getUserID() OR g_user.hasRole('snc_library_librarian')` |
| 4 | write_u_library_borrow_requests_own | Write | record | `current.u_student == gs.getUserID() AND current.u_status == 'Draft'` |
| 5 | read_u_library_approvals_assigned | Read | record | `current.assigned_to == gs.getUserID() OR g_user.hasRole('snc_library_manager')` |
| 6 | write_u_library_configuration | Write | record | `g_user.hasRole('snc_library_admin')` |
| 7 | read_u_library_audit_log | Read | record | `g_user.hasRole('snc_library_admin')` |
| 8 | delete_u_library_notification_log | Delete | record | `g_user.hasRole('snc_library_admin')` |
