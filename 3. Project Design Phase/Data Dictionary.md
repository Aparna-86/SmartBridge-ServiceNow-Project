# Data Dictionary

# Smart Library Request Workflow — ServiceNow Enterprise Solution

> **Document Type:** Data Dictionary  
> **Version:** 2.0.0  
> **Application Scope:** `x_univ_library`  
> **Status:** Final — Complete

---

## 1. Table: u_library_books

**Purpose:** Stores the complete book catalog with availability tracking.  
**Label:** Library Books  
**Extends:** None  
**Requirement:** FR-01

| Field Name | Column Name | Type | Max Length | Mandatory | Default | Unique | Description |
| ----------- | ------------- | ------ | ----------- | ----------- | --------- | -------- | ------------- |
| Title | `u_title` | String | 255 | ✅ Yes | — | No | Full book title |
| Author | `u_author` | String | 255 | ✅ Yes | — | No | Primary author name |
| ISBN | `u_isbn` | String | 20 | ✅ Yes | — | ✅ Yes | International Standard Book Number |
| Category | `u_category` | Reference (u_library_categories) | — | No | — | No | Book category classification |
| Publisher | `u_publisher` | String | 255 | No | — | No | Publisher name |
| Publication Year | `u_publication_year` | Integer | — | No | — | No | Year: 1000 – current year |
| Edition | `u_edition` | String | 50 | No | — | No | Edition descriptor (e.g., "3rd") |
| Total Copies | `u_total_copies` | Integer | — | ✅ Yes | 1 | No | Total physical copies owned; range 0–9999 |
| Available Copies | `u_available_copies` | Integer | — | ✅ Yes | = total_copies | No | Copies available to borrow; system-maintained; range 0–9999 |
| Location | `u_location` | String | 100 | No | — | No | Shelf/aisle/room location descriptor |
| Description | `u_description` | Multi-line Text | 2000 | No | — | No | Book synopsis or summary |
| Cover Image URL | `u_cover_image_url` | URL | 500 | No | — | No | External URL for book cover image |
| Availability Status | `u_availability_status` | Choice | — | ✅ Yes | Available | No | System-maintained: Available / Unavailable |
| Active | `u_active` | True/False | — | ✅ Yes | true | No | Whether record is active; false = deactivated |

**Indexes:** `u_isbn` (unique), `u_title`, `u_author`, `u_available_copies`, `u_active`  
**ACL Rules:** Students: Read only. Librarians: Read/Write. Managers: Read/Write/Delete. Admins: Full.

---

## 2. Table: u_library_categories

**Purpose:** Stores book classification categories used for browsing and filtering.  
**Label:** Library Categories  
**Extends:** None  
**Requirement:** FR-02

| Field Name | Column Name | Type | Max Length | Mandatory | Default | Unique | Description |
| ----------- | ------------- | ------ | ----------- | ----------- | --------- | -------- | ------------- |
| Name | `u_name` | String | 100 | ✅ Yes | — | ✅ Yes (active) | Category name; unique among active records |
| Description | `u_description` | Multi-line Text | 1000 | No | — | No | Category description |
| Icon | `u_icon` | String | 50 | No | — | No | Icon CSS class or identifier for portal |
| Active | `u_active` | True/False | — | ✅ Yes | true | No | Whether category is currently active |

**Indexes:** `u_name`, `u_active`  
**ACL Rules:** Students/Librarians: Read only. Managers: Read/Write. Admins: Full.

---

## 3. Table: u_library_students

**Purpose:** Stores student profiles linked to ServiceNow user accounts.  
**Label:** Library Students  
**Extends:** sys_user (via reference field, not extension table)  
**Requirement:** FR-03

| Field Name | Column Name | Type | Max Length | Mandatory | Default | Unique | Description |
| ----------- | ------------- | ------ | ----------- | ----------- | --------- | -------- | ------------- |
| User | `u_user` | Reference (sys_user) | — | ✅ Yes | — | No | Linked ServiceNow user account |
| University ID | `u_university_id` | String | 50 | ✅ Yes | — | ✅ Yes | University-issued student ID |
| Department | `u_department` | String | 100 | No | — | No | Academic department name |
| Enrollment Year | `u_enrollment_year` | Integer | — | No | — | No | Year of enrollment (4-digit) |
| Max Borrow Limit | `u_max_borrow_limit` | Integer | — | ✅ Yes | 5 | No | Maximum concurrent borrows; range 1–20 |
| Active Borrow Count | `u_active_borrow_count` | Integer | — | ✅ Yes | 0 | No | System-maintained count of currently issued books |
| Active | `u_active` | True/False | — | ✅ Yes | true | No | Whether student account is active |

**Indexes:** `u_university_id` (unique), `u_user`, `u_active`  
**ACL Rules:** Students: Read own record. Librarians: Read all. Managers: Read all. Admins: Full.

---

## 4. Table: u_library_librarians

**Purpose:** Stores librarian staff profiles linked to ServiceNow user accounts.  
**Label:** Library Librarians  
**Extends:** sys_user (via reference field)  
**Requirement:** FR-04

| Field Name | Column Name | Type | Max Length | Mandatory | Default | Unique | Description |
| ----------- | ------------- | ------ | ----------- | ----------- | --------- | -------- | ------------- |
| User | `u_user` | Reference (sys_user) | — | ✅ Yes | — | No | Linked ServiceNow user account |
| Staff ID | `u_staff_id` | String | 50 | ✅ Yes | — | ✅ Yes | University-issued staff ID |
| Department Assignments | `u_department_assignments` | List (u_library_categories) | — | No | — | No | Categories this librarian manages (multi-ref) |
| Active | `u_active` | True/False | — | ✅ Yes | true | No | Whether librarian account is active |

**Indexes:** `u_staff_id` (unique), `u_user`, `u_active`  
**ACL Rules:** Admins: Full. Managers: Read. Librarians: Read own. Students: No access.

---

## 5. Table: u_library_borrow_requests

**Purpose:** Central transaction table tracking all book borrow request records.  
**Label:** Library Borrow Requests  
**Extends:** None  
**Requirement:** FR-05

| Field Name | Column Name | Type | Max Length | Mandatory | Default | Unique | Description |
| ----------- | ------------- | ------ | ----------- | ----------- | --------- | -------- | ------------- |
| Request Number | `u_number` | String | 20 | ✅ Yes | Auto-generated | ✅ Yes | Format: LIB-00000001 (zero-padded 8-digit) |
| Book | `u_book` | Reference (u_library_books) | — | ✅ Yes | — | No | The requested book |
| Student | `u_student` | Reference (u_library_students) | — | ✅ Yes | — | No | The requesting student |
| Requested Pickup Date | `u_requested_pickup_date` | Date | — | No | — | No | Preferred pickup date; 1–30 days from submission |
| Notes | `u_notes` | Multi-line Text | 500 | No | — | No | Optional student notes |
| Status | `u_status` | Choice | — | ✅ Yes | Pending Approval | No | Pending Approval / Approved / Rejected / Escalated / Issued / Overdue / Returned / Cancelled |
| Overdue Flag | `u_overdue_flag` | True/False | — | ✅ Yes | false | No | System-set when expected return date passes |
| Submitted At | `u_submitted_at` | Date/Time | — | ✅ Yes | Now() | No | Submission timestamp |
| Opened By | `u_opened_by` | Reference (sys_user) | — | ✅ Yes | Current user | No | Submitting user's sys_id |

**Status Values:**

| Value | Description |
| ------- | ------------- |
| Pending Approval | Request submitted, awaiting librarian action |
| Approved | Librarian approved; awaiting physical issuance |
| Rejected | Request rejected by librarian or manager |
| Escalated | 48h SLA breached; reassigned to Library Manager |
| Issued | Book physically handed over to student |
| Overdue | Expected return date passed and book not returned |
| Returned | Book returned successfully |
| Cancelled | Student cancelled the request |

**Indexes:** `u_student`, `u_book`, `u_status`, `u_overdue_flag`, `u_submitted_at`  
**ACL Rules:** Students: Read/Create own records. Librarians/Managers: Read all, Write. Admins: Full.

---

## 6. Table: u_library_approvals

**Purpose:** Records every approval decision made on a borrow request, including overrides.  
**Label:** Library Approvals  
**Extends:** None  
**Requirement:** FR-06

| Field Name | Column Name | Type | Max Length | Mandatory | Default | Unique | Description |
| ----------- | ------------- | ------ | ----------- | ----------- | --------- | -------- | ------------- |
| Borrow Request | `u_borrow_request` | Reference (u_library_borrow_requests) | — | ✅ Yes | — | No | Parent borrow request |
| Approver | `u_approver` | Reference (sys_user) | — | ✅ Yes | — | No | User who made the decision |
| Decision | `u_decision` | Choice | — | ✅ Yes | — | No | Approved / Rejected / Escalated |
| Reason | `u_reason` | Multi-line Text | 500 | ✅ Yes (if Rejected) | — | No | Decision rationale; required for rejection |
| Decided At | `u_decided_at` | Date/Time | — | ✅ Yes | Now() | No | Timestamp of decision |
| Override Notes | `u_override_notes` | Multi-line Text | 500 | No | — | No | Additional notes for Library Manager overrides |

**Indexes:** `u_borrow_request`, `u_decided_at`  
**ACL Rules:** Librarians/Managers: Read/Write. Admins: Full. Students: No access.

---

## 7. Table: u_library_issuance_records

**Purpose:** Tracks physical book handover details for each approved borrow request.  
**Label:** Library Issuance Records  
**Extends:** None  
**Requirement:** FR-07

| Field Name | Column Name | Type | Max Length | Mandatory | Default | Unique | Description |
| ----------- | ------------- | ------ | ----------- | ----------- | --------- | -------- | ------------- |
| Borrow Request | `u_borrow_request` | Reference (u_library_borrow_requests) | — | ✅ Yes | — | No | Parent borrow request |
| Book | `u_book` | Reference (u_library_books) | — | ✅ Yes | — | No | The issued book |
| Student | `u_student` | Reference (u_library_students) | — | ✅ Yes | — | No | Recipient student |
| Issued By | `u_issued_by` | Reference (u_library_librarians) | — | ✅ Yes | — | No | Librarian performing issuance |
| Issue Date | `u_issue_date` | Date/Time | — | ✅ Yes | Now() | No | Actual physical handover date and time |
| Expected Return Date | `u_expected_return_date` | Date | — | ✅ Yes | Calculated | No | Issue date + loan_period_days |
| Loan Period Days | `u_loan_period_days` | Integer | — | ✅ Yes | 14 | No | Applied loan period; from config or category override |

**Indexes:** `u_borrow_request`, `u_expected_return_date`  
**ACL Rules:** Students: Read own. Librarians/Managers: Read/Write. Admins: Full.

---

## 8. Table: u_library_return_records

**Purpose:** Records book return details and condition assessment.  
**Label:** Library Return Records  
**Extends:** None  
**Requirement:** FR-08

| Field Name | Column Name | Type | Max Length | Mandatory | Default | Unique | Description |
| ----------- | ------------- | ------ | ----------- | ----------- | --------- | -------- | ------------- |
| Borrow Request | `u_borrow_request` | Reference (u_library_borrow_requests) | — | ✅ Yes | — | No | Parent borrow request |
| Actual Return Date | `u_actual_return_date` | Date/Time | — | ✅ Yes | Now() | No | Physical return date and time |
| Return Condition | `u_return_condition` | Choice | — | ✅ Yes | On Time | No | On Time / Overdue |
| Overdue Days | `u_overdue_days` | Integer | — | ✅ Yes | 0 | No | Days past expected return date; 0 if on time |
| Damage Reported | `u_damage_reported` | True/False | — | ✅ Yes | false | No | Whether physical damage was noted |
| Damage Description | `u_damage_description` | Multi-line Text | 1000 | No | — | No | Description of damage; required if damage_reported = true |
| Received By | `u_received_by` | Reference (u_library_librarians) | — | ✅ Yes | — | No | Librarian processing the return |

**Indexes:** `u_borrow_request`, `u_actual_return_date`  
**ACL Rules:** Librarians/Managers: Read/Write. Admins: Full. Students: Read own.

---

## 9. Table: u_library_notification_log

**Purpose:** Immutable log of every notification sent by the application.  
**Label:** Library Notification Log  
**Extends:** None  
**Requirement:** FR-10-AC-9

| Field Name | Column Name | Type | Max Length | Mandatory | Default | Unique | Description |
| ----------- | ------------- | ------ | ----------- | ----------- | --------- | -------- | ------------- |
| Recipient | `u_recipient` | Reference (sys_user) | — | ✅ Yes | — | No | Notification recipient |
| Event Type | `u_event_type` | String | 100 | ✅ Yes | — | No | Trigger event name |
| Template ID | `u_template_id` | String | 20 | ✅ Yes | — | No | e.g., LIB-NOTIF-001 |
| Sent At | `u_sent_at` | Date/Time | — | ✅ Yes | Now() | No | Send timestamp |
| Delivery Status | `u_delivery_status` | Choice | — | ✅ Yes | Sent | No | Sent / Failed |
| Retry Count | `u_retry_count` | Integer | — | ✅ Yes | 0 | No | Number of delivery retries; max 3 |

**Indexes:** `u_recipient`, `u_event_type`, `u_sent_at`  
**ACL Rules:** Admins: Read/Write. Others: No access.

---

## 10. Table: u_library_configuration

**Purpose:** Centralized parameter store for all system configuration values.  
**Label:** Library Configuration  
**Extends:** None  
**Requirement:** FR-16

| Field Name | Column Name | Type | Max Length | Mandatory | Default | Unique | Description |
| ----------- | ------------- | ------ | ----------- | ----------- | --------- | -------- | ------------- |
| Parameter Key | `u_parameter_key` | String | 100 | ✅ Yes | — | ✅ Yes | Configuration key name |
| Parameter Value | `u_parameter_value` | String | 500 | ✅ Yes | — | No | Configuration value |
| Data Type | `u_data_type` | Choice | — | ✅ Yes | — | No | Integer / Boolean / String / List |
| Description | `u_description` | String | 500 | No | — | No | Human-readable description |
| Updated At | `u_updated_at` | Date/Time | — | ✅ Yes | Now() | No | Last modification timestamp |
| Updated By | `u_updated_by` | Reference (sys_user) | — | ✅ Yes | Current user | No | Last modifier |

**Default Parameter Records:**

| Parameter Key | Default Value | Data Type | Description |
| --------------- | --------------- | ----------- | ------------- |
| `default_loan_period_days` | `14` | Integer | Default book loan period in days |
| `max_borrow_limit_default` | `5` | Integer | Default maximum concurrent borrows per student |
| `approval_escalation_hours` | `48` | Integer | Hours before approval escalates to Library Manager |
| `overdue_reminder_days` | `1,3,7,14` | List | Comma-separated overdue reminder day thresholds |
| `notifications_enabled` | `true` | Boolean | Master toggle for all notifications |
| `email_notifications_enabled` | `true` | Boolean | Toggle for email delivery channel |
| `portal_search_enabled` | `true` | Boolean | Toggle for full-text portal search |
| `damage_report_notification_enabled` | `true` | Boolean | Toggle for damage report notifications |

**ACL Rules:** Admins only: Read/Write. All other roles: No access.

---

## 11. Table: u_library_audit_log

**Purpose:** Immutable record of all create, update, delete, and access denial events.  
**Label:** Library Audit Log  
**Extends:** None  
**Requirement:** FR-14

| Field Name | Column Name | Type | Max Length | Mandatory | Default | Unique | Description |
| ----------- | ------------- | ------ | ----------- | ----------- | --------- | -------- | ------------- |
| Table Name | `u_table_name` | String | 100 | ✅ Yes | — | No | Name of the affected table |
| Record Sys ID | `u_record_sys_id` | String | 32 | ✅ Yes | — | No | sys_id of the affected record |
| User | `u_user` | Reference (sys_user) | — | ✅ Yes | — | No | User who performed the action |
| Action | `u_action` | Choice | — | ✅ Yes | — | No | Created / Updated / Deleted / Access Denied |
| Field Name | `u_field_name` | String | 100 | No | — | No | Name of the changed field (for updates) |
| Old Value | `u_old_value` | Multi-line Text | 5000 | No | — | No | Previous field value (for updates) |
| New Value | `u_new_value` | Multi-line Text | 5000 | No | — | No | New field value (for updates) |
| Timestamp | `u_timestamp` | Date/Time | — | ✅ Yes | Now() | No | Precise event timestamp |

**Critical Constraint:** All records in this table are READ-ONLY. The `delete` and `write` ACL on this table is restricted to `nobody` — no user or process can modify or delete audit log records. Retention: minimum 3 years.

**Indexes:** `u_table_name`, `u_user`, `u_timestamp`, `u_action`  
**ACL Rules:** Admins: Read only. All others: No access. Write/Delete: Nobody.

---

*References: [requirements.md](../../.kiro/specs/smart-library-request-workflow/requirements.md)*  
*See also: [ERDiagram.md](ERDiagram.md) | [DatabaseDesign.md](DatabaseDesign.md)*
