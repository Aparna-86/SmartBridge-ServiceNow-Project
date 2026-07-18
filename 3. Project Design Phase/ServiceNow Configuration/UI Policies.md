# UI Policies Design

# Smart Library Request Workflow — ServiceNow Enterprise Solution

> **Document Type:** UI Policies Design  
> **Version:** 2.0.0  
> **Application Scope:** `x_univ_library`  
> **Status:** Final — Complete

---

## Overview

Eight UI Policies govern form behavior for the Smart Library Request Workflow. UI Policies run on the client side and control field visibility, mandatory status, and read-only state.

---

## UP-01: Rejection Reason Mandatory on Reject

| Attribute | Value |
| ----------- | ------- |
| **Name** | `[LIB] Rejection Reason Mandatory` |
| **Table** | `u_library_approvals` |
| **Condition** | `Decision is Rejected` |
| **Applies on** | Insert and Update |

| Field | Mandatory | Read-only | Visible |
|-------|:---------:|:---------:|:-------:|
| `u_reason` | ✅ True | — | ✅ True |

---

## UP-02: Damage Description Required on Return

| Attribute | Value |
| ----------- | ------- |
| **Name** | `[LIB] Damage Description Required` |
| **Table** | `u_library_return_records` |
| **Condition** | `Damage Reported is true` |
| **Applies on** | Insert and Update |

| Field | Mandatory | Read-only | Visible |
|-------|:---------:|:---------:|:-------:|
| `u_damage_description` | ✅ True | — | ✅ True |

**Inverse Policy:** When `Damage Reported is false`:

| Field | Mandatory | Read-only | Visible |
|-------|:---------:|:---------:|:-------:|
| `u_damage_description` | ❌ False | ✅ True | ❌ False |

---

## UP-03: Borrow Request Read-Only When Closed

| Attribute | Value |
| ----------- | ------- |
| **Name** | `[LIB] Request Read-Only When Closed` |
| **Table** | `u_library_borrow_requests` |
| **Condition** | `Status is one of: Returned, Rejected, Cancelled` |
| **Applies on** | Update |

| Field | Mandatory | Read-only | Visible |
| ------- | :---------: | :---------: | :-------: |
| `u_notes` | — | ✅ True | ✅ True |
| `u_requested_pickup_date` | — | ✅ True | ✅ True |
| `u_book` | — | ✅ True | ✅ True |

---

## UP-04: Hide System Fields from Students

| Attribute | Value |
| ----------- | ------- |
| **Name** | `[LIB] Hide Internal Fields for Students` |
| **Table** | `u_library_borrow_requests` |
| **Condition** | `Current user has role student_library` |
| **Applies on** | All views |

| Field | Mandatory | Read-only | Visible |
|-------|:---------:|:---------:|:-------:|
| `u_overdue_flag` | — | — | ❌ False |
| `u_opened_by` | — | — | ❌ False |

---

## UP-05: Available Copies Read-Only (System Maintained)

| Attribute | Value |
| ----------- | ------- |
| **Name** | `[LIB] Available Copies Read-Only` |
| **Table** | `u_library_books` |
| **Condition** | Always |
| **Applies on** | All views |

| Field | Mandatory | Read-only | Visible |
|-------|:---------:|:---------:|:-------:|
| `u_available_copies` | — | ✅ True | ✅ True |
| `u_availability_status` | — | ✅ True | ✅ True |

---

## UP-06: Book Fields Visible When Active

| Attribute | Value |
| ----------- | ------- |
| **Name** | `[LIB] Show Extended Fields When Active` |
| **Table** | `u_library_books` |
| **Condition** | `Active is true` |
| **Applies on** | All views |

| Field | Mandatory | Read-only | Visible |
| ------- | :---------: | :---------: | :-------: |
| `u_location` | — | — | ✅ True |
| `u_cover_image_url` | — | — | ✅ True |
| `u_description` | — | — | ✅ True |

---

## UP-07: Max Borrow Limit Read-Only for Non-Admins

| Attribute | Value |
| ----------- | ------- |
| **Name** | `[LIB] Borrow Limit Admin Only` |
| **Table** | `u_library_students` |
| **Condition** | `Current user does NOT have role library_admin` |
| **Applies on** | All views |

| Field | Mandatory | Read-only | Visible |
|-------|:---------:|:---------:|:-------:|
| `u_max_borrow_limit` | — | ✅ True | ✅ True |
| `u_active_borrow_count` | — | ✅ True | ✅ True |

---

## UP-08: Category Deactivation Warning Display

| Attribute | Value |
| ----------- | ------- |
| **Name** | `[LIB] Category Deactivation Warning` |
| **Table** | `u_library_categories` |
| **Condition** | `Active is false AND record is not new` |
| **Applies on** | Update |

This UI Policy triggers a client-callable function that displays a warning message to the user. This is supplemented by Client Script CS-05 which performs the actual book count check.

---

*References: [requirements.md](../../.kiro/specs/smart-library-request-workflow/requirements.md) — FR-01, FR-06, FR-08, FR-13*  
*See also: [ClientScripts.md](ClientScripts.md) | [BusinessRules.md](BusinessRules.md)*
