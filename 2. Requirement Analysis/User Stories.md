# Smart Library Request Workflow — User Stories

**Application:** Smart Library Request Workflow  
**Scope:** x_univ_library  
**Platform:** ServiceNow Washington DC  
**Version:** 1.0.0  

---

## Document Purpose

This document catalogs all user stories for the Smart Library Request Workflow application. Each story is written from the perspective of a specific user role and describes a feature or capability in terms of who wants it, what they want, and why they want it. These stories guided the agile development process and were used to define sprint backlogs.

---

## Roles Overview

| Role | Description |
| ------ | ------------- |
| **Student** | A university student who borrows books from the library. |
| **Librarian** | A library staff member who processes book requests, issuances, and returns. |
| **Library Manager** | A senior library staff member who manages operations, approvals, and reporting. |
| **Administrator** | An IT administrator who manages system configuration and user access. |

---

## Student Stories

### STU-01: Catalog Search

**As a** Student,  
**I want** to search the library catalog by title, author, or ISBN,  
**So that** I can quickly find books I need without browsing shelves manually.

### STU-02: Browse by Category

**As a** Student,  
**I want** to browse books by category and sub-category,  
**So that** I can discover books in my area of study even when I don't know specific titles.

### STU-03: View Book Details

**As a** Student,  
**I want** to view detailed information about a book including availability status and shelf location,  
**So that** I can decide whether to borrow it and know where to find it.

### STU-04: Submit Borrow Request

**As a** Student,  
**I want** to submit a borrow request for an available book from the Service Portal,  
**So that** I can initiate the borrowing process without visiting the library counter.

### STU-05: Track Request Status

**As a** Student,  
**I want** to track the status of my borrow requests in real time,  
**So that** I know when my request is approved, issued, or rejected without asking library staff.

### STU-06: View Checked-Out Books

**As a** Student,  
**I want** to see a list of all books currently checked out to me with due dates,  
**So that** I can manage my returns and avoid overdue fines.

### STU-07: Receive Notifications

**As a** Student,  
**I want** to receive email notifications for request approvals, due date reminders, and overdue alerts,  
**So that** I stay informed about my library transactions without constantly checking the portal.

### STU-08: View Fine Balance

**As a** Student,  
**I want** to view my outstanding fine balance and fine history,  
**So that** I can keep track of what I owe and make payments promptly.

### STU-09: Update Profile

**As a** Student,  
**I want** to update my contact information and preferences through the portal,  
**So that** my profile stays current and I receive notifications at my preferred email address.

### STU-10: View Borrowing History

**As a** Student,  
**I want** to view my complete borrowing history including past returns,  
**So that** I can keep a personal record of books I have previously read.

---

## Librarian Stories

### LIB-01: Manage Book Catalog

**As a** Librarian,  
**I want** to add, update, and retire book records in the catalog,  
**So that** the catalog remains accurate and reflects the library's current collection.

### LIB-02: Process Issuance

**As a** Librarian,  
**I want** to process book issuances against approved borrow requests by scanning barcodes,  
**So that** I can quickly hand out books to students with minimal data entry.

### LIB-03: Process Returns

**As a** Librarian,  
**I want** to process book returns and automatically calculate any overdue fines,  
**So that** returns are recorded accurately and fines are applied consistently.

### LIB-04: View Pending Requests

**As a** Librarian,  
**I want** to view a queue of pending borrow requests assigned to me,  
**So that** I can prioritize and process requests in a timely manner.

### LIB-05: Approve or Reject Requests

**As a** Librarian,  
**I want** to approve or reject borrow requests with comments,  
**So that** I can control which books are issued and communicate decisions to students.

### LIB-06: Manage Student Profiles

**As a** Librarian,  
**I want** to view and update student profile information,  
**So that** I can assist students with account issues and verify their eligibility.

### LIB-07: Manage Fines

**As a** Librarian,  
**I want** to view, adjust, or waive fines with proper justification,  
**So that** I can handle special circumstances while maintaining an audit trail.

### LIB-08: View Overdue Alerts

**As a** Librarian,  
**I want** to see a dashboard of currently overdue books and alerts for long-overdue items,  
**So that** I can take proactive steps to recover library resources.

---

## Library Manager Stories

### MGR-01: Configure Loan Policies

**As a** Library Manager,  
**I want** to configure borrowing limits, loan periods, and fine rates through system properties,  
**So that** the system adapts to changing library policies without code changes.

### MGR-02: View Reports

**As a** Library Manager,  
**I want** to view reports on circulation, overdue statistics, and fine collections,  
**So that** I can make data-driven decisions about collection development and staffing.

### MGR-03: View Manager Dashboard

**As a** Library Manager,  
**I want** to see a dashboard with KPIs, circulation trends, and overdue breakdowns,  
**So that** I can monitor library operations at a glance and identify issues quickly.

### MGR-04: Manage Approval Workflow

**As a** Library Manager,  
**I want** to configure approval workflows for different book categories and request types,  
**So that** the right level of oversight is applied to each transaction.

### MGR-05: Escalation Management

**As a** Library Manager,  
**I want** to receive escalated notifications for long-overdue books and take follow-up actions,  
**So that** I can intervene in cases that require special attention.

### MGR-06: Holiday Calendar Management

**As a** Library Manager,  
**I want** to configure university holidays and non-working days,  
**So that** due dates and fine calculations correctly account for days the library is closed.

### MGR-07: Bulk Catalog Operations

**As a** Library Manager,  
**I want** to perform bulk updates to book records and import new books via CSV,  
**So that** I can efficiently manage large catalog changes without manual data entry.

---

## Administrator Stories

### ADM-01: Manage User Roles

**As an** Administrator,  
**I want** to assign and revoke library roles for all users,  
**So that** the right people have appropriate system access based on their job responsibilities.

### ADM-02: View Audit Logs

**As an** Administrator,  
**I want** to view and search audit logs for all application operations,  
**So that** I can investigate security incidents and policy violations.

### ADM-03: Configure System Properties

**As an** Administrator,  
**I want** to configure all application settings including loan periods, fine rates, and notification timing,  
**So that** the system operates according to university policies.

### ADM-04: Monitor System Health

**As an** Administrator,  
**I want** to view the status of scheduled jobs, notification queues, and integration endpoints,  
**So that** I can identify and resolve system issues before they impact users.

### ADM-05: Data Import and Export

**As an** Administrator,  
**I want** to import book and user data in bulk from CSV files with validation reporting,  
**So that** I can migrate data from legacy systems and perform initial system setup efficiently.

### ADM-06: Manage Holiday Calendar

**As an** Administrator,  
**I want** to configure the holiday calendar used for due date calculations,  
**So that** fine calculations respect official university closures.

### ADM-07: Security Configuration

**As an** Administrator,  
**I want** to configure session timeout, password policies, and security settings for the application,  
**So that** the system meets the university's information security requirements.

---

## Story Mapping Summary

| Epic | Stories |
| ------ | --------- |
| **Catalog Management** | STU-01, STU-02, STU-03, LIB-01, MGR-07 |
| **Borrow Requests** | STU-04, STU-05, LIB-04, LIB-05, MGR-04 |
| **Issuance & Returns** | STU-06, LIB-02, LIB-03, MGR-05 |
| **Overdue Management** | LIB-08, MGR-05, STU-07 |
| **Notifications** | STU-07, MGR-05 |
| **Fines & Payments** | STU-08, LIB-07 |
| **User Management** | STU-09, STU-10, LIB-06, ADM-01 |
| **Reports & Dashboards** | MGR-02, MGR-03 |
| **Configuration** | MGR-01, MGR-04, MGR-06, ADM-03, ADM-06 |
| **Administration** | ADM-01, ADM-02, ADM-03, ADM-04, ADM-05, ADM-06, ADM-07 |

---

## Story Count Summary

| Role | Count |
| ------ | ------- |
| Student | 10 |
| Librarian | 8 |
| Library Manager | 7 |
| Administrator | 7 |
| **Total** | **32** |

---

## Document History

| Version | Date | Author | Description |
|---------|------|--------|-------------|
| 1.0 | 2026-01-15 | Project Team | Initial approved version |
