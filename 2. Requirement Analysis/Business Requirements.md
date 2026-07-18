# Smart Library Request Workflow — Business Requirements Document

**Application:** Smart Library Request Workflow  
**Scope:** x_univ_library  
**Platform:** ServiceNow Washington DC  
**Version:** 1.0.0  

---

## Document Purpose

This document defines the complete set of business requirements for the Smart Library Request Workflow application. Each requirement captures a specific business need, its rationale, the primary stakeholder, and assigned priority. These requirements served as the foundation for all design, development, and testing activities.

---

## Requirement Classification

| Priority | Definition |
| ---------- | ----------- |
| **High** | Critical for go-live; must be delivered in the initial release. |
| **Medium** | Important but may be deferred to a subsequent release without blocking core operations. |
| **Low** | Nice-to-have; implemented if time and resources permit. |

---

## Business Requirements

### BR-01: Book Catalog Management

| Field | Value |
| ------- | ------- |
| **Title** | Book Catalog Management |
| **Description** | The system shall maintain a centralized catalog of all library books, including title, author, ISBN, publisher, publication year, edition, total copies, available copies, shelf location, and cover image. Librarians shall be able to add, update, retire, and search books through both the backend interface and the Service Portal. |
| **Rationale** | A single authoritative book catalog eliminates duplicate records, reduces manual errors, and provides real-time inventory visibility to both library staff and students. |
| **Stakeholder** | Library Manager, Librarian |
| **Priority** | **High** |

### BR-02: Category Management

| Field | Value |
| ------- | ------- |
| **Title** | Category Management |
| **Description** | The system shall support a hierarchical category taxonomy for books. Categories shall include sub-categories (e.g., "Science > Computer Science > Programming Languages"). Librarians shall be able to create, edit, merge, and deactivate categories. |
| **Rationale** | Well-organized categories enable students to browse books by subject area and help librarians maintain a structured catalog that aligns with academic curricula. |
| **Stakeholder** | Library Manager |
| **Priority** | **High** |

### BR-03: Student Profile Management

| Field | Value |
| ------- | ------- |
| **Title** | Student Profile Management |
| **Description** | The system shall maintain student profiles linked to the ServiceNow User table. Each profile shall include student ID, department, year of study, contact information, borrowing limit, current checked-out count, fine balance, and borrowing history. Students shall be able to view and update their profiles through the Service Portal. |
| **Rationale** | Accurate student profiles are essential for enforcing borrowing policies, calculating fines, and providing personalized library services. Integration with the existing user identity store eliminates duplicate account management. |
| **Stakeholder** | Student, Librarian |
| **Priority** | **High** |

### BR-04: Librarian Profile Management

| Field | Value |
| ------- | ------- |
| **Title** | Librarian Profile Management |
| **Description** | The system shall maintain librarian profiles with role assignments (Librarian, Senior Librarian, Library Manager). Librarians shall be assigned to one or more library branches or departments. Profile data shall include employee ID, department, shift schedule, and system permissions. |
| **Rationale** | Role-based librarian profiles ensure that the right personnel have appropriate system access and that workflows can route requests to the correct librarian for processing. |
| **Stakeholder** | Library Manager, Librarian |
| **Priority** | **High** |

### BR-05: Borrow Request Submission

| Field | Value |
| ------- | ------- |
| **Title** | Borrow Request Submission |
| **Description** | Students shall be able to submit borrow requests through the Service Portal by searching the catalog, selecting books, specifying borrowing duration, and providing any additional notes. The system shall validate eligibility before submission, including borrowing limit checks, fine balance checks, and book availability checks. |
| **Rationale** | A self-service borrow request mechanism reduces the administrative burden on librarians, empowers students to initiate requests at their convenience, and enforces borrowing policies at the point of submission. |
| **Stakeholder** | Student, Librarian |
| **Priority** | **High** |

### BR-06: Approval Workflow

| Field | Value |
| ------- | ------- |
| **Title** | Approval Workflow |
| **Description** | The system shall implement a configurable multi-step approval workflow for borrow requests. The workflow shall route requests through the appropriate approval chain based on book type, student profile, and borrowing duration. Librarians and Library Managers shall be able to approve, reject, or request modifications to pending requests. |
| **Rationale** | An automated approval workflow enforces consistent business rules, reduces manual routing overhead, and provides audit trails for all approval decisions. Different book categories may require different approval chains. |
| **Stakeholder** | Librarian, Library Manager |
| **Priority** | **High** |

### BR-07: Book Issuance

| Field | Value |
| ------- | ------- |
| **Title** | Book Issuance |
| **Description** | Upon approval of a borrow request, the system shall automatically create an issuance record. The issuance record shall capture the student, book, issue date, due date, librarian who processed the issuance, and current status. Librarians shall be able to issue books through both the backend interface and by scanning barcodes. |
| **Rationale** | Automated issuance reduces manual data entry, ensures that due dates are calculated consistently based on configured loan periods, and provides real-time tracking of which books are checked out to whom. |
| **Stakeholder** | Librarian, Student |
| **Priority** | **High** |

### BR-08: Book Return Processing

| Field | Value |
| ------- | ------- |
| **Title** | Book Return Processing |
| **Description** | Librarians shall process book returns by scanning the book barcode or selecting the issuance record. The system shall calculate any applicable fines based on the configured fine rate and overdue days, update the book's available copy count, and close the issuance record. A return receipt shall be generated for the student. |
| **Rationale** | Streamlined return processing reduces wait times for students, ensures accurate fine calculation, and immediately updates catalog availability for other students. |
| **Stakeholder** | Librarian, Student |
| **Priority** | **High** |

### BR-09: Overdue Book Management

| Field | Value |
| ------- | ------- |
| **Title** | Overdue Book Management |
| **Description** | The system shall automatically identify overdue books on a daily basis through a scheduled job. Overdue books shall be categorized by overdue duration (1–7 days, 8–30 days, 31+ days). The system shall escalate overdue cases based on duration, apply fines, restrict further borrowing for students with overdue items, and trigger notifications. |
| **Rationale** | Automated overdue management ensures consistent enforcement of library policies, reduces manual tracking effort, and minimizes revenue loss from unreturned books. |
| **Stakeholder** | Librarian, Library Manager |
| **Priority** | **High** |

### BR-10: Notification System

| Field | Value |
| ------- | ------- |
| **Title** | Notification System |
| **Description** | The system shall send email and in-platform notifications for the following events: borrow request submission confirmation, approval or rejection of requests, book issuance confirmation, due date reminders (3 days before due date), overdue notifications (daily after due date), fine assessment notifications, and return confirmation. Notification templates shall be configurable by administrators. |
| **Rationale** | Proactive notifications keep students informed about their library transactions, reduce overdue returns, improve student satisfaction, and reduce the workload on librarians answering status inquiries. |
| **Stakeholder** | Student, Librarian |
| **Priority** | **Medium** |

### BR-11: Reports and Analytics

| Field | Value |
| ------- | ------- |
| **Title** | Reports and Analytics |
| **Description** | The system shall provide pre-built reports covering: books issued per day/week/month, most borrowed books, overdue statistics, fine collection summaries, category-wise circulation, student borrowing patterns, and librarian productivity. Reports shall be exportable to PDF, CSV, and XLSX formats. A scheduled report delivery capability shall be available. |
| **Rationale** | Data-driven insights enable library management to make informed decisions about collection development, staffing, resource allocation, and policy adjustments. |
| **Stakeholder** | Library Manager, Administrator |
| **Priority** | **Medium** |

### BR-12: Dashboards

| Field | Value |
| ------- | ------- |
| **Title** | Dashboards |
| **Description** | The system shall provide role-specific dashboards: Student Dashboard showing checked-out books, due dates, fines, and request status; Librarian Dashboard showing pending requests, today's issues and returns, overdue alerts; Manager Dashboard showing KPIs, circulation trends, and fine revenue. Dashboards shall use ServiceNow's platform analytics capabilities. |
| **Rationale** | Role-specific dashboards provide immediate visibility into relevant information, enabling faster decision-making and reducing the time spent navigating through multiple menus. |
| **Stakeholder** | Student, Librarian, Library Manager |
| **Priority** | **Medium** |

### BR-13: Role-Based Access Control

| Field | Value |
| ------- | ------- |
| **Title** | Role-Based Access Control |
| **Description** | The system shall implement granular role-based access control with the following roles: Student (self-service catalog search, borrow requests, profile viewing), Librarian (catalog management, borrow processing, returns, fine management), Senior Librarian (all Librarian privileges plus approval authority, override capabilities), Library Manager (all Senior Librarian privileges plus reports, dashboards, configuration), and Administrator (system configuration, user management, audit log access). |
| **Rationale** | Role-based access control ensures that users can only perform actions appropriate to their responsibilities, maintaining data security, operational integrity, and compliance with university IT policies. |
| **Stakeholder** | Administrator, Library Manager |
| **Priority** | **High** |

### BR-14: Audit Logging

| Field | Value |
| ------- | ------- |
| **Title** | Audit Logging |
| **Description** | The system shall log all significant operations including: record creation, modification, and deletion; approval and rejection actions; configuration changes; fine adjustments; and user login/logout activities. Each audit record shall capture the timestamp, user, action, record reference, previous and new values, and IP address. Audit logs shall be tamper-evident and retained for a minimum of seven years. |
| **Rationale** | Comprehensive audit logging is required for regulatory compliance, security investigations, dispute resolution, and operational accountability. |
| **Stakeholder** | Administrator, Library Manager |
| **Priority** | **Medium** |

### BR-15: Service Portal Interface

| Field | Value |
| ------- | ------- |
| **Title** | Service Portal Interface |
| **Description** | The system shall provide a dedicated Service Portal widget and page set for students. The portal shall include catalog search and browse, borrow request submission, request status tracking, profile management, fine payment, and borrowing history views. The portal shall be responsive and accessible on desktop, tablet, and mobile devices. |
| **Rationale** | A modern, self-service portal improves the student experience, reduces foot traffic at the library counter, and provides 24/7 access to library services from any device. |
| **Stakeholder** | Student, Librarian |
| **Priority** | **High** |

### BR-16: Configuration Management

| Field | Value |
| ------- | ------- |
| **Title** | Configuration Management |
| **Description** | The system shall expose configurable parameters through a System Properties module. Configurable items shall include: maximum borrowing days per category, maximum books per student, fine rate per day, overdue escalation thresholds, notification timing, approval chain definitions, and catalog display settings. Changes to configuration shall be audit-logged. |
| **Rationale** | Configurable parameters allow the library to adapt system behavior to changing policies without requiring code changes, reducing maintenance costs and enabling business agility. |
| **Stakeholder** | Library Manager, Administrator |
| **Priority** | **Medium** |

### BR-17: Administration Module

| Field | Value |
| ------- | ------- |
| **Title** | Administration Module |
| **Description** | The system shall include an administration module accessible to Administrators and Library Managers. The module shall provide: user management (activate, deactivate, role assignment), system configuration interface, data import/export utilities, catalog bulk update tools, fine adjustment and waiver processing, holiday calendar management for due date calculations, and system health monitoring. |
| **Rationale** | A centralized administration module reduces the operational overhead of managing the library system, provides tools for bulk operations, and enables administrators to maintain system health without technical intervention. |
| **Stakeholder** | Administrator, Library Manager |
| **Priority** | **Medium** |

---

## Requirement Traceability Matrix

| BR ID | Module | Priority | FR Mapping |
| ------- | -------- | ---------- | ------------ |
| BR-01 | Book Catalog | High | FR-01 |
| BR-02 | Category Management | High | FR-02 |
| BR-03 | Student Profile | High | FR-03 |
| BR-04 | Librarian Profile | High | FR-04 |
| BR-05 | Borrow Request | High | FR-05 |
| BR-06 | Approval Workflow | High | FR-06 |
| BR-07 | Book Issuance | High | FR-07 |
| BR-08 | Book Return | High | FR-08 |
| BR-09 | Overdue Management | High | FR-09 |
| BR-10 | Notifications | Medium | FR-10 |
| BR-11 | Reports | Medium | FR-11 |
| BR-12 | Dashboards | Medium | FR-12 |
| BR-13 | Role-Based Access Control | High | FR-13 |
| BR-14 | Audit Logging | Medium | FR-14 |
| BR-15 | Service Portal | High | FR-15 |
| BR-16 | Configuration Management | Medium | FR-16 |
| BR-17 | Administration Module | Medium | FR-17 |

---

## Document History

| Version | Date | Author | Description |
|---------|------|--------|-------------|
| 1.0 | 2026-01-15 | Project Team | Initial approved version |
