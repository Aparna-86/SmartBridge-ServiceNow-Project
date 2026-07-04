# User Manual — Student Guide

# Smart Library Request Workflow — ServiceNow Enterprise Solution

> **Document Type:** User Manual  
> **Version:** 2.0.0  
> **Application Scope:** `x_univ_library`  
> **Audience:** Students  
> **Platform:** ServiceNow Washington DC

---

## 1. Getting Started

### 1.1 Accessing the Library Portal

1. Open your web browser (Chrome, Firefox, or Edge recommended).
2. Navigate to: `https://[instance].service-now.com/library`
3. Log in with your university credentials (same as your email/student portal).

![University Library Portal Home](../../screenshots/student-home.png)

### 1.2 Portal Overview

The Library Home page displays:

- **Search Bar** — Search for books by title, author, ISBN, or category
- **My Requests Card** — Quick link to your active borrow requests
- **Browse Catalog Card** — Link to the full book catalog
- **Notifications Card** — Recent notifications and updates
- **Featured Books** — New arrivals and recommended books

---

## 2. Browsing Books

### 2.1 Search for Books

1. On the Library Home page, type a keyword in the search bar.
2. Press **Enter** or click **Search**.

![Book Catalog Search Results](../../screenshots/student-catalog.png)

### 2.2 Browse by Category

1. Navigate to **Book Catalog** from the main menu.
2. Use the category filter panel on the left to filter by category:

   - Computer Science
   - Literature
   - Science
   - Mathematics
   - History
   - Arts
   - Philosophy
   - Engineering

3. Click a category to show only books in that category.
4. Click **All Categories** to clear the filter.

### 2.3 View Book Details

1. Click on any book card to view full details:

   - **Title** and **Author**
   - **ISBN** and **Publisher**
   - **Publication Year** and **Edition**
   - **Category** and **Location** (shelf/aisle)
   - **Description/Synopsis**
   - **Availability Status** (Available/Unavailable)
   - **Available Copies** count

2. If the book is available, the **Request this Book** button appears.

![Book Detail Page](../../screenshots/student-book-detail.png)

---

## 3. Submitting a Borrow Request

### 3.1 Using the Catalog Item

1. Navigate to **Book Catalog**.
2. Find the book you want to borrow.
3. Click **Request this Book** on the book detail page.
4. The **Borrow a Book** form opens with the book pre-selected.

Alternatively:

1. From the main menu, select **Borrow a Book** (catalog item).
2. Search for and select the book using the book picker.

### 3.2 Completing the Request Form

![Borrow Request Form](../../screenshots/student-request-form.png)

Fill in the following fields:

| Field | Required | Description |
| ------- | ---------- | ------------- |
| **Book** | Yes | Select the book you want to borrow. Only available books are shown. |
| **Student** | Yes | Auto-populated with your name. Read-only. |
| **Requested Pickup Date** | No | Preferred date to pick up the book (1–30 days from today). |
| **Notes** | No | Optional message for the librarian. Maximum 500 characters. |

### 3.3 Submitting the Request

1. Review your selections.
2. Click **Submit**.
3. A confirmation message appears: "Your request has been submitted successfully."
4. You receive a confirmation email (LIB-NOTIF-001) with your request number.

### 3.4 Request Number Format

Each request receives a unique number in the format `LIB-XXXXXXXX` (e.g., `LIB-00000042`). Use this number when communicating with library staff about your request.

---

## 4. Checking Request Status

### 4.1 My Requests Page

1. Navigate to **My Requests** from the main menu.
2. All your borrow requests are displayed in a list.

![My Requests Page](../../screenshots/student-my-requests.png)

### 4.2 Understanding Statuses

| Status | Meaning | What You Can Do |
| -------- | --------- | ----------------- |
| **Pending Approval** | Your request is waiting for a librarian to review it. | You can cancel the request. |
| **Approved** | A librarian has approved your request. | Wait for the book to be issued to you. |
| **Issued** | The book has been handed over to you. | Check the due date. Enjoy reading! |
| **Overdue** | The return date has passed. | Return the book as soon as possible. |
| **Returned** | The book has been returned successfully. | No action needed. |
| **Rejected** | The librarian could not approve your request. | Check the reason provided. You can submit a new request. |
| **Escalated** | The request was escalated to a manager for review. | Wait for the manager's decision. |
| **Cancelled** | You or the librarian cancelled the request. | No action needed. |

### 4.3 Status Indicators

Each request row displays a colored badge:

- ✅ **Approved/Issued** — Green
- ⏳ **Pending Approval** — Blue
- ❌ **Rejected/Cancelled** — Red
- ⚠️ **Overdue** — Orange
- 🔄 **Returned** — Gray

---

## 5. Cancelling a Request

### 5.1 Eligibility

You can cancel a request only when its status is **Pending Approval**.

### 5.2 Cancellation Steps

1. Navigate to **My Requests**.
2. Click on the request you want to cancel.
3. Click the **Cancel Request** button.
4. Confirm the cancellation in the dialog box.
5. The status changes to **Cancelled**.

### 5.3 What Happens After Cancellation

- The request is no longer visible to librarians.
- The book's availability counter is restored (another student can borrow it).
- No notification is sent for cancellations.

---

## 6. Viewing Borrowing History

### 6.1 My Borrowing History Page

1. Navigate to **My Borrowing History** from the main menu.
2. View all your past and present borrow requests.
3. Use the filter controls to sort by status or date.

![Borrowing History Page](../../screenshots/student-history.png)

### 6.2 History Details

Click on any historical request to view:

- Book title and author
- Request submission date
- Approval/rejection date
- Issue date
- Expected return date
- Actual return date
- Any overdue days
- Condition on return

---

## 7. Notifications

### 7.1 Notification Types

You receive notifications for the following events:

| Event | Channel | When |
| ------- | --------- | ------ |
| Request Submitted (LIB-NOTIF-001) | Email + In-platform | Immediately after submission |
| Request Approved (LIB-NOTIF-002) | Email + In-platform | When librarian approves |
| Request Rejected (LIB-NOTIF-003) | Email + In-platform | When librarian rejects |
| Book Issued (LIB-NOTIF-004) | Email + In-platform | When book is handed over |
| Book Returned (LIB-NOTIF-005) | Email + In-platform | When return is processed |
| Overdue Reminder (Day 1, 3, 7, 14) (LIB-NOTIF-006–009) | Email + In-platform | When book is overdue |

### 7.2 Viewing Notifications

1. Click the **Bell icon** in the portal header.
2. A dropdown shows your recent notifications.
3. Click **View All** to see your full notification history.

---

## 8. Profile and Settings

### 8.1 My Profile Page

1. Navigate to **My Profile** from the main menu.
2. View your profile information:

   - **Name** (from your university account)
   - **University ID**
   - **Department**
   - **Enrollment Year**
   - **Max Borrow Limit** — Maximum books you can borrow at once
   - **Active Borrow Count** — Books currently in your possession

![My Profile Page](../../screenshots/student-profile.png)

### 8.2 Borrow Limit Information

- Default max borrow limit: **5 books**
- Your active borrow count shows how many books you currently have.
- When you reach the limit, you must return a book before submitting a new request.

---

## 9. Frequently Asked Questions (Students)

**Q: How long can I keep a borrowed book?**

A: The standard loan period is **14 days**. You can see the due date on your issuance record.

**Q: Can I renew a book?**

A: Renewals are not currently supported. You must return the book and submit a new request.

**Q: What happens if I return a book late?**

A: Overdue reminders are sent at 1, 3, 7, and 14 days past the due date. The return will be marked as overdue.

**Q: Can I borrow a book that is currently unavailable?**

A: No. Only books with available copies can be requested. Check back later.

**Q: Why was my request rejected?**

A: The librarian provides a reason when rejecting a request. Check the request details to view the reason.

---

# User Manual — Librarian Guide

> **Audience:** Librarians and Library Managers

---

## 1. Getting Started

### 1.1 Accessing the System

1. Navigate to `https://[instance].service-now.com/library/operations`
2. Log in with your staff credentials.

### 1.2 Operations Dashboard

The Library Operations page provides:

- **Pending Approvals** — List of requests awaiting your decision
- **Pending Issuances** — Approved requests waiting for physical book handover
- **Overdue Books** — Currently overdue books
- **Quick Actions** — Buttons for common tasks

![Library Operations Dashboard](../../screenshots/librarian-operations.png)

---

## 2. Managing Books

### 2.1 Adding a New Book

1. Navigate to **Library Books** module via the application navigator.
2. Click **New**.
3. Fill in the required fields:

| Field | Description |
| ------- | ------------- |
| **Title** | Full book title |
| **Author** | Primary author name |
| **ISBN** | Unique ISBN (validated for uniqueness) |
| **Category** | Select from active categories |
| **Publisher** | Publisher name |
| **Publication Year** | Year of publication (1000–current) |
| **Edition** | Edition descriptor |
| **Total Copies** | Total number of physical copies |
| **Location** | Shelf/aisle location in the library |
| **Description** | Brief synopsis |

1. Click **Submit**.
2. The `available_copies` is automatically set to equal `total_copies`.

### 2.2 Editing a Book

1. Open the book record.
2. Click the edit icon or double-click a field.
3. Make changes.
4. Click **Save** or **Update**.

### 2.3 Deactivating a Book

1. Open the book record.
2. Set **Active** to `false`.
3. Click **Save**.
4. The book is removed from student-facing search results but remains visible to librarians.

### 2.4 Searching Books

Use the list view search to find books by:

- Title (partial match)
- Author (partial match)
- ISBN (exact match)
- Category (filter)
- Availability status (filter)

---

## 3. Processing Approvals

### 3.1 Viewing Pending Requests

1. Navigate to **Library Borrow Requests**.
2. Filter by status: **Pending Approval**.
3. Review each request's details, including the book, student, and pickup date.

### 3.2 Approving a Request

1. Open a pending borrow request.
2. Click **Approve Request**.
3. The status changes to **Approved**.
4. The student receives an approval notification (LIB-NOTIF-002).
5. The request now appears in the **Pending Issuance** list.

### 3.3 Rejecting a Request

1. Open a pending borrow request.
2. Click **Reject Request**.
3. In the dialog, enter a **Rejection Reason** (required).
4. Click **Confirm**.
5. The status changes to **Rejected**.
6. The student receives a rejection notification (LIB-NOTIF-003).
7. The book's available copies are restored.

### 3.4 Approval SLA

- Librarians have **48 hours** to approve or reject a pending request.
- If no action is taken within 48 hours, the request is **Escalated** to the Library Manager.
- The Library Manager has an additional **72 hours** to decide.
- If still unactioned, an admin follow-up task is created.

---

## 4. Issuing Books

### 4.1 Issuing an Approved Request

1. Navigate to **Library Borrow Requests**.
2. Filter by status: **Approved**.
3. Open the approved request.
4. Click **Issue Book**.
5. Verify the following auto-populated information:

| Field | Source |
| ------- | -------- |
| **Borrow Request** | From the parent request |
| **Book** | From the parent request |
| **Student** | From the parent request |
| **Issued By** | Your librarian profile (auto-filled) |
| **Issue Date** | Current date/time (auto-filled) |
| **Loan Period** | Default 14 days (configurable) |
| **Expected Return Date** | Issue date + loan period (calculated) |

1. Click **Submit** to create the issuance record.
2. The borrow request status changes to **Issued**.
3. Physically hand the book to the student.
4. The student receives an issuance notification (LIB-NOTIF-004).

### 4.2 Printing a Borrow Slip

1. Open the issuance record.
2. Click **Print Borrow Slip**.
3. A printable slip opens with:
   - Request number
   - Book title and author
   - Student name and ID
   - Issue date
   - Expected return date
   - Librarian name

---

## 5. Processing Returns

### 5.1 Initiating a Return

1. Navigate to **Library Borrow Requests**.
2. Filter by status: **Issued**.
3. Open the issued request.
4. Click **Process Return**.

### 5.2 Completing the Return Form

![Return Processing Form](../../screenshots/librarian-return.png)

| Field | Required | Description |
| ------- | ---------- | ------------- |
| **Borrow Request** | Yes | Auto-populated from parent |
| **Actual Return Date** | Yes | Auto-populated with current date/time |
| **Return Condition** | Yes | Select **On Time** or **Overdue** |
| **Overdue Days** | Yes | Auto-calculated if overdue |
| **Damage Reported** | Yes | Check if the book is damaged |
| **Damage Description** | Conditional | Required if damage is reported. Describe the damage. |
| **Received By** | Yes | Your librarian profile (auto-filled) |

1. Click **Submit** to process the return.
2. The borrow request status changes to **Returned**.
3. The book's available copies are incremented.
4. The student receives a return notification (LIB-NOTIF-005).

### 5.3 Creating Return from Issuance

1. Open the issuance record.
2. Click **Create Return from Issuance**.
3. A new return record opens with fields pre-populated from the issuance record.
4. Complete the return form as described above.

---

## 6. Sending Manual Reminders

If a student has an overdue book and the automated reminder did not trigger:

1. Open the overdue borrow request.
2. Click **Send Reminder**.
3. The appropriate overdue notification is sent based on the number of days overdue.

---

## 7. Frequently Asked Questions (Librarians)

**Q: Can I edit a borrow request after it is issued?**

A: Once a request status is **Issued**, most fields become read-only. Changes should be made through the issuance or return records.

**Q: What happens if a book is returned damaged?**

A: Check the **Damage Reported** checkbox and provide a detailed description. The Library Manager is notified automatically (LIB-NOTIF-012).

**Q: Can I override an existing approval decision?**

A: Only Library Managers can override approval decisions. Use the override notes field to document the reason.

**Q: How do I handle a student who claims they returned a book that is still marked as issued?**

A: Check the audit logs for the return record. If the return was not processed, process it manually. If the book was lost, mark the request appropriately and contact the Library Manager.

---

*References: [AdministratorGuide.md](AdministratorGuide.md) | [DeveloperGuide.md](DeveloperGuide.md)*
*See also: [MaintenanceGuide.md](../implementation/MaintenanceGuide.md)*
