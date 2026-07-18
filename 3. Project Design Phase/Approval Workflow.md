# Approval Workflow — Smart Library Request Workflow

**Scope:** `x_univ_library`  
**Platform:** ServiceNow Washington DC

---

## 1. Overview

The approval workflow manages the lifecycle of library borrow requests from submission through final decision. It implements a multi-stage escalation chain with SLA monitoring, notification triggers, and administrative override capabilities.

### Workflow Stages

```text
Student submits request
        │
        ▼
┌─────────────────────────────┐
│  Stage 1: Pending Approval  │  SLA: 48 hours
│  Group: librarian_library   │
└─────────────────────────────┘
        │
        ├── Approved ──────────► Issuance Processing
        │
        ├── Rejected ──────────► Student Notified
        │
        └── SLA Breached ─────► Escalation Triggered
                                    │
                                    ▼
                        ┌─────────────────────────────┐
                        │  Stage 2: Escalated         │  SLA: 72 hours
                        │  Group: library_manager     │
                        └─────────────────────────────┘
                                    │
                                    ├── Approved ──────► Issuance Processing
                                    │
                                    ├── Rejected ──────► Student Notified
                                    │
                                    └── SLA Breached ──► Admin Follow-up
                                                              │
                                                              ▼
                                                  ┌─────────────────────────────┐
                                                  │  Stage 3: Admin Follow-up   │  No SLA
                                                  │  Group: library_admin       │
                                                  └─────────────────────────────┘
                                                              │
                                                              ├── Approved
                                                              │
                                                              └── Rejected
```

---

## 2. Approval Stages

### Stage 1: Pending Approval

| Property | Value |
| --- | --- |
| **Group** | `librarian_library` |
| **SLA** | 48 hours (2 business days) |
| **Trigger** | Borrow request submitted with status "Pending" |
| **Action** | Approval record created with `u_approval_group = 'librarian_library'` |
| **Assignee** | Any member of `snc_library_librarian` |
| **Decision Options** | Approved, Rejected |

**SLA Timer:** Starts when the approval record is created (`sys_created_on`). SLA deadline stored in `u_sla_deadline`.

### Stage 2: Escalated

| Property | Value |
| --- | --- |
| **Group** | `library_manager` |
| **SLA** | 72 hours (3 business days) |
| **Trigger** | Breach of Stage 1 SLA, or manual escalation via "Escalate Approval" UI Action |
| **Action** | New approval record created or existing record updated with `u_approval_group = 'library_manager'` |
| **Assignee** | Any member of `snc_library_manager` |
| **Decision Options** | Approved, Rejected |

**SLA Timer:** Starts at escalation timestamp (`u_escalation_date`). New SLA deadline stored in `u_escalation_sla`.

### Stage 3: Admin Follow-up

| Property | Value |
| --- | --- |
| **Group** | `library_admin` |
| **SLA** | None (manual follow-up) |
| **Trigger** | Breach of Stage 2 SLA |
| **Action** | Notification sent to all `snc_library_admin` users. No automatic reassignment; admin reviews via dashboard. |
| **Assignee** | Assigned manually by admin |
| **Decision Options** | Approved, Rejected, Override |

---

## 3. SLA Breach Handling

### SLA Breach Detection

The system checks SLA breaches via two mechanisms:

1. **Scheduled BR:** A business rule runs every 15 minutes on the `u_library_approvals` table, querying for records where `u_decision = 'Pending'` and `u_sla_deadline < gs.now()`.
2. **Flow Trigger:** The "Library Borrow Request Process" flow includes an SLA boundary check node that fires on deadline expiry.

### Breach Actions

| Action | Description |
| --- | --- |
| **Set Flag** | `u_sla_breached = true` on the approval record |
| **Notify Approver** | "Library SLA Breach Warning" sent at 80% of SLA duration |
| **Notify Manager** | "Library SLA Breach Alert" sent to `snc_library_manager` on breach |
| **Escalate** | Approval automatically escalated to next group in chain |
| **Log** | SLA breach event recorded in `u_library_audit_log` |

### SLA Timeline

```text
Assignment (T=0)
    │
    ├── 38.4h (80%) ──► SLA Breach Warning sent to approver
    │
    ├── 48h (100%) ──► SLA Breach Alert sent to manager
    │                  ► Escalation to library_manager (Stage 2)
    │                  ► Escalation SLA clock starts
    │
    ├── 110.4h (80% of Stage 2) ──► Second warning
    │
    ├── 120h (100% of Stage 2) ───► Second breach alert
    │                               ► Admin Follow-up (Stage 3)
    │
    └── Manual resolution by admin
```

---

## 4. Approval Groups

| Group | Name | Role | Escalation Order |
| --- | --- | --- | --- |
| `librarian_library` | Librarian | `snc_library_librarian` | 1 |
| `library_manager` | Library Manager | `snc_library_manager` | 2 |
| `library_admin` | Library Administrator | `snc_library_admin` | 3 |

### Group Membership Rules

- **librarian_library:** All users with `snc_library_librarian` role.
- **library_manager:** Users with `snc_library_manager` role (subset of librarians with management authority).
- **library_admin:** Users with `snc_library_admin` role (system administrators with override capability).

---

## 5. Notification Triggers

| Event | Notification | Recipient | Channel |
| --- | --- | --- | --- |
| Approval record created | Library Approval Needed | Assigned approver | Email, In-app |
| SLA at 80% | Library SLA Breach Warning | Assigned approver | Email, In-app |
| SLA breached (Stage 1) | Library SLA Breach Alert | `snc_library_manager` | Email |
| SLA breached (Stage 2) | Library SLA Breach Alert | `snc_library_admin` | Email |
| Approval escalated | Library Approval Escalated | New approver in next group | Email, In-app |
| Decision made (Approved) | Library Request Approved | Requesting student | Email, In-app |
| Decision made (Rejected) | Library Request Rejected | Requesting student | Email |

---

## 6. Override Mechanics

### Who Can Override

Only users with the `snc_library_admin` role can override approval decisions.

### Override Process

1. Admin navigates to the approval record.
2. Admin clicks the **Override Decision** UI Action.
3. A confirmation dialog prompts the admin to enter an override reason.
4. On confirmation, `LibraryApprovalManager.overrideDecision()` toggles the `u_decision` field (Approved ↔ Rejected).
5. The following fields are updated:
   - `u_override_approver` = current admin's sys_id
   - `u_override_reason` = admin-provided reason
   - `u_decision_date` = current timestamp
6. The borrow request's `u_status` is updated to match the overridden decision.
7. An audit log entry is created recording the override details.
8. Notification is sent to the requesting student with the decision and override note.

### Override Rules

| Rule | Description |
| --- | --- |
| Must have reason | Override is rejected if no reason is provided |
| Cannot self-override | Admin cannot override if they were the original approver |
| Audit trail | All overrides are logged immutably in `u_library_audit_log` |
| Irreversible | An override cannot be undone (must create a new request) |

---

## 7. Flow Designer: Library Borrow Request Process

The approval workflow is orchestrated by the **Library Borrow Request Process** Flow Designer flow.

### Flow Steps

```text
Trigger: Record Created (u_library_borrow_requests, u_status = "Pending")
    │
    1. Create Approval Record
       ├── Set approval_group = "librarian_library"
       ├── Set assigned_date = now
       ├── Set sla_deadline = now + 48 hours
       └── Set decision = "Pending"
    │
    2. Send Notification: Library Approval Needed
    │
    3. Wait for Decision (Approved / Rejected)
       ├── SLA Timer running (48h)
       ├── Check: Is SLA breached?
       │      ├── No → Continue waiting
       │      └── Yes → Go to Step 4 (Escalation)
       └── Decision received → Go to Step 5
    │
    4. Escalation Path
       ├── Update approval_group = "library_manager"
       ├── Set escalated_to = current user
       ├── Set escalation_date = now
       ├── Set escalation_sla = now + 72 hours
       ├── Send Notification: Library Approval Escalated
       └── Go to Step 3 (Wait for Decision)
    │
    5. Decision Processing
       ├── Approved:
       │     ├── Update borrow request status = "Approved"
       │     ├── Set decision_date = now
       │     └── Send Notification: Library Request Approved
       │
       └── Rejected:
             ├── Update borrow request status = "Rejected"
             ├── Set decision_date = now
             └── Send Notification: Library Request Rejected
```

---

## 8. Configuration Parameters

The following `u_library_configuration` records control approval workflow behavior:

| Key | Type | Default | Description |
| --- | --- | --- | --- |
| `approval_sla_hours_stage1` | Integer | 48 | Stage 1 SLA duration in hours |
| `approval_sla_hours_stage2` | Integer | 72 | Stage 2 SLA duration in hours |
| `approval_warning_threshold` | Decimal | 0.80 | SLA fraction at which warning notification fires |
| `approval_auto_escalate` | Boolean | true | Whether SLA breach triggers automatic escalation |
| `approval_require_reason_reject` | Boolean | true | Whether rejection requires a comment reason |
| `approval_max_overrides_per_day` | Integer | 3 | Maximum overrides allowed per admin per day |

---

## 9. Error Handling

| Scenario | Handling |
| --- | --- |
| No approvers in group | Flow pauses, notification sent to `snc_library_admin` with group details |
| SLA timer exceeds max value | Capped at 168 hours (7 days) by `LibraryApprovalManager` |
| Duplicate escalation | Blocked by business rule — only one escalation per approval record |
| Override on already-closed request | Blocked — borrow request must be in "Pending" or "Approved" or "Rejected" state |
| Orphaned approval (no request) | Cleaned up by daily scheduled job, logged to `u_library_audit_log` |
