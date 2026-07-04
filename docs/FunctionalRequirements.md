# Smart Library Request Workflow — Functional Requirements Document

**Application:** Smart Library Request Workflow  
**Scope:** x_univ_library  
**Platform:** ServiceNow Washington DC  
**Version:** 1.0.0  

---

## Document Purpose

This document defines the complete set of functional requirements for the Smart Library Request Workflow application. Each requirement includes a unique identifier, title, detailed description, acceptance criteria, and implementation status. The acceptance criteria defined herein were used to validate each requirement during the testing phase.

---

## Functional Requirements

### FR-01: Book Catalog Management

| Field | Value |
| ------- | ------- |
| **ID** | FR-01 |
| **Title** | Book Catalog Management |
| **Description** | The system shall provide a comprehensive book catalog management feature within the x_univ_library application scope. Librarians shall be able to perform full CRUD operations on book records. The catalog shall include fields for title, author, ISBN-10, ISBN-13, publisher, publication date, edition number, total copies, available copies, shelf location, language, cover image attachment, and active status. The catalog shall support search by keyword, ISBN, author, and title. Duplicate ISBN detection shall prevent duplicate entries. |
| **Acceptance Criteria** | 1. A librarian can create a new book record with all required fields and receive a confirmation message. 2. A librarian can update any editable field on an existing book record. 3. A librarian can retire a book by setting its status to Inactive, which removes it from student-facing search results. 4. The system prevents creation of a book with a duplicate ISBN-13 and displays an appropriate error message. 5. Full-text search across title, author, and ISBN returns results in under 2 seconds for a catalog of 50,000 books. |
| **Priority** | High |
| **Status** | **Complete** |

### FR-02: Category Management

| Field | Value |
| ------- | ------- |
| **ID** | FR-02 |
| **Title** | Category Management |
| **Description** | The system shall maintain a hierarchical category taxonomy for classifying books. Categories shall support parent-child relationships up to five levels deep. Each category record shall include a name, description, parent category reference, active flag, and display order. Librarians shall be able to assign multiple categories to a single book. The system shall provide a tree-view interface for browsing the category hierarchy. |
| **Acceptance Criteria** | 1. A librarian can create a top-level category and a sub-category under it with full metadata. 2. A librarian can assign a book to multiple categories and verify the associations on the book record. 3. The category tree-view renders correctly showing all levels of the hierarchy. 4. Deactivating a category removes it from the book assignment picker but preserves existing associations. 5. A librarian can rename and reorganize categories with all book associations preserved. |
| **Priority** | High |
| **Status** | **Complete** |

### FR-03: Student Profile Management

| Field | Value |
| ------- | ------- |
| **ID** | FR-03 |
| **Title** | Student Profile Management |
| **Description** | The system shall extend the User [sys_user] table with student-specific attributes through a library profile extension. Student profiles shall include: university student ID, department or faculty, year of study (1–6), enrollment status (active/inactive/graduated), maximum borrowing limit, current checked-out count, outstanding fine balance, and phone number. Students shall view and edit their profile through the Service Portal. Librarians shall view and edit all student profiles from the backend. The profile shall display real-time borrowing limit availability. |
| **Acceptance Criteria** | 1. A student can log into the Service Portal and view their library profile with all fields displayed correctly. 2. A student can update their phone number and email preferences through the portal. 3. A librarian can view any student's full profile including borrowing history and fine balance. 4. The profile accurately reflects the current checked-out count and remaining borrowing capacity in real time. 5. A student with an outstanding fine balance exceeding the threshold sees a warning indicator on their profile. |
| **Priority** | High |
| **Status** | **Complete** |

### FR-04: Librarian Profile Management

| Field | Value |
| ------- | ------- |
| **ID** | FR-04 |
| **Title** | Librarian Profile Management |
| **Description** | The system shall manage librarian profiles with role-based permissions. Librarian records shall include: employee ID, department or branch assignment, shift details, role (Librarian, Senior Librarian, Library Manager), and assignable approval authority limits. The system shall enforce that only Library Managers and Administrators can create or modify librarian role assignments. Librarian profiles shall link to the sys_user table for authentication. |
| **Acceptance Criteria** | 1. A Library Manager can create a new librarian profile with role assignment. 2. A user assigned the Librarian role sees only the menus and options appropriate to that role. 3. A Librarian cannot modify their own role assignment or approval limits. 4. Department assignment correctly filters which borrow requests appear in a librarian's queue. 5. A deactivated librarian profile prevents login to the library application module. |
| **Priority** | High |
| **Status** | **Complete** |

### FR-05: Borrow Request Submission

| Field | Value |
| ------- | ------- |
| **ID** | FR-05 |
| **Title** | Borrow Request Submission |
| **Description** | The system shall enable students to submit borrow requests through the Service Portal. The request form shall include: book selection via catalog search, borrowing period (standard or extended), optional notes field, and acknowledgment of library policies. Upon submission, the system shall validate: that the student has not exceeded their borrowing limit, the student has no outstanding fines above the threshold, the selected book has available copies, and the requested duration is within the allowed range for the book's category. Successful submission shall create a Borrow Request record and trigger the approval workflow. |
| **Acceptance Criteria** | 1. A student can search the catalog from the request form, select a book, and submit a borrow request. 2. The system blocks submission and displays an error if the student has reached their borrowing limit. 3. The system blocks submission and displays a fine payment prompt if the student has outstanding fines. 4. The system blocks submission if no available copies exist and displays the expected return date of the next available copy. 5. Upon successful submission, the student receives a confirmation notification with the request ID and current status. |
| **Priority** | High |
| **Status** | **Complete** |

### FR-06: Approval Workflow

| Field | Value |
| ------- | ------- |
| **ID** | FR-06 |
| **Title** | Approval Workflow |
| **Description** | The system shall implement a configurable approval workflow using the ServiceNow Approval Engine. The workflow shall define approval stages based on: book category (standard reference books require librarian approval, rare books require manager approval), borrowing duration (extended periods require additional approval), and student status (first-time borrowers require verification). Approvers shall be able to approve, reject, or request changes with mandatory comments. Rejected requests shall include the reason for rejection visible to the student. Approved requests shall automatically proceed to the issuance stage. |
| **Acceptance Criteria** | 1. A submitted borrow request automatically creates an approval record assigned to the correct approver based on workflow configuration. 2. An approver can approve, reject, or request changes with a comment, and the action is recorded in the audit log. 3. The student sees the current approval status and any approver comments on the portal. 4. A rejected request updates the request status to Rejected and triggers a notification to the student. 5. An approved request automatically transitions to the Pending Issuance state. |
| **Priority** | High |
| **Status** | **Complete** |

### FR-07: Book Issuance

| Field | Value |
| ------- | ------- |
| **ID** | FR-07 |
| **Title** | Book Issuance |
| **Description** | The system shall support book issuance processing by librarians. Upon selection of an approved borrow request, the system shall create an issuance record with: student name, book details, issue date, calculated due date (based on book category's loan period), librarian who processed the issuance, and issuance status. The system shall decrement the book's available copy count upon issuance. Librarians shall be able to issue books via manual selection from a queue or by scanning the student ID and book barcode. |
| **Acceptance Criteria** | 1. A librarian can view a queue of all approved borrow requests pending issuance. 2. Processing an issuance creates the issuance record with the correct due date based on the book category's configured loan period. 3. The book's available copy count decreases by one after successful issuance. 4. The student receives an issuance confirmation notification with the due date. 5. A librarian can scan a barcode to auto-populate the book selection during issuance. |
| **Priority** | High |
| **Status** | **Complete** |

### FR-08: Book Return Processing

| Field | Value |
| ------- | ------- |
| **ID** | FR-08 |
| **Title** | Book Return Processing |
| **Description** | The system shall enable librarians to process book returns. The return interface shall support lookup by issuance record, book barcode scan, or student ID. Upon processing a return, the system shall: calculate any overdue fines based on the actual return date versus the due date, update the issuance record status to Returned, increment the book's available copy count, and generate a return receipt. The system shall handle partial returns for requests with multiple books. |
| **Acceptance Criteria** | 1. A librarian can process a return by scanning the book barcode, and the system auto-populates the issuance details. 2. The system correctly calculates fines for overdue returns based on the configured daily rate. 3. The book's available copy count increases by one after return processing. 4. A return receipt is generated showing the book, return date, any fines assessed, and the updated student balance. 5. The system handles partial returns by only closing the specific issuance record returned. |
| **Priority** | High |
| **Status** | **Complete** |

### FR-09: Overdue Book Management

| Field | Value |
| ------- | ------- |
| **ID** | FR-09 |
| **Title** | Overdue Book Management |
| **Description** | The system shall identify and manage overdue books through a scheduled job that runs daily. The overdue management process shall: identify all issuance records past their due date, calculate fines based on the configured daily rate and number of overdue days, update the student's outstanding fine balance, flag the student for borrowing restriction if overdue items exist, send daily overdue notifications, and escalate cases overdue by more than 30 days to the Library Manager for follow-up action. |
| **Acceptance Criteria** | 1. The scheduled job runs daily and correctly identifies all overdue issuance records. 2. Overdue fines are calculated correctly for each overdue day, respecting the configured maximum fine cap. 3. A student with overdue items is blocked from submitting new borrow requests. 4. The system sends a daily reminder notification to the student for each overdue book. 5. An escalation task is created for the Library Manager when a book is overdue by more than 30 days. |
| **Priority** | High |
| **Status** | **Complete** |

### FR-10: Notification System

| Field | Value |
| ------- | ------- |
| **ID** | FR-10 |
| **Title** | Notification System |
| **Description** | The system shall provide a comprehensive notification framework using ServiceNow's notification capabilities. The notification system shall support email and in-platform notifications for all library transaction events. Notification templates shall be stored in the system and configurable by administrators. The system shall support the following notification triggers: borrow request submitted, request approved, request rejected, book issued, due date reminder (3 days before), daily overdue reminder, fine assessed, return confirmed, and borrowing privilege suspended or restored. |
| **Acceptance Criteria** | 1. A borrow request submission triggers a confirmation email to the student within 60 seconds. 2. An approval notification is sent to the student immediately after an approver acts on a request. 3. The due date reminder is sent exactly 3 calendar days before the book's due date. 4. An administrator can modify the content of any notification template through the template editor. 5. Notification delivery failures are logged and retried up to three times. |
| **Priority** | Medium |
| **Status** | **Complete** |

### FR-11: Reports and Analytics

| Field | Value |
| ------- | ------- |
| **ID** | FR-11 |
| **Title** | Reports and Analytics |
| **Description** | The system shall provide a set of pre-configured reports accessible from the Reports module. Reports shall include: Books Issued (daily, weekly, monthly, yearly), Most Borrowed Books (top 50), Overdue Summary by duration category, Fine Collection Report by month, Category-wise Circulation, Student Borrowing Patterns (by department, year), Librarian Productivity (issues and returns processed per librarian), and Inventory Status Report (active, retired, lost books). All reports shall support date range filters and export to PDF, CSV, and XLSX formats. Report data shall refresh within the standard ServiceNow reporting latency. |
| **Acceptance Criteria** | 1. Each report renders with correct data when run against the current production dataset. 2. Reports respect applied date range filters and return only data within the specified period. 3. Exported reports in CSV, PDF, and XLSX formats contain the same data as displayed on screen. 4. The Most Borrowed Books report correctly ranks books by total issuance count. 5. Librarian Productivity reports accurately reflect the count of transactions processed by each librarian. |
| **Priority** | Medium |
| **Status** | **Complete** |

### FR-12: Dashboards

| Field | Value |
| ------- | ------- |
| **ID** | FR-12 |
| **Title** | Dashboards |
| **Description** | The system shall provide role-specific dashboards using ServiceNow's dashboard and analytics capabilities. The Student Dashboard shall display: currently checked-out books with due dates, pending request status, outstanding fine balance, and borrowing limit usage bar. The Librarian Dashboard shall display: pending approvals count, today's issue queue, today's returns queue, overdue alerts, and recent transactions. The Library Manager Dashboard shall display: daily circulation KPIs, monthly circulation trend chart, overdue breakdown pie chart, fine revenue bar chart, and top borrowed categories. |
| **Acceptance Criteria** | 1. The Student Dashboard renders correctly for student users and shows only their personal data. 2. The Librarian Dashboard displays real-time counts of pending approvals and today's transactions. 3. The Manager Dashboard KPIs match the corresponding report data for the same period. 4. All dashboard charts are interactive and support drill-down to detail records. 5. Dashboards load within 5 seconds for users with typical data volumes. |
| **Priority** | Medium |
| **Status** | **Complete** |

### FR-13: Role-Based Access Control

| Field | Value |
| ------- | ------- |
| **ID** | FR-13 |
| **Title** | Role-Based Access Control |
| **Description** | The system shall implement role-based access control using ServiceNow's native ACL and role infrastructure. The following roles shall be defined within the x_univ_library scope: x_univ_library.student (self-service portal access, own profile view), x_univ_library.librarian (catalog CRUD, issuance and return processing, fine management), x_univ_library.senior_librarian (all librarian privileges plus approval authority, override fine, bulk operations), x_univ_library.manager (all senior librarian privileges plus reports, dashboards, configuration access), and x_univ_library.admin (system configuration, user management, audit log access, all application access). ACL rules shall enforce record-level and field-level security. |
| **Acceptance Criteria** | 1. A user with only the student role cannot access any librarian backend modules. 2. A librarian cannot approve requests that exceed their designated approval authority limits. 3. A Library Manager can access all reports and dashboards but cannot modify system-level configuration restricted to administrators. 4. Field-level ACLs prevent students from viewing other students' fine balances or borrowing history. 5. Role inheritance ensures that Senior Librarians have all permissions of the base Librarian role. |
| **Priority** | High |
| **Status** | **Complete** |

### FR-14: Audit Logging

| Field | Value |
| ------- | ------- |
| **ID** | FR-14 |
| **Title** | Audit Logging |
| **Description** | The system shall maintain comprehensive audit logs for all significant operations within the application. The audit logging mechanism shall leverage ServiceNow's platform audit capabilities supplemented by custom audit records for application-specific events. The following events shall be audited: any create, update, or delete operation on book, category, and issuance records; all approval actions; configuration property changes; fine adjustments and waivers; role assignment changes; and failed login attempts. Each audit record shall contain: timestamp (UTC), user ID, action type, record type and sys_id, previous values, new values, source IP address, and user agent string. |
| **Acceptance Criteria** | 1. Updating a book's title creates an audit record showing the previous and new values. 2. An approval action creates an audit record with the approver's comment and decision. 3. A fine adjustment by a librarian creates an audit record with the reason and previous balance. 4. Audit logs are viewable only by users with the admin or manager role. 5. Audit records older than 7 years are automatically archived and purged from the active table. |
| **Priority** | Medium |
| **Status** | **Complete** |

### FR-15: Service Portal Interface

| Field | Value |
| ------- | ------- |
| **ID** | FR-15 |
| **Title** | Service Portal Interface |
| **Description** | The system shall deliver a dedicated Service Portal experience for students within the existing ServiceNow portal framework. The library portal shall include the following pages and widgets: Library Home (search bar, featured books, quick links), Catalog Search (advanced search with filters by category, author, publication year, language), Book Detail (full book information, availability status, borrow button), My Requests (list view of all borrow requests with status indicators), My Books (currently checked-out books with due dates, renew option), My Fine Balance (fine summary with payment history), and My Profile (personal information, borrowing limit, preferences). The portal shall be fully responsive and meet WCAG 2.1 AA accessibility standards. |
| **Acceptance Criteria** | 1. The Service Portal renders correctly on desktop (1920x1080), tablet (768x1024), and mobile (375x667) viewports. 2. A student can search for a book by keyword and see results with availability status. 3. The borrow button is disabled with a tooltip explanation when the student is ineligible to borrow. 4. The My Books page shows accurate checked-out books with due dates highlighted in red for overdue items. 5. All portal pages pass WCAG 2.1 AA accessibility validation using the WAVE evaluation tool. |
| **Priority** | High |
| **Status** | **Complete** |

### FR-16: Configuration Management

| Field | Value |
| ------- | ------- |
| **ID** | FR-16 |
| **Title** | Configuration Management |
| **Description** | The system shall expose application configuration through sys_properties for runtime modification. The following configuration parameters shall be available: book_loan_period_standard (default 14 days), book_loan_period_extended (default 30 days), max_books_per_student (default 5), fine_rate_per_day (default 0.50), fine_maximum_cap (default 50.00), overdue_escalation_days (default 30), due_date_reminder_days (default 3), max_renewals_allowed (default 2), renewal_period_days (default 7), and borrowing_restriction_threshold (default 10.00). Each property shall include a description, data type, default value, and allowed value range. Configuration changes shall be audit-logged. |
| **Acceptance Criteria** | 1. An administrator can update a configuration property and the change takes effect immediately without restart. 2. Changing max_books_per_student from 5 to 3 immediately enforces the new limit on new borrow requests. 3. A librarian without admin privileges cannot access or modify configuration properties. 4. Each configuration change is recorded in the audit log with the previous and new values. 5. Invalid values outside the allowed range are rejected with a validation error message. |
| **Priority** | Medium |
| **Status** | **Complete** |

### FR-17: Administration Module

| Field | Value |
| ------- | ------- |
| **ID** | FR-17 |
| **Title** | Administration Module |
| **Description** | The system shall provide an administration module accessible from the application navigation menu for users with admin or manager roles. The administration module shall include: User Management (view all users, assign library roles, activate or deactivate accounts, reset borrowing limits), Data Import (CSV import for books, categories, and student profiles with field mapping and validation reporting), Bulk Update (selective bulk update of catalog records with preview), Fine Management (view all fines, adjust or waive fines with reason, view fine adjustment history), Holiday Calendar (configure holidays and non-working days for due date calculations), and System Health (view scheduled job status, notification queue, integration status, and error logs). |
| **Acceptance Criteria** | 1. An administrator can assign or remove library roles for any user, with changes taking effect immediately. 2. A CSV import of 1,000 book records completes with a validation report showing success and error counts. 3. The bulk update tool allows selecting books by category and updating a field with preview before commit. 4. Fine waiver creates an audit trail showing the original fine, waived amount, waiver reason, and approving librarian. 5. The Holiday Calendar configuration correctly excludes configured holidays from due date calculations. |
| **Priority** | Medium |
| **Status** | **Complete** |

---

## Traceability Summary

| FR ID | BR ID | Module | Priority | Status |
| ------- | ------- | -------- | ---------- | -------- |
| FR-01 | BR-01 | Book Catalog | High | Complete |
| FR-02 | BR-02 | Category Management | High | Complete |
| FR-03 | BR-03 | Student Profile | High | Complete |
| FR-04 | BR-04 | Librarian Profile | High | Complete |
| FR-05 | BR-05 | Borrow Request | High | Complete |
| FR-06 | BR-06 | Approval Workflow | High | Complete |
| FR-07 | BR-07 | Book Issuance | High | Complete |
| FR-08 | BR-08 | Book Return | High | Complete |
| FR-09 | BR-09 | Overdue Management | High | Complete |
| FR-10 | BR-10 | Notifications | Medium | Complete |
| FR-11 | BR-11 | Reports | Medium | Complete |
| FR-12 | BR-12 | Dashboards | Medium | Complete |
| FR-13 | BR-13 | Role-Based Access Control | High | Complete |
| FR-14 | BR-14 | Audit Logging | Medium | Complete |
| FR-15 | BR-15 | Service Portal | High | Complete |
| FR-16 | BR-16 | Configuration Management | Medium | Complete |
| FR-17 | BR-17 | Administration Module | Medium | Complete |

---

## Document History

| Version | Date | Author | Description |
|---------|------|--------|-------------|
| 1.0 | 2026-01-15 | Project Team | Initial approved version |
