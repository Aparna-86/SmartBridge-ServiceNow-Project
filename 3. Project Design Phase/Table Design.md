# Tables — Smart Library Request Workflow

**Scope:** `x_univ_library`  
**Platform:** ServiceNow Washington DC

---

## Table: `u_library_books` — Books

**Extends:** Task  
**Label:** Books

| Field | Type | Length | Default | Reference | Description |
| --- | --- | --- | --- | --- | --- |
| `u_book_id` | String | 20 | Auto-generated | — | Unique book identifier (LIB-XXXXX) |
| `u_title` | String | 255 | — | — | Book title |
| `u_author` | String | 255 | — | — | Author name |
| `u_isbn` | String | 20 | — | — | ISBN-10 or ISBN-13 |
| `u_publisher` | String | 128 | — | — | Publisher name |
| `u_publication_year` | Integer | 4 | — | — | Year of publication |
| `u_category` | Reference | — | — | `u_library_categories` | Book category |
| `u_status` | Choice | 40 | Available | — | Available, Issued, Lost, Damaged, Inactive |
| `u_total_copies` | Integer | 5 | 1 | — | Total copies in inventory |
| `u_available_copies` | Integer | 5 | 1 | — | Copies currently available |
| `u_shelf_location` | String | 50 | — | — | Physical shelf location |
| `u_description` | String | 4000 | — | — | Book description |
| `u_cover_image` | Image | — | — | — | Book cover attachment |
| `u_date_added` | Date/Time | — | — | — | Record creation date |
| `u_last_updated` | Date/Time | — | — | — | Last modification date |
| `u_active` | Boolean | — | true | — | Active flag |

**Indexes:** `u_book_id` (unique), `u_isbn`, `u_category`, `u_status`  
**Relationships:** `u_category` → `u_library_categories`, referenced by `u_library_issuance_records.u_book`

---

## Table: `u_library_categories` — Categories

**Label:** Categories

| Field | Type | Length | Default | Reference | Description |
| --- | --- | --- | --- | --- | --- |
| `u_category_code` | String | 10 | — | — | Short category code |
| `u_name` | String | 100 | — | — | Category name |
| `u_description` | String | 4000 | — | — | Category description |
| `u_parent_category` | Reference | — | — | `u_library_categories` | Parent category |
| `u_sort_order` | Integer | 5 | 0 | — | Display sort order |
| `u_is_active` | Boolean | — | true | — | Active flag |

**Indexes:** `u_category_code` (unique), `u_name`  
**Relationships:** `u_parent_category` → `u_library_categories` (self-referencing), referenced by `u_library_books.u_category`

---

## Table: `u_library_students` — Students

**Extends:** User  
**Label:** Students

| Field | Type | Length | Default | Reference | Description |
| --- | --- | --- | --- | --- | --- |
| `u_student_id` | String | 20 | — | — | University student ID |
| `u_department` | String | 100 | — | — | Department or major |
| `u_enrollment_year` | Integer | 4 | — | — | Year of enrollment |
| `u_phone` | String | 20 | — | — | Contact phone number |
| `u_max_borrow` | Integer | 3 | 5 | — | Maximum concurrent borrow limit |
| `u_borrow_count` | Integer | 3 | 0 | — | Current active borrow count |
| `u_total_fines` | Decimal | 10,2 | 0.00 | — | Total outstanding fines |
| `u_eligibility_status` | Choice | 20 | Eligible | — | Eligible, Suspended, Graduated |
| `u_last_activity_date` | Date/Time | — | — | — | Last system interaction |

**Indexes:** `u_student_id` (unique), `u_department`, `u_eligibility_status`  
**Relationships:** Extends `sys_user`, referenced by `u_library_borrow_requests.u_student`

---

## Table: `u_library_librarians` — Librarians

**Extends:** User  
**Label:** Librarians

| Field | Type | Length | Default | Reference | Description |
| --- | --- | --- | --- | --- | --- |
| `u_employee_id` | String | 20 | — | — | Employee ID |
| `u_librarian_type` | Choice | 30 | — | — | Librarian, Senior Librarian, Manager, Admin |
| `u_department` | String | 100 | — | — | Library department |
| `u_shift_timing` | String | 50 | — | — | Work shift schedule |
| `u_extension_number` | String | 10 | — | — | Phone extension |
| `u_date_joined` | Date | — | — | — | Employment start date |
| `u_is_active` | Boolean | — | true | — | Active flag |

**Indexes:** `u_employee_id` (unique), `u_librarian_type`  
**Relationships:** Extends `sys_user`, referenced by `u_library_approvals.u_approver`

---

## Table: `u_library_borrow_requests` — Borrow Requests

**Extends:** Task  
**Label:** Borrow Requests

| Field | Type | Length | Default | Reference | Description |
| --- | --- | --- | --- | --- | --- |
| `u_request_number` | String | 20 | Auto-generated | — | Unique request number (BR-XXXXX) |
| `u_student` | Reference | — | — | `u_library_students` | Requesting student |
| `u_book` | Reference | — | — | `u_library_books` | Requested book |
| `u_request_type` | Choice | 20 | — | — | Borrow, Renew, Reserve |
| `u_status` | Choice | 30 | Draft | — | Draft, Pending, Approved, Rejected, Issued, Returned, Cancelled |
| `u_borrow_duration_days` | Integer | 3 | 14 | — | Requested borrow duration |
| `u_purpose` | String | 4000 | — | — | Purpose of borrowing |
| `u_urgency` | Choice | 10 | Normal | — | Low, Normal, High, Critical |
| `u_submission_date` | Date/Time | — | — | — | Date request was submitted |
| `u_decision_date` | Date/Time | — | — | — | Date of approval/rejection |
| `u_notes` | String | 4000 | — | — | Internal notes |
| `u_last_action_at` | Date/Time | — | — | — | Timestamp of last update |
| `u_sla_deadline` | Date/Time | — | — | — | SLA deadline timestamp |
| `u_sla_breached` | Boolean | — | false | — | SLA breach flag |

**Indexes:** `u_request_number` (unique), `u_student`, `u_book`, `u_status`, `u_submission_date`  
**Relationships:** `u_student` → `u_library_students`, `u_book` → `u_library_books`, referenced by `u_library_approvals.u_request`, `u_library_issuance_records.u_borrow_request`

---

## Table: `u_library_approvals` — Approvals

**Extends:** Task  
**Label:** Approvals

| Field | Type | Length | Default | Reference | Description |
| --- | --- | --- | --- | --- | --- |
| `u_approval_number` | String | 20 | Auto-generated | — | Unique approval number (AP-XXXXX) |
| `u_request` | Reference | — | — | `u_library_borrow_requests` | Associated borrow request |
| `u_approval_group` | Choice | 30 | librarian_library | — | librarian_library, library_manager, library_admin |
| `u_approver` | Reference | — | — | `u_library_librarians` | Assigned approver |
| `u_decision` | Choice | 20 | — | — | Pending, Approved, Rejected, Returned for Revision |
| `u_comments` | String | 4000 | — | — | Approval comments |
| `u_assigned_date` | Date/Time | — | — | — | Date assigned to approver |
| `u_decision_date` | Date/Time | — | — | — | Date of decision |
| `u_sla_deadline` | Date/Time | — | — | — | SLA deadline (48h from assignment) |
| `u_sla_breached` | Boolean | — | false | — | SLA breach flag |
| `u_escalated_to` | Reference | — | — | `u_library_librarians` | Escalated approver |
| `u_escalation_date` | Date/Time | — | — | — | Escalation timestamp |
| `u_escalation_sla` | Date/Time | — | — | — | Escalation SLA (72h from escalation) |
| `u_override_approver` | Reference | — | — | `sys_user` | Admin who overrode the decision |
| `u_override_reason` | String | 4000 | — | — | Reason for override |

**Indexes:** `u_approval_number` (unique), `u_request`, `u_approval_group`, `u_approver`, `u_decision`  
**Relationships:** `u_request` → `u_library_borrow_requests`, `u_approver` → `u_library_librarians`, `u_escalated_to` → `u_library_librarians`

---

## Table: `u_library_issuance_records` — Issuance Records

**Label:** Issuance Records

| Field | Type | Length | Default | Reference | Description |
| --- | --- | --- | --- | --- | --- |
| `u_issuance_number` | String | 20 | Auto-generated | — | Unique issuance number (ISS-XXXXX) |
| `u_borrow_request` | Reference | — | — | `u_library_borrow_requests` | Associated borrow request |
| `u_book` | Reference | — | — | `u_library_books` | Issued book |
| `u_student` | Reference | — | — | `u_library_students` | Student who borrowed |
| `u_issued_by` | Reference | — | — | `u_library_librarians` | Librarian who issued |
| `u_issue_date` | Date/Time | — | — | — | Date of issuance |
| `u_due_date` | Date/Time | — | — | — | Expected return date |
| `u_status` | Choice | 20 | Issued | — | Issued, Returned, Overdue, Lost |
| `u_fine_amount` | Decimal | 10,2 | 0.00 | — | Fine assessed (if applicable) |
| `u_notes` | String | 4000 | — | — | Additional notes |

**Indexes:** `u_issuance_number` (unique), `u_borrow_request`, `u_book`, `u_student`, `u_status`, `u_due_date`  
**Relationships:** `u_borrow_request` → `u_library_borrow_requests`, `u_book` → `u_library_books`, `u_student` → `u_library_students`, `u_issued_by` → `u_library_librarians`, referenced by `u_library_return_records.u_issuance_record`

---

## Table: `u_library_return_records` — Return Records

**Label:** Return Records

| Field | Type | Length | Default | Reference | Description |
| --- | --- | --- | --- | --- | --- |
| `u_return_number` | String | 20 | Auto-generated | — | Unique return number (RET-XXXXX) |
| `u_issuance_record` | Reference | — | — | `u_library_issuance_records` | Associated issuance record |
| `u_book` | Reference | — | — | `u_library_books` | Returned book |
| `u_student` | Reference | — | — | `u_library_students` | Returning student |
| `u_processed_by` | Reference | — | — | `u_library_librarians` | Librarian who processed |
| `u_return_date` | Date/Time | — | — | — | Date of return |
| `u_condition` | Choice | 20 | Good | — | Good, Fair, Damaged, Lost |
| `u_late_fee` | Decimal | 10,2 | 0.00 | — | Late return fee |
| `u_comments` | String | 4000 | — | — | Condition comments |

**Indexes:** `u_return_number` (unique), `u_issuance_record`, `u_book`, `u_return_date`  
**Relationships:** `u_issuance_record` → `u_library_issuance_records`, `u_book` → `u_library_books`, `u_student` → `u_library_students`, `u_processed_by` → `u_library_librarians`

---

## Table: `u_library_notification_log` — Notification Log

**Label:** Notification Log

| Field | Type | Length | Default | Reference | Description |
| --- | --- | --- | --- | --- | --- |
| `u_notification_id` | String | 20 | Auto-generated | — | Unique notification ID (NOT-XXXXX) |
| `u_recipient` | Reference | — | — | `sys_user` | Notification recipient |
| `u_notification_type` | Choice | 40 | — | — | Email, In-app, SMS |
| `u_category` | Choice | 40 | — | — | Request, Approval, Overdue, Report, System |
| `u_subject` | String | 255 | — | — | Notification subject |
| `u_body` | String | 8000 | — | — | Notification body content |
| `u_reference_table` | String | 100 | — | — | Source table name |
| `u_reference_sys_id` | String | 32 | — | — | Source record sys_id |
| `u_sent_date` | Date/Time | — | — | — | Date notification was sent |
| `u_read` | Boolean | — | false | — | Read flag |
| `u_read_date` | Date/Time | — | — | — | Date notification was read |
| `u_delivery_status` | Choice | 20 | Sent | — | Sent, Delivered, Failed, Bounced |
| `u_priority` | Choice | 10 | Medium | — | Low, Medium, High |

**Indexes:** `u_recipient`, `u_category`, `u_sent_date`, `u_read`  
**Relationships:** `u_recipient` → `sys_user`

---

## Table: `u_library_configuration` — Configuration

**Label:** Configuration

| Field | Type | Length | Default | Reference | Description |
| --- | --- | --- | --- | --- | --- |
| `u_config_key` | String | 100 | — | — | Configuration key (unique) |
| `u_config_value` | String | 4000 | — | — | Configuration value |
| `u_config_type` | Choice | 20 | String | — | String, Integer, Boolean, JSON |
| `u_description` | String | 4000 | — | — | Description of configuration |
| `u_module` | Choice | 30 | General | — | General, Borrowing, Approval, Notification, Fine |
| `u_is_editable` | Boolean | — | true | — | Whether editable via UI |
| `u_last_modified_by` | Reference | — | — | `sys_user` | Last modifier |
| `u_last_modified_date` | Date/Time | — | — | — | Last modification date |

**Indexes:** `u_config_key` (unique), `u_module`  
**Relationships:** `u_last_modified_by` → `sys_user`

---

## Table: `u_library_audit_log` — Audit Log

**Label:** Audit Log

| Field | Type | Length | Default | Reference | Description |
| --- | --- | --- | --- | --- | --- |
| `u_audit_id` | String | 20 | Auto-generated | — | Unique audit ID (AUD-XXXXX) |
| `u_table_name` | String | 100 | — | — | Affected table |
| `u_record_sys_id` | String | 32 | — | — | Affected record |
| `u_field_name` | String | 100 | — | — | Changed field |
| `u_old_value` | String | 4000 | — | — | Previous value |
| `u_new_value` | String | 4000 | — | — | New value |
| `u_action` | Choice | 20 | — | — | Create, Update, Delete |
| `u_changed_by` | Reference | — | — | `sys_user` | User who made the change |
| `u_change_date` | Date/Time | — | — | — | Date of change |
| `u_ip_address` | String | 45 | — | — | User IP address |
| `u_session_id` | String | 64 | — | — | User session ID |

**Indexes:** `u_table_name`, `u_record_sys_id`, `u_action`, `u_change_date`, `u_changed_by`  
**Relationships:** `u_changed_by` → `sys_user`

---

## Entity-Relationship Summary

```
u_library_categories ────< u_library_books
                               │
                               │
u_library_students ────< u_library_borrow_requests >─── u_library_books
                               │
                               │
u_library_borrow_requests ────< u_library_approvals >─── u_library_librarians
                               │
                               │
u_library_borrow_requests ────< u_library_issuance_records >─── u_library_books
                               │                              >─── u_library_students
                               │                              >─── u_library_librarians
                               │
u_library_issuance_records ────< u_library_return_records >─── u_library_books
                                                           >─── u_library_students
                                                           >─── u_library_librarians
```

Key:

- `───<` : One-to-many (referencing table has foreign key)
- `>───` : Many-to-one (table has foreign key to referenced table)
