# Email Templates — Smart Library Request Workflow

**Scope:** `x_univ_library`
**Platform:** ServiceNow Washington DC
**Version:** 1.0.0
**Total Templates:** 13 (LIB-NOTIF-001 through LIB-NOTIF-013)

---

## Template Configuration

All templates are configured as ServiceNow Email Template records within the `x_univ_library` scope. Each template uses the university-branded HTML layout with the library logo, header, and footer. Templates support merge variables for dynamic content from the triggering record.

### Common Placeholders

| Variable | Description | Source |
| --- | --- | --- |
| `${student.first_name}` | Student's first name | `u_library_students` → `sys_user.first_name` |
| `${u_number}` | Borrow request number | `u_library_borrow_requests.number` |
| `${u_book.u_title}` | Book title | `u_library_books.u_title` |
| `${u_book.u_author}` | Book author | `u_library_books.u_author` |
| `${portal_url}` | Service Portal base URL | System property |

---

## LIB-NOTIF-001 — Borrow Request Submitted

**Sent when:** Student submits a borrow request via the Service Catalog or Record Producer.
**Recipient:** Student
**Channel:** Email + In-platform notification

| Field | Value |
| --- | --- |
| **Subject** | Your Library Request Has Been Received — ${u_number} |
| **Table** | `u_library_borrow_requests` |
| **Event** | After Insert |
| **Active** | true |

**HTML Body:**

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
</head>
<body style="font-family: 'Segoe UI', Arial, sans-serif; color: #333; line-height: 1.6;">
    <div style="max-width: 600px; margin: 0 auto; padding: 20px;">
        <div style="background-color: #1a5276; padding: 20px; border-radius: 8px 8px 0 0;">
            <h1 style="color: white; margin: 0; font-size: 22px;">University Library</h1>
        </div>
        <div style="background-color: #f8f9fa; padding: 30px; border: 1px solid #dee2e6; border-top: none;">
            <p>Dear <strong>${student.first_name}</strong>,</p>
            <p>Thank you for submitting your library borrow request. We have received your request and it is now pending review by our library team.</p>

            <div style="background-color: white; border-left: 4px solid #1a5276; padding: 15px; margin: 20px 0;">
                <h3 style="margin: 0 0 10px 0; color: #1a5276;">Request Details</h3>
                <table style="width: 100%; border-collapse: collapse;">
                    <tr><td style="padding: 4px 0; color: #666; width: 140px;">Request Number:</td><td style="padding: 4px 0; font-weight: bold;">${u_number}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Book Title:</td><td style="padding: 4px 0;">${u_book.u_title}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Author:</td><td style="padding: 4px 0;">${u_book.u_author}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Requested Pickup:</td><td style="padding: 4px 0;">${u_requested_pickup_date}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Submitted:</td><td style="padding: 4px 0;">${u_submission_date}</td></tr>
                </table>
            </div>

            <h3 style="color: #1a5276;">What Happens Next</h3>
            <p>You will receive a notification when a librarian has reviewed your request. Expected processing time: up to 2 business days.</p>

            <p style="text-align: center; margin: 25px 0;">
                <a href="${portal_url}/library?id=my_requests" style="background-color: #1a5276; color: white; padding: 12px 30px; text-decoration: none; border-radius: 5px; display: inline-block;">View Your Requests</a>
            </p>
        </div>
        <div style="background-color: #e9ecef; padding: 15px; text-align: center; font-size: 12px; color: #666; border-radius: 0 0 8px 8px;">
            <p>University Library — Smart Borrowing System</p>
            <p>This is an automated notification. Please do not reply to this email.</p>
        </div>
    </div>
</body>
</html>
```

---

## LIB-NOTIF-002 — Request Approved

**Sent when:** Librarian approves the borrow request.
**Recipient:** Student
**Channel:** Email + In-platform notification

| Field | Value |
| --- | --- |
| **Subject** | Your Library Request Has Been Approved — ${u_book.u_title} |
| **Table** | `u_library_borrow_requests` |
| **Event** | After Update, status → Approved |
| **Active** | true |

**HTML Body:**

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
</head>
<body style="font-family: 'Segoe UI', Arial, sans-serif; color: #333; line-height: 1.6;">
    <div style="max-width: 600px; margin: 0 auto; padding: 20px;">
        <div style="background-color: #27ae60; padding: 20px; border-radius: 8px 8px 0 0;">
            <h1 style="color: white; margin: 0; font-size: 22px;">Request Approved</h1>
        </div>
        <div style="background-color: #f8f9fa; padding: 30px; border: 1px solid #dee2e6; border-top: none;">
            <p>Dear <strong>${student.first_name}</strong>,</p>
            <p>Great news! Your library borrow request has been <strong style="color: #27ae60;">approved</strong>.</p>

            <div style="background-color: white; border-left: 4px solid #27ae60; padding: 15px; margin: 20px 0;">
                <h3 style="margin: 0 0 10px 0; color: #27ae60;">Approval Details</h3>
                <table style="width: 100%; border-collapse: collapse;">
                    <tr><td style="padding: 4px 0; color: #666; width: 140px;">Request Number:</td><td style="padding: 4px 0; font-weight: bold;">${u_number}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Book Title:</td><td style="padding: 4px 0;">${u_book.u_title}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Author:</td><td style="padding: 4px 0;">${u_book.u_author}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Approved By:</td><td style="padding: 4px 0;">${approver.first_name} ${approver.last_name}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Approval Date:</td><td style="padding: 4px 0;">${approval_date}</td></tr>
                </table>
            </div>

            <div style="background-color: #fff3cd; border: 1px solid #ffc107; border-radius: 5px; padding: 15px; margin: 20px 0;">
                <h3 style="color: #856404; margin: 0 0 5px 0;">Pickup Instructions</h3>
                <p style="margin: 0;">Please collect your book from the library circulation desk within <strong>3 calendar days</strong>. After this deadline, your reservation may be cancelled.</p>
                <p style="margin: 5px 0 0 0;"><strong>Pickup Deadline:</strong> ${pickup_deadline}</p>
                <p style="margin: 5px 0 0 0;"><strong>Library Hours:</strong> Monday–Friday 08:00–20:00, Saturday 09:00–17:00</p>
            </div>

            <p>Please bring your university ID card when collecting your book.</p>

            <p style="text-align: center; margin: 25px 0;">
                <a href="${portal_url}/library?id=my_requests" style="background-color: #27ae60; color: white; padding: 12px 30px; text-decoration: none; border-radius: 5px; display: inline-block;">View My Requests</a>
            </p>
        </div>
        <div style="background-color: #e9ecef; padding: 15px; text-align: center; font-size: 12px; color: #666; border-radius: 0 0 8px 8px;">
            <p>University Library — Smart Borrowing System</p>
        </div>
    </div>
</body>
</html>
```

---

## LIB-NOTIF-003 — Request Rejected

**Sent when:** Librarian rejects the borrow request.
**Recipient:** Student
**Channel:** Email + In-platform notification

| Field | Value |
| --- | --- |
| **Subject** | Library Request Update — ${u_book.u_title} |
| **Table** | `u_library_borrow_requests` |
| **Event** | After Update, status → Rejected |
| **Active** | true |

**HTML Body:**

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
</head>
<body style="font-family: 'Segoe UI', Arial, sans-serif; color: #333; line-height: 1.6;">
    <div style="max-width: 600px; margin: 0 auto; padding: 20px;">
        <div style="background-color: #c0392b; padding: 20px; border-radius: 8px 8px 0 0;">
            <h1 style="color: white; margin: 0; font-size: 22px;">Request Not Approved</h1>
        </div>
        <div style="background-color: #f8f9fa; padding: 30px; border: 1px solid #dee2e6; border-top: none;">
            <p>Dear <strong>${student.first_name}</strong>,</p>
            <p>We regret to inform you that your library borrow request has not been approved at this time.</p>

            <div style="background-color: white; border-left: 4px solid #c0392b; padding: 15px; margin: 20px 0;">
                <h3 style="margin: 0 0 10px 0; color: #c0392b;">Request Details</h3>
                <table style="width: 100%; border-collapse: collapse;">
                    <tr><td style="padding: 4px 0; color: #666; width: 140px;">Request Number:</td><td style="padding: 4px 0; font-weight: bold;">${u_number}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Book Title:</td><td style="padding: 4px 0;">${u_book.u_title}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Reviewed By:</td><td style="padding: 4px 0;">${approver.first_name} ${approver.last_name}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Decision Date:</td><td style="padding: 4px 0;">${decision_date}</td></tr>
                </table>
            </div>

            <div style="background-color: #fce4e4; border: 1px solid #e74c3c; border-radius: 5px; padding: 15px; margin: 20px 0;">
                <h3 style="color: #c0392b; margin: 0 0 5px 0;">Reason</h3>
                <p style="margin: 0;">${rejection_reason}</p>
            </div>

            <h3 style="color: #1a5276;">What You Can Do</h3>
            <ul>
                <li>If you believe this decision is incorrect, please visit the library circulation desk.</li>
                <li>You may submit a new request once the reason above has been addressed.</li>
                <li>Contact your librarian for assistance: <a href="mailto:library@university.edu">library@university.edu</a></li>
            </ul>
        </div>
        <div style="background-color: #e9ecef; padding: 15px; text-align: center; font-size: 12px; color: #666; border-radius: 0 0 8px 8px;">
            <p>University Library — Smart Borrowing System</p>
        </div>
    </div>
</body>
</html>
```

---

## LIB-NOTIF-004 — Book Issued

**Sent when:** Librarian issues the book to the student.
**Recipient:** Student
**Channel:** Email + In-platform notification

| Field | Value |
| --- | --- |
| **Subject** | Your Book Is Ready — ${u_book.u_title} |
| **Table** | `u_library_issuance_records` |
| **Event** | After Insert |
| **Active** | true |

**HTML Body:**

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
</head>
<body style="font-family: 'Segoe UI', Arial, sans-serif; color: #333; line-height: 1.6;">
    <div style="max-width: 600px; margin: 0 auto; padding: 20px;">
        <div style="background-color: #2980b9; padding: 20px; border-radius: 8px 8px 0 0;">
            <h1 style="color: white; margin: 0; font-size: 22px;">Book Issued Successfully</h1>
        </div>
        <div style="background-color: #f8f9fa; padding: 30px; border: 1px solid #dee2e6; border-top: none;">
            <p>Dear <strong>${student.first_name}</strong>,</p>
            <p>Your book has been issued to you. Please take note of your return deadline.</p>

            <div style="background-color: white; border-left: 4px solid #2980b9; padding: 15px; margin: 20px 0;">
                <h3 style="margin: 0 0 10px 0; color: #2980b9;">Issuance Details</h3>
                <table style="width: 100%; border-collapse: collapse;">
                    <tr><td style="padding: 4px 0; color: #666; width: 140px;">Book Title:</td><td style="padding: 4px 0; font-weight: bold;">${u_book.u_title}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Author:</td><td style="padding: 4px 0;">${u_book.u_author}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Issue Date:</td><td style="padding: 4px 0;">${issue_date}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Return By:</td><td style="padding: 4px 0; font-weight: bold; color: #e67e22;">${expected_return_date}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Loan Period:</td><td style="padding: 4px 0;">${loan_period_days} days</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Issued By:</td><td style="padding: 4px 0;">${issued_by.first_name} ${issued_by.last_name}</td></tr>
                </table>
            </div>

            <div style="background-color: #fef9e7; border: 1px solid #f39c12; border-radius: 5px; padding: 15px; margin: 20px 0;">
                <p style="margin: 0; font-weight: bold; color: #e67e22;">Please return this book by ${expected_return_date}. Late returns may affect your borrowing privileges.</p>
            </div>

            <p>You will receive a reminder 3 days before your return deadline.</p>
        </div>
        <div style="background-color: #e9ecef; padding: 15px; text-align: center; font-size: 12px; color: #666; border-radius: 0 0 8px 8px;">
            <p>University Library — Smart Borrowing System</p>
        </div>
    </div>
</body>
</html>
```

---

## LIB-NOTIF-005 — Book Returned

**Sent when:** Librarian processes the book return.
**Recipient:** Student
**Channel:** Email + In-platform notification

| Field | Value |
| --- | --- |
| **Subject** | Return Confirmed — Thank You |
| **Table** | `u_library_return_records` |
| **Event** | After Insert |
| **Active** | true |

**HTML Body:**

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
</head>
<body style="font-family: 'Segoe UI', Arial, sans-serif; color: #333; line-height: 1.6;">
    <div style="max-width: 600px; margin: 0 auto; padding: 20px;">
        <div style="background-color: #2ecc71; padding: 20px; border-radius: 8px 8px 0 0;">
            <h1 style="color: white; margin: 0; font-size: 22px;">Return Confirmed</h1>
        </div>
        <div style="background-color: #f8f9fa; padding: 30px; border: 1px solid #dee2e6; border-top: none;">
            <p>Dear <strong>${student.first_name}</strong>,</p>
            <p>Thank you for returning your library book. Your borrowing record has been updated.</p>

            <div style="background-color: white; border-left: 4px solid #2ecc71; padding: 15px; margin: 20px 0;">
                <h3 style="margin: 0 0 10px 0; color: #2ecc71;">Return Details</h3>
                <table style="width: 100%; border-collapse: collapse;">
                    <tr><td style="padding: 4px 0; color: #666; width: 140px;">Book Title:</td><td style="padding: 4px 0; font-weight: bold;">${u_book.u_title}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Return Date:</td><td style="padding: 4px 0;">${actual_return_date}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Condition:</td><td style="padding: 4px 0;">${return_condition}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Received By:</td><td style="padding: 4px 0;">${received_by.first_name} ${received_by.last_name}</td></tr>
                </table>
            </div>

            <p>You may submit new borrow requests from the Library Portal.</p>

            <p style="text-align: center; margin: 25px 0;">
                <a href="${portal_url}/library" style="background-color: #2ecc71; color: white; padding: 12px 30px; text-decoration: none; border-radius: 5px; display: inline-block;">Visit Library Portal</a>
            </p>
        </div>
        <div style="background-color: #e9ecef; padding: 15px; text-align: center; font-size: 12px; color: #666; border-radius: 0 0 8px 8px;">
            <p>University Library — Smart Borrowing System</p>
        </div>
    </div>
</body>
</html>
```

---

## LIB-NOTIF-006 — Overdue Reminder (Day 1)

**Sent when:** Scheduled job identifies a book 1 day overdue.
**Recipient:** Student
**Channel:** Email + In-platform notification

| Field | Value |
| --- | --- |
| **Subject** | Reminder: Library Book Due Yesterday — ${u_book.u_title} |
| **Table** | `u_library_issuance_records` |
| **Event** | Scheduled Job (daily) |
| **Active** | true |

**HTML Body:**

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
</head>
<body style="font-family: 'Segoe UI', Arial, sans-serif; color: #333; line-height: 1.6;">
    <div style="max-width: 600px; margin: 0 auto; padding: 20px;">
        <div style="background-color: #f39c12; padding: 20px; border-radius: 8px 8px 0 0;">
            <h1 style="color: white; margin: 0; font-size: 22px;">Overdue Reminder</h1>
        </div>
        <div style="background-color: #f8f9fa; padding: 30px; border: 1px solid #dee2e6; border-top: none;">
            <p>Dear <strong>${student.first_name}</strong>,</p>
            <p>This is a friendly reminder that your library book was due yesterday.</p>

            <div style="background-color: white; border-left: 4px solid #f39c12; padding: 15px; margin: 20px 0;">
                <h3 style="margin: 0 0 10px 0; color: #f39c12;">Overdue Details</h3>
                <table style="width: 100%; border-collapse: collapse;">
                    <tr><td style="padding: 4px 0; color: #666; width: 140px;">Book Title:</td><td style="padding: 4px 0; font-weight: bold;">${u_book.u_title}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Due Date:</td><td style="padding: 4px 0;">${expected_return_date}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Days Overdue:</td><td style="padding: 4px 0; color: #e67e22; font-weight: bold;">1 day</td></tr>
                </table>
            </div>

            <p>Please return this book to the library circulation desk as soon as possible. Continued overdue status may affect your ability to borrow new books.</p>

            <p style="font-size: 12px; color: #666;"><strong>Library Hours:</strong> Monday–Friday 08:00–20:00, Saturday 09:00–17:00</p>
        </div>
        <div style="background-color: #e9ecef; padding: 15px; text-align: center; font-size: 12px; color: #666; border-radius: 0 0 8px 8px;">
            <p>University Library — Smart Borrowing System</p>
        </div>
    </div>
</body>
</html>
```

---

## LIB-NOTIF-007 — Overdue Reminder (Day 3)

**Sent when:** Scheduled job identifies a book 3 days overdue.
**Recipient:** Student
**Channel:** Email + In-platform notification

| Field | Value |
| --- | --- |
| **Subject** | Second Reminder: Overdue Library Book — ${u_book.u_title} |
| **Table** | `u_library_issuance_records` |
| **Event** | Scheduled Job (daily) |
| **Active** | true |

**HTML Body:**

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
</head>
<body style="font-family: 'Segoe UI', Arial, sans-serif; color: #333; line-height: 1.6;">
    <div style="max-width: 600px; margin: 0 auto; padding: 20px;">
        <div style="background-color: #e67e22; padding: 20px; border-radius: 8px 8px 0 0;">
            <h1 style="color: white; margin: 0; font-size: 22px;">Second Overdue Notice</h1>
        </div>
        <div style="background-color: #f8f9fa; padding: 30px; border: 1px solid #dee2e6; border-top: none;">
            <p>Dear <strong>${student.first_name}</strong>,</p>
            <p>This is your second reminder. The library book listed below is now <strong style="color: #e74c3c;">3 days overdue</strong>.</p>

            <div style="background-color: #fdebd0; border: 1px solid #e67e22; border-radius: 5px; padding: 15px; margin: 20px 0;">
                <p style="margin: 0; font-weight: bold; color: #c0392b;">Your borrowing privileges are currently suspended until this book is returned.</p>
            </div>

            <div style="background-color: white; border-left: 4px solid #e67e22; padding: 15px; margin: 20px 0;">
                <h3 style="margin: 0 0 10px 0; color: #e67e22;">Overdue Details</h3>
                <table style="width: 100%; border-collapse: collapse;">
                    <tr><td style="padding: 4px 0; color: #666; width: 140px;">Book Title:</td><td style="padding: 4px 0; font-weight: bold;">${u_book.u_title}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Due Date:</td><td style="padding: 4px 0;">${expected_return_date}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Days Overdue:</td><td style="padding: 4px 0; color: #e74c3c; font-weight: bold;">3 days</td></tr>
                </table>
            </div>

            <p>Please return this book immediately. If you are having difficulties, please contact the library: <a href="mailto:library@university.edu">library@university.edu</a></p>
        </div>
        <div style="background-color: #e9ecef; padding: 15px; text-align: center; font-size: 12px; color: #666; border-radius: 0 0 8px 8px;">
            <p>University Library — Smart Borrowing System</p>
        </div>
    </div>
</body>
</html>
```

---

## LIB-NOTIF-008 — Overdue Reminder (Day 7)

**Sent when:** Scheduled job identifies a book 7 days overdue.
**Recipient:** Student
**Channel:** Email + In-platform notification

| Field | Value |
| --- | --- |
| **Subject** | Third Reminder: Overdue Book — Immediate Action Required — ${u_book.u_title} |
| **Table** | `u_library_issuance_records` |
| **Event** | Scheduled Job (daily) |
| **Active** | true |

**HTML Body:**

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
</head>
<body style="font-family: 'Segoe UI', Arial, sans-serif; color: #333; line-height: 1.6;">
    <div style="max-width: 600px; margin: 0 auto; padding: 20px;">
        <div style="background-color: #e74c3c; padding: 20px; border-radius: 8px 8px 0 0;">
            <h1 style="color: white; margin: 0; font-size: 22px;">Overdue — Action Required</h1>
        </div>
        <div style="background-color: #f8f9fa; padding: 30px; border: 1px solid #dee2e6; border-top: none;">
            <p>Dear <strong>${student.first_name}</strong>,</p>
            <p>This is your <strong style="color: #e74c3c;">third reminder</strong>. The book listed below is now <strong style="color: #e74c3c;">7 days overdue</strong>.</p>

            <div style="background-color: #fadbd8; border: 1px solid #e74c3c; border-radius: 5px; padding: 15px; margin: 20px 0;">
                <p style="margin: 0; font-weight: bold; color: #c0392b;">A fine of ${fine_amount} has been applied to your account. Continued non-return will result in escalation to the Library Manager.</p>
            </div>

            <div style="background-color: white; border-left: 4px solid #e74c3c; padding: 15px; margin: 20px 0;">
                <h3 style="margin: 0 0 10px 0; color: #e74c3c;">Overdue Details</h3>
                <table style="width: 100%; border-collapse: collapse;">
                    <tr><td style="padding: 4px 0; color: #666; width: 140px;">Book Title:</td><td style="padding: 4px 0; font-weight: bold;">${u_book.u_title}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Due Date:</td><td style="padding: 4px 0;">${expected_return_date}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Days Overdue:</td><td style="padding: 4px 0; color: #e74c3c; font-weight: bold;">7 days</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Current Fine:</td><td style="padding: 4px 0; color: #e74c3c; font-weight: bold;">$${fine_amount}</td></tr>
                </table>
            </div>

            <p>Please return this book immediately to avoid further escalation.</p>
            <p>Contact the library: <a href="mailto:library@university.edu">library@university.edu</a> | University Library, Circulation Desk</p>
        </div>
        <div style="background-color: #e9ecef; padding: 15px; text-align: center; font-size: 12px; color: #666; border-radius: 0 0 8px 8px;">
            <p>University Library — Smart Borrowing System</p>
        </div>
    </div>
</body>
</html>
```

---

## LIB-NOTIF-009 — Overdue Final Notice (Day 14)

**Sent when:** Scheduled job identifies a book 14 days overdue.
**Recipient:** Student
**Channel:** Email + In-platform notification

| Field | Value |
| --- | --- |
| **Subject** | Final Notice: Overdue Library Book — Action Required — ${u_book.u_title} |
| **Table** | `u_library_issuance_records` |
| **Event** | Scheduled Job (daily) |
| **Active** | true |

**HTML Body:**

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
</head>
<body style="font-family: 'Segoe UI', Arial, sans-serif; color: #333; line-height: 1.6;">
    <div style="max-width: 600px; margin: 0 auto; padding: 20px;">
        <div style="background-color: #c0392b; padding: 20px; border-radius: 8px 8px 0 0;">
            <h1 style="color: white; margin: 0; font-size: 22px;">Final Notice — Escalated</h1>
        </div>
        <div style="background-color: #f8f9fa; padding: 30px; border: 1px solid #dee2e6; border-top: none;">
            <p>Dear <strong>${student.first_name}</strong>,</p>
            <p><strong style="color: #c0392b;">URGENT:</strong> This is a final notice regarding your severely overdue library book. This matter has been escalated to the Library Manager.</p>

            <div style="background-color: #fce4e4; border: 2px solid #c0392b; border-radius: 5px; padding: 15px; margin: 20px 0;">
                <h3 style="margin: 0 0 10px 0; color: #c0392b;">Escalated Overdue Details</h3>
                <table style="width: 100%; border-collapse: collapse;">
                    <tr><td style="padding: 4px 0; color: #666; width: 140px;">Book Title:</td><td style="padding: 4px 0; font-weight: bold;">${u_book.u_title}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Due Date:</td><td style="padding: 4px 0;">${expected_return_date}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Days Overdue:</td><td style="padding: 4px 0; color: #c0392b; font-weight: bold;">14 days</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Status:</td><td style="padding: 4px 0; color: #c0392b; font-weight: bold;">Escalated to Library Manager</td></tr>
                </table>
            </div>

            <p>Please return this book <strong>IMMEDIATELY</strong> and contact the library to resolve this matter:</p>
            <p><strong>Email:</strong> <a href="mailto:library@university.edu">library@university.edu</a><br>
            <strong>Location:</strong> University Library, Circulation Desk (Ground Floor)</p>

            <div style="background-color: #fdebd0; border: 1px solid #e67e22; border-radius: 5px; padding: 15px; margin: 20px 0;">
                <p style="margin: 0;"><strong>Failure to return this book may result in:</strong></p>
                <ul style="margin: 5px 0 0 0;">
                    <li>Suspension of your borrowing account</li>
                    <li>Referral to the student affairs office</li>
                </ul>
            </div>
        </div>
        <div style="background-color: #e9ecef; padding: 15px; text-align: center; font-size: 12px; color: #666; border-radius: 0 0 8px 8px;">
            <p>University Library — Smart Borrowing System</p>
        </div>
    </div>
</body>
</html>
```

---

## LIB-NOTIF-010 — New Request Alert to Librarians

**Sent when:** A student submits a borrow request.
**Recipient:** Librarian group (`x_univ_library.librarian`)
**Channel:** Email + In-platform notification

| Field | Value |
| --- | --- |
| **Subject** | New Borrow Request Awaiting Approval — ${u_book.u_title} |
| **Table** | `u_library_borrow_requests` |
| **Event** | After Insert |
| **Active** | true |

**HTML Body:**

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
</head>
<body style="font-family: 'Segoe UI', Arial, sans-serif; color: #333; line-height: 1.6;">
    <div style="max-width: 600px; margin: 0 auto; padding: 20px;">
        <div style="background-color: #1a5276; padding: 20px; border-radius: 8px 8px 0 0;">
            <h1 style="color: white; margin: 0; font-size: 22px;">New Borrow Request</h1>
        </div>
        <div style="background-color: #f8f9fa; padding: 30px; border: 1px solid #dee2e6; border-top: none;">
            <p>Hello Library Team,</p>
            <p>A new borrow request has been submitted and is awaiting your review.</p>

            <div style="background-color: white; border-left: 4px solid #1a5276; padding: 15px; margin: 20px 0;">
                <h3 style="margin: 0 0 10px 0; color: #1a5276;">Request Details</h3>
                <table style="width: 100%; border-collapse: collapse;">
                    <tr><td style="padding: 4px 0; color: #666; width: 140px;">Request Number:</td><td style="padding: 4px 0; font-weight: bold;">${u_number}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Book:</td><td style="padding: 4px 0;">${u_book.u_title} by ${u_book.u_author}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Student:</td><td style="padding: 4px 0;">${u_student.sys_user.first_name} ${u_student.sys_user.last_name}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Student ID:</td><td style="padding: 4px 0;">${u_student.u_student_id}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Requested Pickup:</td><td style="padding: 4px 0;">${u_requested_pickup_date}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Submitted:</td><td style="padding: 4px 0;">${u_submission_date}</td></tr>
                </table>
            </div>

            <div style="background-color: #eaf2f8; border: 1px solid #2980b9; border-radius: 5px; padding: 15px; margin: 20px 0;">
                <p style="margin: 0;"><strong>SLA Deadline:</strong> ${sla_deadline} (48 hours from submission)</p>
            </div>

            <p style="text-align: center; margin: 25px 0;">
                <a href="${platform_url}/nav_to.do?uri=u_library_borrow_requests.do?sys_id=${sys_id}" style="background-color: #1a5276; color: white; padding: 12px 30px; text-decoration: none; border-radius: 5px; display: inline-block;">Review Request</a>
            </p>

            <p style="font-size: 12px; color: #999;">This request will auto-escalate to the Library Manager if not actioned within 48 hours.</p>
        </div>
        <div style="background-color: #e9ecef; padding: 15px; text-align: center; font-size: 12px; color: #666; border-radius: 0 0 8px 8px;">
            <p>University Library — Smart Borrowing System</p>
        </div>
    </div>
</body>
</html>
```

---

## LIB-NOTIF-011 — Escalation to Library Manager

**Sent when:** A borrow request is not actioned within the 48-hour SLA.
**Recipient:** Library Manager (`x_univ_library.manager`)
**Channel:** Email + In-platform notification

| Field | Value |
| --- | --- |
| **Subject** | Approval Escalated — Action Required — ${u_book.u_title} |
| **Table** | `u_library_borrow_requests` |
| **Event** | Flow: Escalation branch |
| **Active** | true |

**HTML Body:**

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
</head>
<body style="font-family: 'Segoe UI', Arial, sans-serif; color: #333; line-height: 1.6;">
    <div style="max-width: 600px; margin: 0 auto; padding: 20px;">
        <div style="background-color: #e74c3c; padding: 20px; border-radius: 8px 8px 0 0;">
            <h1 style="color: white; margin: 0; font-size: 22px;">Escalation — Action Required</h1>
        </div>
        <div style="background-color: #f8f9fa; padding: 30px; border: 1px solid #dee2e6; border-top: none;">
            <p>Dear Library Manager,</p>
            <p>A borrow request has been <strong style="color: #e74c3c;">escalated</strong> to you as it has not been actioned within the required 48-hour window.</p>

            <div style="background-color: white; border-left: 4px solid #e74c3c; padding: 15px; margin: 20px 0;">
                <h3 style="margin: 0 0 10px 0; color: #e74c3c;">Escalation Details</h3>
                <table style="width: 100%; border-collapse: collapse;">
                    <tr><td style="padding: 4px 0; color: #666; width: 160px;">Request Number:</td><td style="padding: 4px 0; font-weight: bold;">${u_number}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Book:</td><td style="padding: 4px 0;">${u_book.u_title}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Student:</td><td style="padding: 4px 0;">${u_student.sys_user.first_name} ${u_student.sys_user.last_name}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Original Deadline:</td><td style="padding: 4px 0;">${original_sla_deadline}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Escalated At:</td><td style="padding: 4px 0;">${escalation_time}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666; font-weight: bold;">Your Deadline:</td><td style="padding: 4px 0; font-weight: bold; color: #e74c3c;">${manager_sla_deadline} (72 hours from escalation)</td></tr>
                </table>
            </div>

            <p style="text-align: center; margin: 25px 0;">
                <a href="${platform_url}/nav_to.do?uri=u_library_borrow_requests.do?sys_id=${sys_id}" style="background-color: #e74c3c; color: white; padding: 12px 30px; text-decoration: none; border-radius: 5px; display: inline-block;">Review Escalated Request</a>
            </p>
        </div>
        <div style="background-color: #e9ecef; padding: 15px; text-align: center; font-size: 12px; color: #666; border-radius: 0 0 8px 8px;">
            <p>University Library — Smart Borrowing System</p>
        </div>
    </div>
</body>
</html>
```

---

## LIB-NOTIF-012 — Damage Report Created

**Sent when:** A librarian reports damage during return processing.
**Recipient:** Library Manager (`x_univ_library.manager`)
**Channel:** Email + In-platform notification

| Field | Value |
| --- | --- |
| **Subject** | Damage Report — ${u_book.u_title} (${u_book.u_book_id}) |
| **Table** | `u_library_return_records` |
| **Event** | After Insert, condition: `u_condition IN Damaged,Lost` |
| **Active** | true |

**HTML Body:**

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
</head>
<body style="font-family: 'Segoe UI', Arial, sans-serif; color: #333; line-height: 1.6;">
    <div style="max-width: 600px; margin: 0 auto; padding: 20px;">
        <div style="background-color: #8e44ad; padding: 20px; border-radius: 8px 8px 0 0;">
            <h1 style="color: white; margin: 0; font-size: 22px;">Damage Report</h1>
        </div>
        <div style="background-color: #f8f9fa; padding: 30px; border: 1px solid #dee2e6; border-top: none;">
            <p>Dear Library Manager,</p>
            <p>A book has been reported as <strong style="color: #8e44ad;">${return_condition}</strong> during return processing. Please review the details below.</p>

            <div style="background-color: white; border-left: 4px solid #8e44ad; padding: 15px; margin: 20px 0;">
                <h3 style="margin: 0 0 10px 0; color: #8e44ad;">Damage Report Details</h3>
                <table style="width: 100%; border-collapse: collapse;">
                    <tr><td style="padding: 4px 0; color: #666; width: 140px;">Book Title:</td><td style="padding: 4px 0; font-weight: bold;">${u_book.u_title}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Book ID:</td><td style="padding: 4px 0;">${u_book.u_book_id}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Author:</td><td style="padding: 4px 0;">${u_book.u_author}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Condition Reported:</td><td style="padding: 4px 0; font-weight: bold; color: #8e44ad;">${return_condition}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Student:</td><td style="padding: 4px 0;">${u_student.sys_user.first_name} ${u_student.sys_user.last_name}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Student ID:</td><td style="padding: 4px 0;">${u_student.u_student_id}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Processed By:</td><td style="padding: 4px 0;">${processed_by.first_name} ${processed_by.last_name}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Return Date:</td><td style="padding: 4px 0;">${actual_return_date}</td></tr>
                </table>
            </div>

            <div style="background-color: #f5eef8; border: 1px solid #8e44ad; border-radius: 5px; padding: 15px; margin: 20px 0;">
                <h3 style="margin: 0 0 5px 0; color: #8e44ad;">Comments from Librarian</h3>
                <p style="margin: 0; font-style: italic;">${comments}</p>
            </div>

            <p style="text-align: center; margin: 25px 0;">
                <a href="${platform_url}/nav_to.do?uri=u_library_return_records.do?sys_id=${sys_id}" style="background-color: #8e44ad; color: white; padding: 12px 30px; text-decoration: none; border-radius: 5px; display: inline-block;">View Damage Report</a>
            </p>
        </div>
        <div style="background-color: #e9ecef; padding: 15px; text-align: center; font-size: 12px; color: #666; border-radius: 0 0 8px 8px;">
            <p>University Library — Smart Borrowing System</p>
        </div>
    </div>
</body>
</html>
```

---

## LIB-NOTIF-013 — Admin Follow-up Required

**Sent when:** An escalated approval breaches the 72-hour manager SLA.
**Recipient:** `library_admin` group
**Channel:** Email + In-platform notification

| Field | Value |
| --- | --- |
| **Subject** | Admin Follow-up Required — Borrow Request ${u_number} |
| **Table** | `u_library_borrow_requests` |
| **Event** | Flow: 72-hour SLA breach branch |
| **Active** | true |

**HTML Body:**

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
</head>
<body style="font-family: 'Segoe UI', Arial, sans-serif; color: #333; line-height: 1.6;">
    <div style="max-width: 600px; margin: 0 auto; padding: 20px;">
        <div style="background-color: #2c3e50; padding: 20px; border-radius: 8px 8px 0 0;">
            <h1 style="color: white; margin: 0; font-size: 22px;">Admin Follow-up Required</h1>
        </div>
        <div style="background-color: #f8f9fa; padding: 30px; border: 1px solid #dee2e6; border-top: none;">
            <p>Dear Library Administrator,</p>
            <p>A borrow request has breached all SLA deadlines and requires <strong style="color: #e74c3c;">administrative intervention</strong>.</p>

            <div style="background-color: #fce4e4; border: 2px solid #c0392b; border-radius: 5px; padding: 15px; margin: 20px 0;">
                <h3 style="margin: 0 0 10px 0; color: #c0392b;">SLA Breach Details</h3>
                <table style="width: 100%; border-collapse: collapse;">
                    <tr><td style="padding: 4px 0; color: #666; width: 170px;">Request Number:</td><td style="padding: 4px 0; font-weight: bold;">${u_number}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Book Title:</td><td style="padding: 4px 0;">${u_book.u_title}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Student:</td><td style="padding: 4px 0;">${u_student.sys_user.first_name} ${u_student.sys_user.last_name}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Submitted:</td><td style="padding: 4px 0;">${u_submission_date}</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Initial SLA:</td><td style="padding: 4px 0;">48 hours</td></tr>
                    <tr><td style="padding: 4px 0; color: #666;">Escalation SLA:</td><td style="padding: 4px 0;">72 hours</td></tr>
                    <tr><td style="padding: 4px 0; color: #666; font-weight: bold;">Current Status:</td><td style="padding: 4px 0; font-weight: bold; color: #c0392b;">SLA Breached — Over ${hours_since_submission} hours since submission</td></tr>
                </table>
            </div>

            <div style="background-color: #eaf2f8; border: 1px solid #2980b9; border-radius: 5px; padding: 15px; margin: 20px 0;">
                <h3 style="margin: 0 0 5px 0; color: #2980b9;">Required Actions</h3>
                <ol style="margin: 0;">
                    <li>Review the borrow request and associated approval records.</li>
                    <li>Contact the assigned approver or manager for status update.</li>
                    <li>Override or reassign the approval as appropriate.</li>
                    <li>Document the reason for SLA breach and actions taken.</li>
                </ol>
            </div>

            <p style="text-align: center; margin: 25px 0;">
                <a href="${platform_url}/nav_to.do?uri=u_library_borrow_requests.do?sys_id=${sys_id}" style="background-color: #2c3e50; color: white; padding: 12px 30px; text-decoration: none; border-radius: 5px; display: inline-block;">Review and Take Action</a>
            </p>

            <p style="font-size: 12px; color: #999;">This notification was generated automatically by the SLA breach monitoring flow.</p>
        </div>
        <div style="background-color: #e9ecef; padding: 15px; text-align: center; font-size: 12px; color: #666; border-radius: 0 0 8px 8px;">
            <p>University Library — Smart Borrowing System</p>
        </div>
    </div>
</body>
</html>
```

---

## Template Summary

| ID | Event | Recipient | Trigger | SLA |
| --- | --- | --- | --- | --- |
| 001 | Borrow Request Submitted | Student | After Insert | Immediate |
| 002 | Request Approved | Student | After Update (Approved) | Immediate |
| 003 | Request Rejected | Student | After Update (Rejected) | Immediate |
| 004 | Book Issued | Student | After Insert (Issuance) | Immediate |
| 005 | Book Returned | Student | After Insert (Return) | Immediate |
| 006 | Overdue Day 1 | Student | Scheduled Job (daily) | 1 day overdue |
| 007 | Overdue Day 3 | Student | Scheduled Job (daily) | 3 days overdue |
| 008 | Overdue Day 7 | Student | Scheduled Job (daily) | 7 days overdue |
| 009 | Overdue Final Notice | Student | Scheduled Job (daily) | 14 days overdue |
| 010 | New Request Alert | Librarian group | After Insert | Immediate |
| 011 | Escalation to Manager | Library Manager | Flow (48h breach) | 48 hours |
| 012 | Damage Report | Library Manager | After Insert (Damaged/Lost) | Immediate |
| 013 | Admin Follow-up | Admin group | Flow (72h breach) | 120 hours |

---

*References: [FunctionalRequirements.md](FunctionalRequirements.md) — FR-10, [Notifications.md](servicenow/Notifications.md), [FlowDesigner.md](servicenow/FlowDesigner.md)*
