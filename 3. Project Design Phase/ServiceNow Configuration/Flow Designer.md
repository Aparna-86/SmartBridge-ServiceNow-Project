# Flow Designer Documentation

# Smart Library Request Workflow — ServiceNow Enterprise Solution

> **Document Type:** Flow Designer Design  
> **Version:** 2.0.0  
> **Application Scope:** `x_univ_library`  
> **Status:** Final — Complete

---

## Overview

The Smart Library Request Workflow uses three Flow Designer flows to automate the complete borrow request lifecycle. All flows run within the `x_univ_library` scope.

| Flow Name | Trigger | Purpose |
| ----------- | --------- | --------- |
| `[LIB] Borrow Request Lifecycle` | Record Created (u_library_borrow_requests) | Main orchestration flow |
| `[LIB] Book Issuance Sub-Flow` | Called by parent flow | Issue book after approval |
| `[LIB] Overdue Management` | Scheduled (daily) | Detect and notify overdue books |

---

## Flow 1: Borrow Request Lifecycle

### Trigger

- **Type:** Record Created  
- **Table:** `u_library_borrow_requests`  
- **Condition:** `status = Pending Approval`

### Flow Diagram

```mermaid
flowchart TD
    START([🟢 Trigger: Borrow Request Created\nStatus = Pending Approval])

    START --> N1[📧 Send Notification\nLIB-NOTIF-001\nSubmission Confirmation → Student]
    N1 --> N2[📧 Send Notification\nLIB-NOTIF-010\nNew Request → Librarian Group]
    N2 --> A1[✅ Create Approval Task\nAssign to: librarian_library group]

    A1 --> WAIT{⏳ Wait for Decision\nor 48-hour SLA}

    WAIT -->|Librarian Approves| APR[Set Status = Approved\nRecord: approver, timestamp, notes]
    WAIT -->|Librarian Rejects| REJ[Set Status = Rejected\nRecord: rejection reason\nRestore available_copies]
    WAIT -->|48h SLA Breach| ESC[Set Status = Escalated\nReassign to library_manager group]

    APR --> N3[📧 Send Notification\nLIB-NOTIF-002\nApproval → Student]
    N3 --> SUB1[🔁 Call Sub-Flow:\nBook Issuance]

    REJ --> N4[📧 Send Notification\nLIB-NOTIF-003\nRejection → Student]
    REJ --> END_REJ([🔴 Flow Ends])

    ESC --> N5[📧 Send Notification\nLIB-NOTIF-011\nEscalation → Library Manager]
    ESC --> WAIT2{⏳ Wait for Manager Decision\nor 72h from Escalation}

    WAIT2 -->|Manager Approves| APR2[Set Status = Approved\nRecord override action]
    WAIT2 -->|Manager Rejects| REJ2[Set Status = Rejected\nRecord override reason\nRestore available_copies]
    WAIT2 -->|72h Breach| ADM[Create Follow-up Task\nassigned to library_admin group]

    APR2 --> N6[📧 Send Notification\nLIB-NOTIF-002\nApproval → Student]
    N6 --> SUB2[🔁 Call Sub-Flow:\nBook Issuance]
    REJ2 --> N7[📧 Send Notification\nLIB-NOTIF-003\nRejection → Student]
    ADM --> N8[📧 Send Notification\nAdmin Alert → library_admin group]

    SUB1 --> END([🟢 Flow Ends])
    SUB2 --> END
    REJ2 --> END_REJ2([🔴 Flow Ends])
    N8 --> END_ADM([🟡 Admin Follow-up Created])
```

### Flow Steps — Detailed

| Step # | Step Type | Name | Configuration |
| -------- | ----------- | ------ | --------------- |
| 1 | Action: Send Notification | Submission Confirmation | Template: LIB-NOTIF-001, Recipient: `trigger.u_student.u_user` |
| 2 | Action: Send Notification | New Request Alert | Template: LIB-NOTIF-010, Recipient Group: `librarian_library` |
| 3 | Action: Ask for Approval | Librarian Approval | Approver: `librarian_library` group, Due: +48 hours |
| 4 | Flow Logic: If | Branch on Approval | Approved / Rejected / Timeout |
| 5a | Action: Update Record | Set Status = Approved | `u_library_borrow_requests.u_status = approved` |
| 5b | Action: Update Record | Set Status = Rejected | `u_status = rejected`; restore availability |
| 5c | Flow Logic: Escalation | Reassign to Manager | `u_status = escalated`; reassign to `library_manager` group |
| 6 | Action: Send Notification | Decision Notification | Template varies by branch |
| 7 | Action: Flow Sub-Flow | Book Issuance | Call `[LIB] Book Issuance Sub-Flow` |

---

## Flow 2: Book Issuance Sub-Flow

### Trigger

- **Type:** Called from parent flow (`[LIB] Borrow Request Lifecycle`)  
- **Input:** `borrow_request_sys_id`

### Flow Diagram

```mermaid
flowchart TD
    START([🔁 Called: borrow_request_sys_id])
    START --> CALC[Calculate expected_return_date\n= today + loan_period_days\nfrom configuration or category]
    CALC --> CREATE[Create Issuance Record\nu_library_issuance_records]
    CREATE --> WAIT{⏳ Wait: Librarian Confirms\nPhysical Handover}
    WAIT -->|Confirmed| ISSUE[Set Borrow Request\nStatus = Issued\nRecord: issue_date, issued_by]
    ISSUE --> N1[📧 Send Notification\nLIB-NOTIF-004\nIssuance Confirmation → Student]
    N1 --> N2[Update Student\nactive_borrow_count += 1]
    N2 --> END([✅ Sub-Flow Complete])
```

### Loan Period Calculation Logic

```text
1. Check u_library_configuration for category-specific loan period
   key: "loan_period_days_[category_sys_id]"
2. If not found, use default_loan_period_days (default: 14)
3. expected_return_date = issue_date + loan_period_days
```

---

## Flow 3: Overdue Management (Scheduled Flow)

### Trigger

- **Type:** Scheduled Trigger  
- **Frequency:** Daily at 06:00 (configurable)

### Flow Diagram

```mermaid
flowchart TD
    START([⏰ Scheduled: Daily 06:00])
    START --> QUERY[Query: All Borrow Requests\nStatus = Issued\nExpected Return Date < Today]
    QUERY --> LOOP[For Each Overdue Request]

    LOOP --> SET_FLAG[Set u_overdue_flag = true\nSet u_status = overdue]
    SET_FLAG --> CALC_DAYS[Calculate overdue_days\n= today - expected_return_date]

    CALC_DAYS --> CHECK{Days Overdue?}

    CHECK -->|Day 1| N1[📧 LIB-NOTIF-006\n1st Reminder → Student]
    CHECK -->|Day 3| N2[📧 LIB-NOTIF-007\n2nd Reminder → Student]
    CHECK -->|Day 7| N3[📧 LIB-NOTIF-008\n3rd Reminder → Student]
    CHECK -->|Day 14| N4[📧 LIB-NOTIF-009\n4th Reminder → Student\n+ Escalate to Manager]
    CHECK -->|Day 15+| N5[Create Follow-up Task\n→ Library Manager\nWith overdue details]

    N1 & N2 & N3 & N4 & N5 --> LOG[Log execution to\nu_library_audit_log]
    LOG --> NEXT[Next Record]
    NEXT --> LOOP
    LOOP -->|No More Records| END([✅ Job Complete])
```

---

## Notification Templates Reference

All notifications used in flows:

| Template ID | Event | Recipient | Subject |
| ------------- | ------- | ----------- | --------- |
| `LIB-NOTIF-001` | Request Submitted | Student | "Your Library Request Has Been Received — [Request#]" |
| `LIB-NOTIF-002` | Request Approved | Student | "Your Library Request Has Been Approved — [Book Title]" |
| `LIB-NOTIF-003` | Request Rejected | Student | "Library Request Update — [Book Title]" |
| `LIB-NOTIF-004` | Book Issued | Student | "Your Book Is Ready — [Book Title]" |
| `LIB-NOTIF-005` | Book Returned | Student | "Return Confirmed — Thank You" |
| `LIB-NOTIF-006` | Overdue Day 1 | Student | "Reminder: Library Book Due Yesterday — [Book Title]" |
| `LIB-NOTIF-007` | Overdue Day 3 | Student | "Second Reminder: Overdue Library Book — [Book Title]" |
| `LIB-NOTIF-008` | Overdue Day 7 | Student | "Urgent: Library Book 7 Days Overdue — [Book Title]" |
| `LIB-NOTIF-009` | Overdue Day 14 | Student | "Final Notice: Overdue Library Book — Action Required" |
| `LIB-NOTIF-010` | New Pending Request | Librarian Group | "New Borrow Request Awaiting Approval — [Book Title]" |
| `LIB-NOTIF-011` | Approval Escalated | Library Manager | "Approval Escalated — Action Required Within 24 Hours" |
| `LIB-NOTIF-012` | Damage Reported | Library Manager | "Book Damage Report — [Book Title]" |
| `LIB-NOTIF-013` | Admin Follow-up | library_admin Group | "Library Workflow Escalation — Administrator Action Required" |

---

## SLA Configuration

Two SLA definitions are configured and active in the system:

| SLA Name | Table | Start Condition | Breach Duration | Action on Breach |
| ---------- | ------- | ---------------- | ----------------- | ----------------- |
| `[LIB] Librarian Approval SLA` | `u_library_approvals` | Status = Pending | 48 hours | Escalate to library_manager |
| `[LIB] Manager Approval SLA` | `u_library_approvals` | Status = Escalated | 72 hours from escalation | Create admin follow-up task |

The SLAs were configured using ServiceNow SLA Definition records. Each SLA timer starts automatically when the corresponding approval record is created with the matching status. On breach, the Flow Designer flow is triggered via a platform event, which handles the escalation or admin follow-up task creation. SLA performance metrics are captured in the timeline and available in the Approval Processing Time report (RPT-008).

---

*References: [requirements.md](../../.kiro/specs/smart-library-request-workflow/requirements.md) — FR-05 through FR-09, FR-10*  
*See also: [ApprovalWorkflow.md](../ApprovalWorkflow.md) | [Notifications.md](Notifications.md)*
