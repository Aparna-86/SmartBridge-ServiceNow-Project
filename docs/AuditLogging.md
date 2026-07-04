# Audit Logging — Smart Library Request Workflow

**Scope:** `x_univ_library`
**Platform:** ServiceNow Washington DC
**Version:** 1.0.0

---

## 1. Overview

The `u_library_audit_log` table provides an immutable, tamper-evident audit trail for all significant operations within the Smart Library Request Workflow application. The audit system captures record lifecycle events, approval actions, configuration changes, and access denial events. Audit logs are write-once, read-only by authorized personnel, and retained for 3 years with automated archival.

---

## 2. Table: `u_library_audit_log`

**Label:** Audit Log
**Scope:** `x_univ_library`

### 2.1 Schema

| Field | Type | Length | Default | Reference | Description |
| --- | --- | --- | --- | --- | --- |
| `u_audit_id` | String | 20 | Auto-generated | — | Unique audit identifier (AUD-XXXXX) |
| `u_table_name` | String | 100 | — | — | Name of the affected table |
| `u_record_sys_id` | String | 32 | — | — | sys_id of the affected record |
| `u_record_number` | String | 20 | — | — | Human-readable record number |
| `u_field_name` | String | 100 | — | — | Name of the changed field (null for Create/Delete) |
| `u_old_value` | String | 4000 | — | — | Previous value before the change |
| `u_new_value` | String | 4000 | — | — | New value after the change |
| `u_action` | Choice | 20 | — | — | `create`, `update`, `delete`, `access_denied`, `approval`, `config_change` |
| `u_changed_by` | Reference | — | — | `sys_user` | User who performed the action |
| `u_change_date` | Date/Time | — | — | — | UTC timestamp of the action |
| `u_ip_address` | String | 45 | — | — | Source IP address of the user |
| `u_session_id` | String | 64 | — | — | ServiceNow session ID |
| `u_user_agent` | String | 256 | — | — | Browser user agent string |
| `u_details` | String | 8000 | — | — | Free-text additional context |

### 2.2 Indexes

| Index Name | Fields | Type |
| --- | --- | --- |
| `idx_audit_table` | `u_table_name` | Non-unique |
| `idx_audit_record` | `u_record_sys_id` | Non-unique |
| `idx_audit_action` | `u_action` | Non-unique |
| `idx_audit_date` | `u_change_date` | Non-unique |
| `idx_audit_user` | `u_changed_by` | Non-unique |
| `idx_audit_composite` | `u_table_name`, `u_action`, `u_change_date` | Non-unique |

### 2.3 Relationships

- `u_changed_by` → `sys_user`

---

## 3. Events Captured

### 3.1 Record Created

Triggered by the After Insert business rule on all core application tables. All field values are captured as a JSON string in `u_new_value`.

| Table | Action Value |
| --- | --- |
| `u_library_books` | `create` |
| `u_library_categories` | `create` |
| `u_library_students` | `create` |
| `u_library_librarians` | `create` |
| `u_library_borrow_requests` | `create` |
| `u_library_approvals` | `create` |
| `u_library_issuance_records` | `create` |
| `u_library_return_records` | `create` |
| `u_library_notification_log` | `create` |
| `u_library_configuration` | `create` |

### 3.2 Record Updated

Triggered by the After Update business rule. Only changed fields are logged — one audit record per modified field. System fields (`sys_updated_on`, `sys_updated_by`) are excluded.

### 3.3 Record Deleted

Triggered by the Before Delete business rule. Full record content is serialized to JSON in `u_old_value` before deletion proceeds. Restricted to admin role.

### 3.4 Access Denied

Triggered by the `LibraryACLFailureHandler` Script Include that intercepts ACL evaluation failures. Logs the user, table, attempted operation, and rejection reason.

### 3.5 Approval Action

Captured when an approver approves, rejects, or returns a request for revision. Stores decision, comments, and approval number in `u_details`.

### 3.6 Configuration Change

Triggered on update of any `u_library_configuration` record. Logs old and new values with the modifier identity.

---

## 4. Business Rule BR-15 — Audit Log Writer

BR-15 is the sole mechanism for writing to the `u_library_audit_log` table.

| Property | Value |
| --- | --- |
| **Name** | BR-15 — Audit Log Writer |
| **Table** | All core application tables |
| **When** | After Insert, After Update, Before Delete, custom event `audit.access_denied` |
| **Condition** | `current.operation != 'delete'` (delete handled separately) |
| **Order** | 100 |
| **Scope** | `x_univ_library` |

**Behavior:**

- On **Insert**: Creates one audit record with all field values in `u_new_value` as JSON.
- On **Update**: Iterates over changed fields (excluding system fields) and creates one audit record per changed field with `u_old_value` and `u_new_value`.
- On **Delete**: Serializes entire record to `u_old_value`, captures the deleting user, and inserts.
- On **custom event**: Receives payload from `LibraryACLFailureHandler` with access denial details.

The Business Rule includes a recursive guard: if `current.getTableName() == 'u_library_audit_log'`, it exits immediately to prevent infinite loops.

---

## 5. Read-Only Enforcement

The `u_library_audit_log` table is protected by three enforcement layers:

### 5.1 ACL Layer

| Operation | Allowed Roles |
| --- | --- |
| Read | `x_univ_library.manager`, `x_univ_library.admin` |
| Create | System only (via BR-15) |
| Write | None |
| Delete | None |

### 5.2 Business Rule Layer

A **Before Delete** business rule (`BR-15-Delete Guard`) runs with condition `gs.hasRole('admin')` evaluating to false. The rule aborts the deletion with error: `"Audit log records cannot be deleted. Retention policy is managed by the archival process."`

### 5.3 UI Layer

- "Delete" related link removed from audit log form via UI Policy.
- List view does not render delete checkboxes for any role.
- Context menu "Delete" option suppressed via UI Action condition.

---

## 6. Retention Policy

### 6.1 Retention Schedule

| Duration | Storage | Accessibility |
| --- | --- | --- |
| 0–3 years | `u_library_audit_log` (active) | Full search by manager+ |
| 3–7 years | `u_library_audit_log_archive` | Read-only via archive module |
| 7+ years | Purged | Not accessible |

### 6.2 Archival Job: `LIB_AUDIT_ARCHIVE`

| Property | Value |
| --- | --- |
| Schedule | 1st of every month at 02:00 UTC |
| Condition | `u_change_date < (now - 3 years)` |
| Action | Move to archive table, then delete from active |
| Error handling | Job stops on first failure and creates sys_event |

### 6.3 Purge Job: `LIB_AUDIT_PURGE`

| Property | Value |
| --- | --- |
| Schedule | 1st of every quarter at 03:00 UTC |
| Condition | Records in archive older than 7 years |
| Action | Permanent deletion |

### 6.4 Archive Table: `u_library_audit_log_archive`

Identical schema to the active table with a simplified index set (`u_audit_id`, `u_change_date` only).

---

## 7. Admin Search Interface

**Module:** Administration > Audit Log Explorer
**Available to:** `x_univ_library.manager`, `x_univ_library.admin`

| Feature | Description |
| --- | --- |
| **Date range filter** | From/To date picker on `u_change_date` |
| **Table filter** | Dropdown of application tables |
| **Action filter** | Checkboxes for each action type |
| **User filter** | Type-ahead reference lookup |
| **Record lookup** | Search by record number or sys_id |
| **Field filter** | Filter by `u_field_name` for update actions |
| **Export** | CSV and PDF export |
| **Detail drill-down** | Side-by-side old/new value comparison |
| **Archive toggle** | Switch between active and archive tables |

Color coding in list view: Create (green), Update (blue), Delete (red), Access Denied (orange), Approval (purple), Config Change (gray).

---

## 8. Sample Audit Log Entries

### Entry 1: Book Created

| Field | Value |
| --- | --- |
| `u_audit_id` | AUD-000042 |
| `u_table_name` | `u_library_books` |
| `u_record_number` | LIB-00042 |
| `u_action` | `create` |
| `u_new_value` | `{"u_title":"The Great Gatsby","u_author":"F. Scott Fitzgerald","u_isbn":"9780743273565","u_total_copies":"3","u_available_copies":"3"}` |
| `u_changed_by` | john.doe |
| `u_change_date` | 2026-02-15 09:30:00 UTC |

### Entry 2: Book Title Updated

| Field | Value |
| --- | --- |
| `u_audit_id` | AUD-00137 |
| `u_table_name` | `u_library_books` |
| `u_record_number` | LIB-00089 |
| `u_field_name` | `u_title` |
| `u_action` | `update` |
| `u_old_value` | `To Kill a Mockingbird (2nd Edition)` |
| `u_new_value` | `To Kill a Mockingbird (50th Anniversary Edition)` |
| `u_changed_by` | jane.smith |
| `u_change_date` | 2026-03-02 14:15:00 UTC |

### Entry 3: Borrow Request Approved

| Field | Value |
| --- | --- |
| `u_audit_id` | AUD-00291 |
| `u_table_name` | `u_library_borrow_requests` |
| `u_record_number` | BR-00015 |
| `u_action` | `update` |
| `u_field_name` | `u_status` |
| `u_old_value` | `pending_approval` |
| `u_new_value` | `approved` |
| `u_details` | `{"approval_number":"AP-00015","approver":"robert.johnson","decision":"approved"}` |
| `u_changed_by` | robert.johnson |
| `u_change_date` | 2026-03-10 11:45:00 UTC |

### Entry 4: Access Denied

| Field | Value |
| --- | --- |
| `u_audit_id` | AUD-00456 |
| `u_table_name` | `u_library_books` |
| `u_action` | `access_denied` |
| `u_details` | `{"operation":"delete","reason":"User does not have admin role"}` |
| `u_changed_by` | mike.wilson |
| `u_change_date` | 2026-03-15 16:20:00 UTC |
| `u_ip_address` | 10.0.1.105 |

### Entry 5: Configuration Change

| Field | Value |
| --- | --- |
| `u_audit_id` | AUD-00789 |
| `u_table_name` | `u_library_configuration` |
| `u_action` | `config_change` |
| `u_field_name` | `u_config_value` |
| `u_old_value` | `5` |
| `u_new_value` | `8` |
| `u_details` | `{"config_key":"max_books_per_student"}` |
| `u_changed_by` | admin |
| `u_change_date` | 2026-04-01 08:00:00 UTC |

### Entry 6: Record Deleted

| Field | Value |
| --- | --- |
| `u_audit_id` | AUD-00901 |
| `u_table_name` | `u_library_categories` |
| `u_record_number` | CAT-00005 |
| `u_action` | `delete` |
| `u_old_value` | `{"u_name":"Rare Books","u_category_code":"RARE","u_is_active":"true"}` |
| `u_changed_by` | admin |
| `u_change_date` | 2026-04-20 10:30:00 UTC |

---

## 9. Performance & Growth

| Metric | Estimate |
| --- | --- |
| Avg daily records | ~500 |
| Annual active table size | ~180,000 records |
| Query performance | Sub-second with indexed filters |
| Archival batch size | 10,000 records per run |

---

*References: [FunctionalRequirements.md](FunctionalRequirements.md) — FR-14, [SecurityDesign.md](SecurityDesign.md) — Section 5, [Tables.md](Tables.md) — u_library_audit_log*
