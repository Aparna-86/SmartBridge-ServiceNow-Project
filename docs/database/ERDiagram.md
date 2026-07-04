# Entity Relationship Diagram

# Smart Library Request Workflow — ServiceNow Enterprise Solution

> **Document Type:** Entity Relationship Diagram  
> **Version:** 2.0.0  
> **Application Scope:** `x_univ_library`  
> **Status:** Final — Complete

---

## 1. Complete ER Diagram

```mermaid
erDiagram
    CATEGORIES {
        string sys_id PK "ServiceNow system ID"
        string u_name "Category name — unique, required, max 100"
        string u_description "Category description — text"
        string u_icon "Icon identifier — max 50"
        boolean u_active "Active flag — default true"
        datetime sys_created_on "Created timestamp"
        ref sys_created_by FK "Creator user"
        datetime sys_updated_on "Last updated"
        ref sys_updated_by FK "Last updater"
    }

    BOOKS {
        string sys_id PK "ServiceNow system ID"
        string u_title "Book title — required, max 255"
        string u_author "Author name — required, max 255"
        string u_isbn "ISBN — unique, required, max 20"
        ref u_category FK "FK → CATEGORIES"
        string u_publisher "Publisher — max 255"
        int u_publication_year "Year 1000–current"
        string u_edition "Edition string — max 50"
        int u_total_copies "Total owned — 0..9999"
        int u_available_copies "Currently available — 0..9999"
        string u_location "Shelf location — max 100"
        string u_description "Synopsis — max 2000"
        string u_cover_image_url "Image URL — max 500"
        string u_availability_status "Available | Unavailable"
        boolean u_active "Active flag — default true"
    }

    SYS_USER {
        string sys_id PK "ServiceNow user sys_id"
        string user_name "Login name"
        string email "Email address"
        string first_name "First name"
        string last_name "Last name"
    }

    STUDENTS {
        string sys_id PK "ServiceNow system ID"
        ref u_user FK "FK → SYS_USER — required"
        string u_university_id "Student ID — unique, required, max 50"
        string u_department "Department — max 100"
        int u_enrollment_year "Enrollment year"
        int u_max_borrow_limit "Max concurrent borrows — 1..20, default 5"
        int u_active_borrow_count "System-maintained borrow count"
        boolean u_active "Active flag — default true"
    }

    LIBRARIANS {
        string sys_id PK "ServiceNow system ID"
        ref u_user FK "FK → SYS_USER — required"
        string u_staff_id "Staff ID — unique, required, max 50"
        string u_department_assignments "Category list (multi-reference)"
        boolean u_active "Active flag — default true"
    }

    BORROW_REQUESTS {
        string sys_id PK "ServiceNow system ID"
        string u_number "Request number — LIB-00000001 format"
        ref u_book FK "FK → BOOKS — required"
        ref u_student FK "FK → STUDENTS — required"
        date u_requested_pickup_date "Preferred pickup date"
        string u_notes "Student notes — max 500"
        string u_status "Pending Approval|Approved|Rejected|Escalated|Issued|Overdue|Returned|Cancelled"
        boolean u_overdue_flag "System-set overdue flag"
        datetime u_submitted_at "Submission timestamp"
        ref u_opened_by FK "FK → SYS_USER (submitter)"
    }

    APPROVALS {
        string sys_id PK "ServiceNow system ID"
        ref u_borrow_request FK "FK → BORROW_REQUESTS — required"
        ref u_approver FK "FK → SYS_USER (librarian or manager)"
        string u_decision "Approved|Rejected|Escalated"
        string u_reason "Decision reason — max 500"
        datetime u_decided_at "Decision timestamp"
        string u_override_notes "Override reason if Library Manager override"
    }

    ISSUANCE_RECORDS {
        string sys_id PK "ServiceNow system ID"
        ref u_borrow_request FK "FK → BORROW_REQUESTS — required"
        ref u_book FK "FK → BOOKS"
        ref u_student FK "FK → STUDENTS"
        ref u_issued_by FK "FK → LIBRARIANS"
        datetime u_issue_date "Physical handover datetime"
        date u_expected_return_date "Calculated return deadline"
        int u_loan_period_days "Applied loan period in days"
    }

    RETURN_RECORDS {
        string sys_id PK "ServiceNow system ID"
        ref u_borrow_request FK "FK → BORROW_REQUESTS — required"
        datetime u_actual_return_date "Return datetime"
        string u_return_condition "On Time|Overdue"
        int u_overdue_days "Days past due — 0 if on time"
        boolean u_damage_reported "Damage flag"
        string u_damage_description "Damage details — text"
        ref u_received_by FK "FK → LIBRARIANS"
    }

    NOTIFICATION_LOG {
        string sys_id PK "ServiceNow system ID"
        ref u_recipient FK "FK → SYS_USER"
        string u_event_type "Event type string"
        string u_template_id "e.g., LIB-NOTIF-001"
        datetime u_sent_at "Send timestamp"
        string u_delivery_status "Sent|Failed"
        int u_retry_count "Retry attempts — max 3"
    }

    CONFIGURATION {
        string sys_id PK "ServiceNow system ID"
        string u_parameter_key "Parameter key — unique"
        string u_parameter_value "Parameter value"
        string u_data_type "Integer|Boolean|String|List"
        string u_description "Parameter description"
        datetime u_updated_at "Last updated"
        ref u_updated_by FK "FK → SYS_USER"
    }

    AUDIT_LOG {
        string sys_id PK "ServiceNow system ID"
        string u_table_name "Affected table"
        string u_record_sys_id "Affected record sys_id"
        ref u_user FK "FK → SYS_USER (actor)"
        string u_action "Created|Updated|Deleted|Access Denied"
        string u_field_name "Changed field name"
        string u_old_value "Previous value"
        string u_new_value "New value"
        datetime u_timestamp "Event timestamp"
    }

    %% Relationships
    CATEGORIES ||--o{ BOOKS : "classifies"
    SYS_USER ||--o| STUDENTS : "has profile"
    SYS_USER ||--o| LIBRARIANS : "has profile"
    BOOKS ||--o{ BORROW_REQUESTS : "requested via"
    STUDENTS ||--o{ BORROW_REQUESTS : "submits"
    BORROW_REQUESTS ||--o{ APPROVALS : "receives"
    BORROW_REQUESTS ||--o| ISSUANCE_RECORDS : "generates"
    BORROW_REQUESTS ||--o| RETURN_RECORDS : "closes with"
    LIBRARIANS ||--o{ ISSUANCE_RECORDS : "issues"
    LIBRARIANS ||--o{ RETURN_RECORDS : "receives"
    SYS_USER ||--o{ NOTIFICATION_LOG : "receives"
    SYS_USER ||--o{ AUDIT_LOG : "generates"
```

---

## 2. Relationship Summary

| Relationship | Type | Description |
| ------------- | ------ | ------------- |
| Category → Books | One-to-Many | A category classifies many books |
| SYS_USER → Students | One-to-Zero/One | Each ServiceNow user may have one student profile |
| SYS_USER → Librarians | One-to-Zero/One | Each ServiceNow user may have one librarian profile |
| Books → Borrow Requests | One-to-Many | A book can be requested many times |
| Students → Borrow Requests | One-to-Many | A student submits many requests |
| Borrow Requests → Approvals | One-to-Many | A request can have multiple approval records (override history) |
| Borrow Requests → Issuance Records | One-to-Zero/One | An approved request generates one issuance record |
| Borrow Requests → Return Records | One-to-Zero/One | An issued request generates one return record |
| Librarians → Issuance Records | One-to-Many | A librarian issues many books |
| Librarians → Return Records | One-to-Many | A librarian receives many returns |

---

## 3. Key Constraints

| Table | Field | Constraint |
| ------- | ------- | ----------- |
| BOOKS | `u_isbn` | UNIQUE across all records |
| BOOKS | `u_available_copies` | 0 ≤ value ≤ `u_total_copies` |
| BOOKS | `u_total_copies` | Cannot be set below current borrowed count |
| STUDENTS | `u_university_id` | UNIQUE across all records |
| STUDENTS | `u_max_borrow_limit` | 1 ≤ value ≤ 20 |
| LIBRARIANS | `u_staff_id` | UNIQUE across all records |
| BORROW_REQUESTS | `u_number` | UNIQUE, auto-generated `LIB-XXXXXXXX` |
| CATEGORIES | `u_name` | UNIQUE among active categories |
| CONFIGURATION | `u_parameter_key` | UNIQUE across all records |
| AUDIT_LOG | All fields | READ-ONLY — no update or delete permitted |

---

## 4. Index Strategy

| Table | Indexed Fields | Rationale |
| ------- | --------------- | ----------- |
| BOOKS | `u_isbn`, `u_title`, `u_author`, `u_available_copies`, `u_active` | ISBN lookups, portal search, availability queries |
| CATEGORIES | `u_name`, `u_active` | Category browse and deactivation guard |
| STUDENTS | `u_university_id`, `u_user`, `u_active` | Profile lookup, role assignment trigger |
| BORROW_REQUESTS | `u_student`, `u_book`, `u_status`, `u_overdue_flag`, `u_submitted_at` | Active borrow queries, overdue detection |
| APPROVALS | `u_borrow_request`, `u_decided_at` | SLA calculation, escalation detection |
| ISSUANCE_RECORDS | `u_borrow_request`, `u_expected_return_date` | Overdue date comparisons |
| AUDIT_LOG | `u_table_name`, `u_user`, `u_timestamp` | Admin audit searches |

---

*References: [requirements.md](../../.kiro/specs/smart-library-request-workflow/requirements.md) — Section 9 (Data Model Overview)*  
*See also: [DataDictionary.md](DataDictionary.md) | [DatabaseDesign.md](DatabaseDesign.md)*
