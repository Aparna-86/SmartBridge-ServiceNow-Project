# Smart Library Request Workflow — Acceptance Criteria

**Application:** Smart Library Request Workflow  
**Scope:** x_univ_library  
**Platform:** ServiceNow Washington DC  
**Version:** 1.0.0  

---

## Document Purpose

This document defines the complete acceptance criteria for each functional requirement using the GIVEN/WHEN/THEN behavior-driven development (BDD) format. These criteria were used for test case creation, automated testing, and user acceptance testing (UAT) sign-off.

---

## FR-01: Book Catalog Management

### AC-01.1: Create a Book Record

- **GIVEN** a librarian is logged into the Library Catalog module  
- **WHEN** they fill in all required fields (title, author, ISBN-13, publisher, publication year, total copies) and click Save  
- **THEN** a new book record is created with a unique sys_id, the available copies count equals total copies, and a success confirmation message is displayed

### AC-01.2: Update a Book Record

- **GIVEN** a book record exists in the catalog  
- **WHEN** a librarian modifies the title, author, or total copies fields and clicks Save  
- **THEN** the record is updated, the previous values are recorded in the audit log, and the confirmation message is displayed

### AC-01.3: Retire a Book

- **GIVEN** a book record exists with status Active  
- **WHEN** a librarian sets the status to Inactive and saves  
- **THEN** the book no longer appears in student-facing catalog search results, but the record is preserved in the database

### AC-01.4: Duplicate ISBN Prevention

- **GIVEN** a book with ISBN-13 "978-0-123-45678-9" already exists in the catalog  
- **WHEN** a librarian attempts to create a new book with the same ISBN-13  
- **THEN** the system displays an error message "A book with this ISBN already exists" and prevents the creation

### AC-01.5: Full-Text Search Performance

- **GIVEN** the catalog contains 50,000 book records  
- **WHEN** a user searches for a book by entering a partial title or author name  
- **THEN** the search results are displayed within 2 seconds and include all matching records ranked by relevance

---

## FR-02: Category Management

### AC-02.1: Create Categories

- **GIVEN** a librarian is on the Category Management page  
- **WHEN** they create a top-level category "Science" and a sub-category "Computer Science" under it  
- **THEN** both categories are created with the correct parent-child relationship, and the tree-view reflects the hierarchy

### AC-02.2: Assign Multiple Categories

- **GIVEN** a book record exists and categories "Computer Science" and "Programming" exist  
- **WHEN** a librarian assigns both categories to the book and saves  
- **THEN** the book record shows both category associations, and the book appears in search results for either category

### AC-02.3: Deactivate Category

- **GIVEN** a category "Reference" has 15 books assigned to it  
- **WHEN** a librarian deactivates the category  
- **THEN** the category no longer appears in the category picker for new book assignments, but the 15 existing books retain their category association

### AC-02.4: Reorganize Categories

- **GIVEN** categories exist in a hierarchy  
- **WHEN** a librarian moves a sub-category to a different parent or renames a category  
- **THEN** all book associations are preserved, and the tree-view updates to reflect the reorganization

---

## FR-03: Student Profile Management

### AC-03.1: View Profile in Portal

- **GIVEN** a student is logged into the Service Portal  
- **WHEN** they navigate to My Profile  
- **THEN** their library profile is displayed showing name, student ID, department, year of study, borrowing limit, current checked-out count, and fine balance

### AC-03.2: Update Contact Information

- **GIVEN** a student is viewing their profile on the portal  
- **WHEN** they update their phone number and email preferences and click Save  
- **THEN** the changes are persisted immediately and a success message is displayed

### AC-03.3: Librarian Views Student Profile

- **GIVEN** a librarian is logged into the backend  
- **WHEN** they search for and open a student's profile  
- **THEN** the full profile including borrowing history and fine balance is displayed

### AC-03.4: Real-Time Borrowing Capacity

- **GIVEN** a student has a borrowing limit of 5 and currently has 3 books checked out  
- **WHEN** they view their profile  
- **THEN** the profile shows 2 remaining borrowing slots (calculated in real time)

### AC-03.5: Fine Warning Indicator

- **GIVEN** a student has an outstanding fine balance of $15 exceeding the $10 threshold  
- **WHEN** they view their profile or attempt to submit a borrow request  
- **THEN** a warning indicator is displayed informing them of the outstanding balance

---

## FR-04: Librarian Profile Management

### AC-04.1: Create Librarian Profile

- **GIVEN** a Library Manager is in the User Management module  
- **WHEN** they create a new librarian profile, assign the Librarian role, and set the department  
- **THEN** the user account is created with appropriate roles, and the librarian can access the library application

### AC-04.2: Role-Based Menu Visibility

- **GIVEN** a user has the Librarian role assigned  
- **WHEN** they log into the application  
- **THEN** they see only the Catalog, Issuance, Returns, and Fines modules, and not the Reports or Configuration modules

### AC-04.3: Cannot Self-Promote

- **GIVEN** a Librarian is logged into the application  
- **WHEN** they attempt to modify their own role assignment or approval limits  
- **THEN** the role and approval limit fields are read-only and cannot be edited

### AC-04.4: Department-Based Work Queue

- **GIVEN** a librarian is assigned to the "Science Library" department  
- **WHEN** they view the pending requests queue  
- **THEN** they see only borrow requests for books belonging to categories managed by their department

### AC-04.5: Deactivated Profile

- **GIVEN** a librarian's profile is deactivated by an administrator  
- **WHEN** the librarian attempts to log into the library application  
- **THEN** access is denied and an appropriate message is displayed

---

## FR-05: Borrow Request Submission

### AC-05.1: Successful Submission

- **GIVEN** a student is on the Service Portal and has searched for an available book  
- **WHEN** they click the Borrow button, select a standard 14-day loan period, accept the terms, and submit  
- **THEN** a borrow request record is created with status Pending Approval, the student sees a confirmation with the request ID, and a confirmation email is sent

### AC-05.2: Borrowing Limit Exceeded

- **GIVEN** a student has a borrowing limit of 5 and currently has 5 books checked out  
- **WHEN** they attempt to submit a borrow request for an additional book  
- **THEN** the submission is blocked and an error message states "You have reached your maximum borrowing limit of 5 books"

### AC-05.3: Outstanding Fine Threshold

- **GIVEN** a student has an outstanding fine balance of $25 exceeding the $10 threshold  
- **WHEN** they attempt to submit a borrow request  
- **THEN** the submission is blocked and an error message states "Please clear your outstanding fines before submitting a new request" with a link to the fine payment page

### AC-05.4: Unavailable Book

- **GIVEN** a book has 0 available copies and all copies are checked out  
- **WHEN** a student attempts to submit a borrow request for that book  
- **THEN** the borrow button is disabled with a tooltip "No copies available. Expected return: [date of earliest due copy]"

### AC-05.5: Confirmation Notification

- **GIVEN** a student has successfully submitted a borrow request  
- **WHEN** the submission is processed  
- **THEN** the student receives an email within 60 seconds containing the request ID, book title, submission date, and current status

---

## FR-06: Approval Workflow

### AC-06.1: Automatic Approval Record

- **GIVEN** a student submits a borrow request for a standard book  
- **WHEN** the submission is processed  
- **THEN** an approval record is automatically created and assigned to the appropriate approver based on the workflow configuration

### AC-06.2: Approve with Comment

- **GIVEN** a borrow request is pending approval  
- **WHEN** an approver enters an approval comment and clicks Approve  
- **THEN** the request status changes to Approved, the approval decision and comment are recorded in the audit log, and the student is notified

### AC-06.3: Reject with Reason

- **GIVEN** a borrow request is pending approval  
- **WHEN** an approver enters a rejection reason and clicks Reject  
- **THEN** the request status changes to Rejected, the rejection reason is recorded, and the student sees the reason on the portal and receives a notification

### AC-06.4: Student Visibility

- **GIVEN** a borrow request has been approved or rejected  
- **WHEN** the student views the request on the Service Portal  
- **THEN** the current status and any approver comments are displayed

### AC-06.5: Automatic Transition to Issuance

- **GIVEN** a borrow request is approved  
- **WHEN** the approval is finalized  
- **THEN** the request status transitions to Pending Issuance and appears in the librarian's issuance queue

---

## FR-07: Book Issuance

### AC-07.1: Issuance Queue

- **GIVEN** a librarian is logged into the Issuance module  
- **WHEN** they view the issuance queue  
- **THEN** all requests with status Pending Issuance are displayed, sorted by submission date

### AC-07.2: Correct Due Date Calculation

- **GIVEN** a librarian processes an issuance for a book in the "Standard" category with a 14-day loan period configured  
- **WHEN** the issuance is confirmed on January 15, 2026  
- **THEN** the due date is calculated as January 29, 2026 (14 days from issue date, adjusted for holidays)

### AC-07.3: Available Copies Decrement

- **GIVEN** a book has 5 total copies and 3 available copies  
- **WHEN** a librarian issues one copy  
- **THEN** the available copies count decrements to 2 and the borrowed copies count increments to 3

### AC-07.4: Student Notification

- **GIVEN** a book has been issued to a student  
- **WHEN** the issuance record is saved  
- **THEN** the student receives an email notification with the book title, issue date, and due date

### AC-07.5: Barcode Scanning

- **GIVEN** a librarian is on the issuance form  
- **WHEN** they scan a book's barcode into the barcode field  
- **THEN** the system auto-populates the book details and the corresponding pending request is selected if one exists

---

## FR-08: Book Return Processing

### AC-08.1: Barcode Return

- **GIVEN** a librarian is on the Return Processing page  
- **WHEN** they scan the book barcode  
- **THEN** the system auto-populates the issuance record with the student name, book title, issue date, and due date

### AC-08.2: Fine Calculation

- **GIVEN** a book was due on January 29, 2026, and is returned on February 5, 2026, with a fine rate of $0.50 per day  
- **WHEN** the librarian processes the return  
- **THEN** the system calculates 7 overdue days × $0.50 = $3.50 fine and displays it before final confirmation

### AC-08.3: Available Copies Increment

- **GIVEN** a book had 2 available copies before return  
- **WHEN** the return is processed  
- **THEN** the available copies count increments to 3

### AC-08.4: Return Receipt

- **GIVEN** a return has been processed  
- **WHEN** the return is finalized  
- **THEN** a return receipt is generated showing the book title, return date, any fines assessed, and the updated student balance

### AC-08.5: Partial Return

- **GIVEN** a borrow request had 3 books issued, and only 2 are returned  
- **WHEN** the librarian processes returns for the 2 books  
- **THEN** only those 2 issuance records are closed, the third remains active, and the available copies count increases by 2

---

## FR-09: Overdue Book Management

### AC-09.1: Daily Scheduled Job

- **GIVEN** the system time is 2:00 AM and there are issuance records past their due date  
- **WHEN** the overdue management scheduled job executes  
- **THEN** all overdue records are identified, fines are calculated, and student balances are updated within 30 minutes

### AC-09.2: Fine Calculation Accuracy

- **GIVEN** a book is 10 days overdue and the fine rate is $0.50 per day with a $50 maximum cap  
- **WHEN** the overdue job runs  
- **THEN** the calculated fine is $5.00, and the student's outstanding balance is increased accordingly

### AC-09.3: Borrowing Restriction

- **GIVEN** a student has at least one overdue book  
- **WHEN** they attempt to submit a new borrow request  
- **THEN** the submission is blocked with a message "You have overdue books. Please return them before submitting a new request."

### AC-09.4: Daily Overdue Notification

- **GIVEN** a student has an overdue book  
- **WHEN** the overdue job runs each day  
- **THEN** the student receives a daily reminder notification listing all overdue books and the accumulated fine

### AC-09.5: Long-Overdue Escalation

- **GIVEN** a book is overdue by more than 30 days  
- **WHEN** the overdue job runs  
- **THEN** an escalation task is created and assigned to the Library Manager with the student details, book details, and overdue summary

---

## FR-10: Notification System

### AC-10.1: Request Confirmation

- **GIVEN** a student submits a borrow request  
- **WHEN** the request is saved  
- **THEN** a confirmation email is sent to the student's registered email address within 60 seconds

### AC-10.2: Approval Notification

- **GIVEN** an approver approves or rejects a borrow request  
- **WHEN** the approval action is saved  
- **THEN** a notification is sent to the student within 60 seconds with the decision and any comments

### AC-10.3: Due Date Reminder

- **GIVEN** a book is due in exactly 3 days  
- **WHEN** the due date reminder scheduled job runs  
- **THEN** a reminder notification is sent to the student with the book title and due date

### AC-10.4: Template Customization

- **GIVEN** an administrator has appropriate permissions  
- **WHEN** they navigate to the notification template editor and modify the borrow confirmation template  
- **THEN** the changes are saved and all subsequent borrow confirmation emails use the updated template

### AC-10.5: Delivery Failure Handling

- **GIVEN** a notification fails to send due to an invalid email address  
- **WHEN** the system detects the failure  
- **THEN** the delivery failure is logged in the notification log, and the system retries delivery up to 3 times with a 5-minute interval between attempts

---

## FR-11: Reports and Analytics

### AC-11.1: Report Data Accuracy

- **GIVEN** a Library Manager runs the "Books Issued This Month" report  
- **WHEN** the report is generated  
- **THEN** the report counts match the actual issuance records in the database for the selected period

### AC-11.2: Date Range Filters

- **GIVEN** a Library Manager is viewing the Overdue Summary report  
- **WHEN** they apply a date range filter of January 1, 2026 to January 31, 2026  
- **THEN** the report displays only overdue records with due dates within that range

### AC-11.3: Export Functionality

- **GIVEN** a report is displayed on screen  
- **WHEN** the user clicks Export to CSV or PDF or XLSX  
- **THEN** a file is downloaded containing the same data as displayed, with correct column headers and formatting

### AC-11.4: Most Borrowed Books Ranking

- **GIVEN** books have been issued multiple times  
- **WHEN** the Most Borrowed Books report is generated  
- **THEN** the books are ranked correctly by total issuance count in descending order

### AC-11.5: Librarian Productivity

- **GIVEN** multiple librarians have processed issuances and returns  
- **WHEN** the Librarian Productivity report is generated  
- **THEN** each librarian's transaction count matches the processed records associated with their user ID

---

## FR-12: Dashboards

### AC-12.1: Student Dashboard Scope

- **GIVEN** a student is logged into the Service Portal  
- **WHEN** they view their dashboard  
- **THEN** only their personal checked-out books, requests, fines, and borrowing limit are displayed

### AC-12.2: Real-Time Librarian Dashboard

- **GIVEN** a librarian is logged into the backend  
- **WHEN** they view their dashboard  
- **THEN** the pending approvals count, today's issuances, today's returns, and overdue alerts are displayed with real-time counts

### AC-12.3: Manager Dashboard KPI Accuracy

- **GIVEN** a Library Manager views the Manager Dashboard  
- **WHEN** the dashboard renders  
- **THEN** the KPIs match the corresponding report data for the same time period (e.g., daily circulation count matches the Books Issued report)

### AC-12.4: Interactive Chart Drill-Down

- **GIVEN** a chart on any dashboard displays summary data  
- **WHEN** the user clicks on a chart segment (e.g., a bar in the bar chart)  
- **THEN** a drill-down view opens showing the underlying detail records

### AC-12.5: Dashboard Load Time

- **GIVEN** a user navigates to any dashboard  
- **WHEN** the dashboard page loads  
- **THEN** all widgets and charts are fully rendered within 5 seconds

---

## FR-13: Role-Based Access Control

### AC-13.1: Student Module Restriction

- **GIVEN** a user with only the student role is logged in  
- **WHEN** they attempt to navigate to the backend URL for book catalog management  
- **THEN** access is denied and an authorization error is displayed

### AC-13.2: Librarian Approval Limits

- **GIVEN** a librarian has an approval authority limit of $50  
- **WHEN** they attempt to approve a fine waiver of $75  
- **THEN** the system displays an error indicating the amount exceeds their approval authority

### AC-13.3: Manager Configuration Access

- **GIVEN** a Library Manager is logged in  
- **WHEN** they navigate to the administration module  
- **THEN** they can access reports and user management but cannot modify system-level security configuration

### AC-13.4: Field-Level Student Privacy

- **GIVEN** a student is viewing another student's profile (if accessible via search)  
- **WHEN** the profile loads  
- **THEN** fields containing fine balance, borrowing history, and contact information are hidden or empty

### AC-13.5: Role Inheritance

- **GIVEN** a user has the Senior Librarian role  
- **WHEN** they attempt to perform any function available to the Librarian role  
- **THEN** the action is permitted without additional role assignment

---

## FR-14: Audit Logging

### AC-14.1: Book Update Audit

- **GIVEN** a librarian changes a book's title from "Old Title" to "New Title"  
- **WHEN** the change is saved  
- **THEN** an audit record is created showing "Old Title" as the previous value and "New Title" as the new value

### AC-14.2: Approval Audit Trail

- **GIVEN** an approver approves a borrow request with the comment "Approved per policy"  
- **WHEN** the approval is saved  
- **THEN** the audit log contains the approval decision, comment, timestamp, and approver user ID

### AC-14.3: Fine Adjustment Audit

- **GIVEN** a librarian waives a fine of $15.00 with reason "Student provided evidence of early return"  
- **WHEN** the waiver is saved  
- **THEN** the audit log records the original fine amount, waived amount, adjusted balance, reason, and librarian ID

### AC-14.4: Audit Log Access Restriction

- **GIVEN** a user with the Librarian role (not Manager or Admin)  
- **WHEN** they attempt to access the audit log module  
- **THEN** access is denied

### AC-14.5: Data Retention

- **GIVEN** audit records exist that are older than 7 years  
- **WHEN** the archival scheduled job runs  
- **THEN** the records are moved to the archive table and purged from the active audit table

---

## FR-15: Service Portal Interface

### AC-15.1: Responsive Rendering

- **GIVEN** a student accesses the Service Portal  
- **WHEN** they open it on a desktop (1920x1080), tablet (768x1024), and mobile (375x667) device  
- **THEN** all portal pages render correctly with no overlapping elements, horizontal scrolling, or broken layouts

### AC-15.2: Search with Availability

- **GIVEN** a student is on the Catalog Search page  
- **WHEN** they enter a search keyword  
- **THEN** search results display the book title, author, and availability status (Available: X copies or Checked Out)

### AC-15.3: Contextual Borrow Button

- **GIVEN** a student views a book detail page and the student is ineligible to borrow  
- **WHEN** the page loads  
- **THEN** the Borrow button is disabled and a tooltip explains the reason (e.g., "Borrowing limit reached" or "Outstanding fines")

### AC-15.4: My Books Overdue Highlighting

- **GIVEN** a student has checked-out books, some of which are overdue  
- **WHEN** they view the My Books page  
- **THEN** overdue books are highlighted in red with the overdue days displayed prominently

### AC-15.5: WCAG 2.1 AA Compliance

- **GIVEN** the Service Portal pages are evaluated for accessibility  
- **WHEN** tested using the WAVE evaluation tool and Axe DevTools  
- **THEN** no critical or serious accessibility violations are detected

---

## FR-16: Configuration Management

### AC-16.1: Immediate Configuration Effect

- **GIVEN** an administrator changes max_books_per_student from 5 to 3  
- **WHEN** the configuration is saved  
- **THEN** any subsequent borrow request submission enforces the new limit of 3 books without requiring a system restart

### AC-16.2: Enforcement on New Requests

- **GIVEN** max_books_per_student is changed from 5 to 3  
- **WHEN** a student who already has 4 books checked out attempts to submit a new request  
- **THEN** the submission is blocked with the message "You have reached your maximum borrowing limit of 3 books"

### AC-16.3: Access Restriction

- **GIVEN** a librarian without admin privileges views the application  
- **WHEN** they attempt to access the system properties module  
- **THEN** the module is not visible in their navigation menu

### AC-16.4: Audit Logging of Changes

- **GIVEN** an administrator changes fine_rate_per_day from 0.50 to 1.00  
- **WHEN** the change is saved  
- **THEN** the audit log records the previous value (0.50), new value (1.00), change timestamp, and administrator user ID

### AC-16.5: Validation of Values

- **GIVEN** an administrator attempts to set max_books_per_student to -1  
- **WHEN** they try to save the property  
- **THEN** the system displays a validation error "Value must be between 1 and 20" and does not save the change

---

## FR-17: Administration Module

### AC-17.1: Role Assignment

- **GIVEN** an administrator is in the User Management section  
- **WHEN** they select a user, assign the Librarian role, and save  
- **THEN** the user immediately gains the Librarian role and can access librarian modules

### AC-17.2: CSV Import with Validation

- **GIVEN** an administrator uploads a CSV file containing 1,000 book records  
- **WHEN** the import process completes  
- **THEN** a validation report is displayed showing the number of successful imports, failed records with error descriptions, and total processing time

### AC-17.3: Bulk Update with Preview

- **GIVEN** an administrator selects all books in the "Computer Science" category  
- **WHEN** they set the shelf location to "CS-3" and click Preview  
- **THEN** the system shows a preview of how many records will be updated and the new values before committing

### AC-17.4: Fine Waiver Audit Trail

- **GIVEN** a librarian processes a fine waiver of $25.00 for a student  
- **WHEN** the waiver is submitted with reason "First-time offender"  
- **THEN** the fine record shows the original amount ($25.00), waived amount ($25.00), adjusted balance ($0.00), waiver reason, and processing librarian

### AC-17.5: Holiday Calendar Due Date Adjustment

- **GIVEN** the university is closed on January 26, 2026 (holiday)  
- **WHEN** a book is issued on January 15, 2026, with a 14-day standard loan period  
- **THEN** the due date is calculated as January 30, 2026 (14 days + 1 day holiday adjustment) instead of January 29, 2026

---

## Acceptance Criteria Traceability Matrix

| FR ID | AC Count | UAT Status |
| ------- | ---------- | ------------ |
| FR-01 | 5 | Passed |
| FR-02 | 4 | Passed |
| FR-03 | 5 | Passed |
| FR-04 | 5 | Passed |
| FR-05 | 5 | Passed |
| FR-06 | 5 | Passed |
| FR-07 | 5 | Passed |
| FR-08 | 5 | Passed |
| FR-09 | 5 | Passed |
| FR-10 | 5 | Passed |
| FR-11 | 5 | Passed |
| FR-12 | 5 | Passed |
| FR-13 | 5 | Passed |
| FR-14 | 5 | Passed |
| FR-15 | 5 | Passed |
| FR-16 | 5 | Passed |
| FR-17 | 5 | Passed |
| **Total** | **85** | **All Passed** |

---

## Document History

| Version | Date | Author | Description |
|---------|------|--------|-------------|
| 1.0 | 2026-01-15 | Project Team | Initial approved version |
