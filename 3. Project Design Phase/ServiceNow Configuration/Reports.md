# Reports Design

# Smart Library Request Workflow — ServiceNow Enterprise Solution

> **Document Type:** Reports Design  
> **Version:** 2.0.0  
> **Application Scope:** `x_univ_library`  
> **Status:** Final — Complete

---

## 1. Report Catalog

| Report ID | Report Name | Type | Access Level |
| ----------- | ------------- | ------ | ------------- |
| `RPT-001` | Books Borrowed per Month | Bar Chart + Table | Manager, Admin |
| `RPT-002` | Most Borrowed Books | Horizontal Bar | Manager, Admin |
| `RPT-003` | Overdue Books | List | Librarian, Manager, Admin |
| `RPT-004` | Student Activity | Summary Table | Manager, Admin |
| `RPT-005` | Inventory Status | Summary Table | Librarian, Manager, Admin |
| `RPT-006` | Notification Log Summary | Pie Chart | Admin |
| `RPT-007` | Audit Log Summary | List | Admin |
| `RPT-008` | Approval Processing Time | Bar Chart | Manager, Admin |

---

## 2. Report Specifications

### RPT-001: Books Borrowed per Month

**Purpose:** Track borrowing volume trends over time (ref. FR-11-AC-1).

| Attribute | Value |
| ----------- | ------- |
| **Table** | `u_library_borrow_requests` |
| **Filter** | `u_status IN (issued, returned)` |
| **Group By** | `u_submitted_at` (monthly) |
| **Aggregate** | COUNT |
| **Visualization** | Bar Chart + Data Table |
| **Time Period** | Last 12 months (configurable) |
| **Export** | CSV, PDF |

**Columns:**

- Month / Year
- Total Requests
- Requests Issued
- Requests Returned
- Requests Pending

---

### RPT-002: Most Borrowed Books (Top 20)

**Purpose:** Identify the most popular books in the catalog (ref. FR-11-AC-2).

| Attribute | Value |
| ----------- | ------- |
| **Table** | `u_library_borrow_requests` |
| **Filter** | `u_status IN (issued, returned)` AND date range (selectable) |
| **Group By** | `u_book.u_title` |
| **Aggregate** | COUNT |
| **Sort** | COUNT descending |
| **Limit** | Top 20 |
| **Visualization** | Horizontal Bar Chart |

**Columns:**

- Rank
- Book Title
- Author
- Category
- Total Borrows
- Current Availability

---

### RPT-003: Overdue Books

**Purpose:** List all currently overdue borrow requests for follow-up (ref. FR-11-AC-3, FR-09-AC-6).

| Attribute | Value |
| ----------- | ------- |
| **Table** | `u_library_borrow_requests` |
| **Filter** | `u_overdue_flag = true` AND `u_status != returned` |
| **Sort** | `overdue_days` descending |
| **Visualization** | Detailed List |
| **Export** | CSV, PDF |
| **Scheduling** | Available for daily scheduled delivery |

**Columns:**

- Request Number
- Student Name
- Student ID
- Book Title
- Expected Return Date
- Days Overdue
- Last Notification Sent

---

### RPT-004: Student Activity

**Purpose:** Analyze borrowing behavior by student (ref. FR-11-AC-4).

| Attribute | Value |
| ----------- | ------- |
| **Table** | `u_library_borrow_requests` |
| **Group By** | `u_student` |
| **Aggregates** | COUNT (total), SUM (by status) |
| **Visualization** | Summary Table |
| **Export** | CSV, PDF |

**Columns:**

- Student Name
- Student ID
- Department
- Total Requests
- Active Borrows
- Returns
- Overdue (current)
- Overdue (historical)

---

### RPT-005: Inventory Status

**Purpose:** Overview of the book catalog with current availability (ref. FR-11-AC-5).

| Attribute | Value |
| ----------- | ------- |
| **Table** | `u_library_books` |
| **Filter** | `u_active = true` |
| **Group By** | `u_category` |
| **Visualization** | Summary Table with Category Grouping |
| **Export** | CSV, PDF |

**Columns:**

- Category
- Book Title
- Author
- ISBN
- Total Copies
- Available Copies
- Borrowed Copies
- Availability Status

---

### RPT-006: Notification Log Summary

**Purpose:** Audit of notification delivery for system health monitoring (ref. FR-17-AC-6).

| Attribute | Value |
| ----------- | ------- |
| **Table** | `u_library_notification_log` |
| **Group By** | `u_event_type` |
| **Aggregate** | COUNT by `u_delivery_status` |
| **Visualization** | Pie Chart + Table |

**Columns:**

- Event Type
- Template ID
- Total Sent
- Successfully Delivered
- Failed
- Failure Rate %

---

### RPT-007: Audit Log Summary

**Purpose:** Administrative review of all system events (ref. FR-14-AC-3).

| Attribute | Value |
| ----------- | ------- |
| **Table** | `u_library_audit_log` |
| **Filter** | Date range (configurable) |
| **Sort** | `u_timestamp` descending |
| **Visualization** | Detailed List |
| **Filters** | By table, user, action type, date range |

**Columns:**

- Timestamp
- User
- Table
- Record
- Action
- Field Changed
- Old Value
- New Value

---

### RPT-008: Approval Processing Time

**Purpose:** Measure librarian responsiveness to borrow requests (ref. FR-11, FR-06).

| Attribute | Value |
| ----------- | ------- |
| **Table** | `u_library_approvals` |
| **Filter** | `u_decision IN (approved, rejected)` AND date range |
| **Group By** | `u_approver` |
| **Aggregate** | AVG processing time (decided_at - borrow_request.submitted_at) |
| **Visualization** | Bar Chart |

**Columns:**

- Librarian Name
- Total Decisions
- Average Processing Time (hours)
- Approvals
- Rejections
- Escalations

---

## 3. Report Access Control

| Report | Student | Librarian | Manager | Admin |
| -------- | :-------: | :---------: | :-------: | :-----: |
| RPT-001 | ❌ | ❌ | ✅ | ✅ |
| RPT-002 | ❌ | ❌ | ✅ | ✅ |
| RPT-003 | ❌ | ✅ | ✅ | ✅ |
| RPT-004 | ❌ | ❌ | ✅ | ✅ |
| RPT-005 | ❌ | ✅ | ✅ | ✅ |
| RPT-006 | ❌ | ❌ | ❌ | ✅ |
| RPT-007 | ❌ | ❌ | ❌ | ✅ |
| RPT-008 | ❌ | ❌ | ✅ | ✅ |

---

## 4. Scheduled Report Delivery

Scheduled report delivery was configured using ServiceNow Scheduled Report Delivery:

| Schedule | Report | Recipients | Format |
| ---------- | -------- | ----------- | -------- |
| Weekly (Monday 07:00) | RPT-001, RPT-003, RPT-005 | library_manager group | PDF |
| Daily (07:00) | RPT-003 | library_manager group | CSV |
| Monthly (1st, 07:00) | RPT-001, RPT-002, RPT-004, RPT-008 | library_admin group | PDF |

The scheduled deliveries were created as Scheduled Job records that trigger the ServiceNow PDF/CSV generation engine. Reports are automatically emailed to the configured recipient groups. The weekly bundle is generated as a consolidated PDF, while the daily overdue report is delivered in CSV format for downstream data processing. All schedules are configurable via the Scheduled Jobs module.

---

*References: [requirements.md](../../.kiro/specs/smart-library-request-workflow/requirements.md) — FR-11*  
*See also: [Dashboards.md](Dashboards.md) | [RolesAndPermissions.md](RolesAndPermissions.md)*
