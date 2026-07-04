# Notifications & Email Templates

# Smart Library Request Workflow — ServiceNow Enterprise Solution

> **Document Type:** Notifications Design  
> **Version:** 2.0.0  
> **Application Scope:** `x_univ_library`  
> **Status:** Final — Complete

---

## 1. Notification Architecture

All notifications follow the ServiceNow notification framework:

- **Delivery Channels:** In-platform notification center AND email (when enabled in configuration)
- **Retry Logic:** Up to 3 retries within 15 minutes on delivery failure
- **Logging:** Every notification logged to `u_library_notification_log`
- **Master Toggle:** `notifications_enabled` parameter in `u_library_configuration`
- **Email Toggle:** `email_notifications_enabled` parameter in `u_library_configuration`

---

## 2. Notification Matrix

| ID | Event | Recipient | Channel | Trigger |
| ---- | ------- | ----------- | --------- | --------- |
| `LIB-NOTIF-001` | Borrow Request Submitted | Student | Email + In-platform | After Insert on borrow_request |
| `LIB-NOTIF-002` | Request Approved | Student | Email + In-platform | Flow: Approved branch |
| `LIB-NOTIF-003` | Request Rejected | Student | Email + In-platform | Flow: Rejected branch |
| `LIB-NOTIF-004` | Book Issued | Student | Email + In-platform | After Update: status → issued |
| `LIB-NOTIF-005` | Book Returned | Student | Email + In-platform | After Insert on return_record |
| `LIB-NOTIF-006` | Overdue Day 1 | Student | Email + In-platform | Scheduled Job |
| `LIB-NOTIF-007` | Overdue Day 3 | Student | Email + In-platform | Scheduled Job |
| `LIB-NOTIF-008` | Overdue Day 7 | Student | Email + In-platform | Scheduled Job |
| `LIB-NOTIF-009` | Overdue Day 14 | Student | Email + In-platform | Scheduled Job |
| `LIB-NOTIF-010` | New Pending Request | Librarian Group | Email + In-platform | After Insert on borrow_request |
| `LIB-NOTIF-011` | Approval Escalated | Library Manager | Email + In-platform | Flow: Escalation branch |
| `LIB-NOTIF-012` | Damage Report Created | Library Manager | Email + In-platform | After Insert: damage_reported = true |
| `LIB-NOTIF-013` | Admin Follow-up Required | library_admin Group | Email + In-platform | Flow: 72h breach |

---

## 3. Email Templates

---

### LIB-NOTIF-001 — Borrow Request Submitted

**Subject:** `Your Library Request Has Been Received — ${number}`

```html
<!-- Email Template Body -->
Dear ${student.first_name},

Thank you for submitting your library borrow request. We have received your request and it is now pending review by our library team.

REQUEST DETAILS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Request Number:    ${u_number}
Book Title:        ${u_book.u_title}
Author:            ${u_book.u_author}
Requested Pickup:  ${u_requested_pickup_date}
Submitted:         ${u_submitted_at}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

WHAT HAPPENS NEXT
You will receive a notification when a librarian has reviewed your request. 
Expected processing time: up to 2 business days.

To view your request status, visit the Library Self-Service Portal:
${portal_url}/library

If you have any questions, please contact the library directly.

Best regards,
University Library — Smart Borrowing System

────────────────────────────────────────
This is an automated notification. Please do not reply to this email.
For assistance, visit the library portal or contact library@university.edu
```

---

### LIB-NOTIF-002 — Request Approved

**Subject:** `Your Library Request Has Been Approved — ${u_book.u_title}`

```html

Dear ${student.first_name},

Great news! Your library borrow request has been approved.

APPROVAL DETAILS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Request Number:    ${u_number}
Book Title:        ${u_book.u_title}
Author:            ${u_book.u_author}
Approved By:       ${approver.first_name} ${approver.last_name}
Approval Date:     ${approval_date}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

PICKUP INSTRUCTIONS
Please collect your book from the library circulation desk within 3 calendar days.
After this deadline, your reservation may be cancelled.

Pickup Deadline: ${pickup_deadline}
Library Hours: Monday–Friday 08:00–20:00, Saturday 09:00–17:00

Please bring your university ID card when collecting your book.

Best regards,
University Library — Smart Borrowing System
```

---

### LIB-NOTIF-003 — Request Rejected

**Subject:** `Library Request Update — ${u_book.u_title}`

```html

Dear ${student.first_name},

We regret to inform you that your library borrow request has not been approved at this time.

REQUEST DETAILS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Request Number:    ${u_number}
Book Title:        ${u_book.u_title}
Reviewed By:       ${approver.first_name} ${approver.last_name}
Decision Date:     ${decision_date}
Reason:            ${rejection_reason}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

WHAT YOU CAN DO
- If you believe this decision is incorrect, please visit the library desk
- You may submit a new request once the reason above has been addressed
- Contact your librarian for assistance: library@university.edu

Best regards,
University Library — Smart Borrowing System
```

---

### LIB-NOTIF-004 — Book Issued

**Subject:** `Your Book Is Ready — ${u_book.u_title}`

```html

Dear ${student.first_name},

Your book has been issued to you. Please take note of your return deadline.

ISSUANCE DETAILS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Request Number:    ${u_number}
Book Title:        ${u_book.u_title}
Author:            ${u_book.u_author}
Issue Date:        ${issue_date}
📅 Return By:      ${expected_return_date}
Loan Period:       ${loan_period_days} days
Issued By:         ${issued_by.first_name} ${issued_by.last_name}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

⚠️ IMPORTANT: Please return this book by ${expected_return_date}.
Late returns may affect your borrowing privileges.

You will receive a reminder 3 days before your return deadline.

Best regards,
University Library — Smart Borrowing System
```

---

### LIB-NOTIF-005 — Book Returned

**Subject:** `Return Confirmed — Thank You`

```html

Dear ${student.first_name},

Thank you for returning your library book. Your borrowing record has been updated.

RETURN DETAILS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Book Title:        ${u_book.u_title}
Request Number:    ${u_number}
Return Date:       ${actual_return_date}
Condition:         ${return_condition}
Received By:       ${received_by.first_name} ${received_by.last_name}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

You may submit new borrow requests from the Library Portal.

Thank you for using the University Library Smart Borrowing System.

Best regards,
University Library — Smart Borrowing System
```

---

### LIB-NOTIF-006 — Overdue Day 1 Reminder

**Subject:** `Reminder: Library Book Due Yesterday — ${u_book.u_title}`

```html

Dear ${student.first_name},

This is a friendly reminder that your library book was due yesterday.

OVERDUE DETAILS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Book Title:        ${u_book.u_title}
Request Number:    ${u_number}
Due Date:          ${expected_return_date}
Days Overdue:      1 day
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Please return this book to the library circulation desk as soon as possible.
Continued overdue status may affect your ability to borrow new books.

Library Hours: Monday–Friday 08:00–20:00, Saturday 09:00–17:00

Best regards,
University Library — Smart Borrowing System
```

---

### LIB-NOTIF-007 — Overdue Day 3 Reminder

**Subject:** `Second Reminder: Overdue Library Book — ${u_book.u_title}`

```html

Dear ${student.first_name},

This is your second reminder. The library book listed below is now 3 days overdue.

⚠️ Your borrowing privileges are currently suspended until this book is returned.

OVERDUE DETAILS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Book Title:        ${u_book.u_title}
Request Number:    ${u_number}
Due Date:          ${expected_return_date}
Days Overdue:      3 days
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Please return this book immediately. If you are having difficulties, 
please contact the library: library@university.edu

Best regards,
University Library — Smart Borrowing System
```

---

### LIB-NOTIF-009 — Overdue Day 14 Final Notice

**Subject:** `Final Notice: Overdue Library Book — Action Required — ${u_book.u_title}`

```html

Dear ${student.first_name},

⚠️ URGENT: This is a final notice regarding your severely overdue library book.

This matter has been escalated to the Library Manager.

OVERDUE DETAILS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Book Title:        ${u_book.u_title}
Request Number:    ${u_number}
Due Date:          ${expected_return_date}
Days Overdue:      14 days
Status:            Escalated to Library Manager
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Please return this book IMMEDIATELY and contact the library to resolve this matter:
📧 library@university.edu
📍 University Library, Circulation Desk (Ground Floor)

Failure to return this book may result in:
- Suspension of your borrowing account
- Referral to the student affairs office

Best regards,
University Library — Smart Borrowing System
```

---

### LIB-NOTIF-010 — New Request Alert to Librarians

**Subject:** `New Borrow Request Awaiting Approval — ${u_book.u_title}`

```html

Hello Library Team,

A new borrow request has been submitted and is awaiting your review.

REQUEST DETAILS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Request Number:    ${u_number}
Book:              ${u_book.u_title} by ${u_book.u_author}
Student:           ${u_student.u_user.first_name} ${u_student.u_user.last_name}
Student ID:        ${u_student.u_university_id}
Requested Pickup:  ${u_requested_pickup_date}
Submitted:         ${u_submitted_at}
Notes:             ${u_notes}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

⏱️ SLA Deadline: ${sla_deadline} (48 hours from submission)

Please review and action this request in the Library Management System:
${platform_url}/nav_to.do?uri=u_library_borrow_requests.do?sys_id=${sys_id}

This request will auto-escalate to the Library Manager if not actioned within 48 hours.

University Library — Smart Borrowing System
```

---

### LIB-NOTIF-011 — Escalation to Library Manager

**Subject:** `Approval Escalated — Action Required — ${u_book.u_title}`

```html

Dear Library Manager,

A borrow request has been escalated to you as it has not been actioned within the required 48-hour window.

ESCALATION DETAILS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Request Number:    ${u_number}
Book:              ${u_book.u_title}
Student:           ${u_student.u_user.first_name} ${u_student.u_user.last_name}
Original Deadline: ${original_sla_deadline}
Escalated At:      ${escalation_time}
Your Deadline:     ${manager_sla_deadline} (72 hours from escalation)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Please review and action this request:
${platform_url}/nav_to.do?uri=u_library_borrow_requests.do?sys_id=${sys_id}

University Library — Smart Borrowing System
```

---

## 4. Notification Configuration

Each notification was configured in ServiceNow with:

- **Name:** `[LIB] <Event Name>` (e.g., `[LIB] Borrow Request Submitted`)
- **Table:** The triggering table
- **When to send:** Match the trigger event
- **Who will receive:** Role or user reference
- **Email layout:** University email HTML template applied
- **Subject:** Per template above
- **Message (HTML):** Per template above

All 13 notification records were created in the `x_univ_library` scope and included in the application Update Set. The `LibraryNotificationHelper` Script Include handles the programmatic trigger from Flow Designer flows, while record-based events (After Insert, After Update) are configured natively through ServiceNow notification records. The university SMTP server was configured in the platform's outbound email properties for email delivery.

---

*References: [requirements.md](../../.kiro/specs/smart-library-request-workflow/requirements.md) — FR-10*  
*See also: [FlowDesigner.md](FlowDesigner.md) | [ScriptIncludes.md](ScriptIncludes.md)*
