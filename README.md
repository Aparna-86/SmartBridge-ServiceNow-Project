# Smart Library Request Workflow

An enterprise ServiceNow scoped application that automates the complete university library book borrowing lifecycle — from catalog browsing through approval, issuance, return, and overdue management.

**Platform:** ServiceNow Washington DC | **Scope:** `x_univ_library` | **Status:** Complete

---

## Repository Structure

```
├── 1. Ideation Phase/
├── 2. Requirement Analysis/
├── 3. Project Design Phase/
├── 4. Project Planning Phase/
├── 5. Project Development Phase/
├── 6. Project Documentation/
└── 7. Project Demonstration/
```

---

### 1. Ideation Phase

Early-stage brainstorming, problem identification, and empathy analysis.

| File | Description |
|------|-------------|
| Brainstorming - Idea Generation - Prioritization.md | Brainstorming session — problem, solution, platform |
| Define Problem Statements.md | Problem statement: manual library ops, no tracking |
| Empathy Map Canvas.md | Empathy map for 4 stakeholders |

---

### 2. Requirement Analysis

Functional and non-functional requirements, user stories, and acceptance criteria.

| File | Description |
|------|-------------|
| Solution Requirements.md | Consolidated functional & non-functional requirements (FR-01 to FR-17) |
| Functional Requirements.md | Detailed functional requirements with acceptance criteria |
| Non-Functional Requirements.md | Performance, security, availability, compliance |
| Business Requirements.md | Business objectives and stakeholder needs |
| User Stories.md | User stories for all actors (Student, Librarian, Manager, Admin) |
| Data Flow Diagrams and User Stories.md | Data flow descriptions and user stories |
| Customer Journey Map.md | End-to-end journey from discovery to return |
| Acceptance Criteria.md | Acceptance criteria for FR-01 through FR-08 |

---

### 3. Project Design Phase

System architecture, database design, ServiceNow configuration, and technical design.

**Architecture & Design**

| File | Description |
|------|-------------|
| Overview.md | Executive summary, scope, stakeholders, success criteria |
| System Architecture.md | System context diagram, ServiceNow platform layers |
| Application Architecture.md | Application structure, UI architecture, data flow |
| Technical Blueprint.md | Naming conventions, implementation phases, scope |
| ER Diagram.md | Entity-relationship diagram for all 5 tables |
| Database Design.md | Full column definitions, field types, indexes |
| Data Dictionary.md | Comprehensive field-level data dictionary |
| Table Design.md | 5 table schemas with relationships |

**Workflow & Logic**

| File | Description |
|------|-------------|
| Approval Workflow.md | Flow Designer 8-step approval flow |
| Server Scripts.md | Business rules + script includes |
| Email Templates.md | 7 email notification templates |
| Security Design.md | 8 ACLs, role hierarchy |
| Audit Logging.md | Mandatory + custom audit config |

**UI & Catalog**

| File | Description |
|------|-------------|
| UI Actions.md | 4 UI actions: approve, reject, mark_returned, cancel |
| Service Catalog.md | Service catalog configuration for borrow request |
| Portal Design.md | Service Portal page + widget design |

**ServiceNow Configuration**

| File | Description |
|------|-------------|
| Business Rules.md | 4 business rules: enforce_limit, auto_assign, validate_status, check_overdue |
| Client Scripts.md | Client scripts for form behavior |
| UI Policies.md | UI policies for field visibility/enforcement |
| Script Includes.md | Reusable script includes |
| Flow Designer.md | Flow Designer configuration |
| Notifications.md | Notification configuration |
| Roles and Permissions.md | Role + ACL configuration |
| Reports.md | Report definitions |
| Dashboards.md | Dashboard layouts |

---

### 4. Project Planning Phase

Implementation planning and deployment strategy.

| File | Description |
|------|-------------|
| Implementation Plan.md | Step-by-step setup: instance, tables, fields, roles, ACLs, scripts, portal, sample data |

---

### 5. Project Development Phase

Testing resources and sample data.

| File | Description |
|------|-------------|
| Test Cases.md | 36 detailed test cases with preconditions, steps, expected results |
| Testing Guide.md | Test plan across 8 categories |
| Sample Data/books.csv | 10 sample books with ISBN, title, author, category |
| Sample Data/categories.csv | 8 library categories |
| Sample Data/students.csv | 5 student profiles |
| Sample Data/librarians.csv | 3 librarian profiles |
| Sample Data/borrow_requests.csv | 8 sample requests in various states |

---

### 6. Project Documentation

Final deliverables, user manuals, and deployment documentation.

| File | Description |
|------|-------------|
| README.md | Project overview, features, architecture, setup |
| User Manual.md | End-user manual for students and librarians |
| Administrator Guide.md | Admin operations, performance tuning, backup/restore |
| Developer Guide.md | Developer setup, coding standards, conventions |
| Deployment Manual.md | 3-phase deployment plan |
| Maintenance Guide.md | Maintenance schedule, monitoring, updates |
| Troubleshooting.md | 20 troubleshooting entries by category |
| CHANGELOG.md | v1.0.0 release notes |
| LICENSE | MIT License |
| Banner.svg | Project banner image |

---

### 7. Project Demonstration

Demo materials, screenshots, and presentation links.

| File | Description |
|------|-------------|
| Presentation Link.md | Google Drive link to presentation |
| Screenshots/ | Application screenshots (5 images) |

---

## Quick Start

1. Import the `x_univ_library` Update Set into a ServiceNow Washington DC instance
2. Assign roles: `student_library`, `librarian_library`, `library_manager`, `library_admin`
3. Load sample data from `5. Project Development Phase/Sample Data/`
4. Configure SMTP for notification delivery
5. Verify with test cases in `5. Project Development Phase/Test Cases.md`

## Roles

| Role | Access |
|------|--------|
| Student | Browse books, submit/manage borrow requests |
| Librarian | Manage inventory, process requests |
| Library Manager | Full operational access + reports |
| Administrator | Full system configuration |

## Technology Stack

| Component | Technology |
|-----------|------------|
| Platform | ServiceNow Washington DC |
| Presentation | Service Portal + Native Forms |
| Business Logic | Flow Designer, Business Rules, Script Includes |
| Automation | 3 Flows, 3 Scheduled Jobs |
| Notifications | 13 templates (email + in-platform) |
| Security | ACLs, Roles, SAML 2.0 SSO |
| Analytics | Performance Analytics (6 indicators) |

---

**Built on ServiceNow | SmartBridge Technologies**
