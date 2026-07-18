# Solution Requirements
# Smart Library Request Workflow — SmartBridge × ServiceNow

> **Phase:** 2 — Requirement Analysis  
> **Document:** Solution Requirements  
> **Project:** Smart Library Request Workflow  
> **Version:** 1.0.0  
> **Source of Truth:** [Requirements Specification](../.kiro/specs/smart-library-request-workflow/requirements.md)

---

## 1. Summary

This document provides a consolidated view of all solution requirements — functional and non-functional — derived from the authoritative requirements specification. All requirement IDs trace directly to the `.kiro` requirements document.

---

## 2. Functional Requirements Summary

| Req ID | Module | Title | Priority |
|--------|--------|-------|---------|
| FR-01 | Books | Book Catalog Management | High |
| FR-02 | Categories | Category Management | High |
| FR-03 | Students | Student Profile Management | High |
| FR-04 | Librarians | Librarian Profile Management | High |
| FR-05 | Borrow Requests | Borrow Request Submission | High |
| FR-06 | Approvals | Approval Workflow | High |
| FR-07 | Issuance | Book Issuance | High |
| FR-08 | Returns | Book Return Processing | High |
| FR-09 | Overdue | Overdue Book Management | High |
| FR-10 | Notifications | Notification System | High |
| FR-11 | Reports | Reports and Analytics | Medium |
| FR-12 | Dashboards | Dashboards | Medium |
| FR-13 | Security | Role-Based Access Control | High |
| FR-14 | Audit | Audit Logging | High |
| FR-15 | Portal | Service Portal Interface | High |
| FR-16 | Config | Configuration Management | Medium |
| FR-17 | Admin | Administration Module | Medium |

---

## 3. Non-Functional Requirements Summary

| Req ID | Category | Target |
|--------|----------|--------|
| NFR-01 | Performance | Portal search ≤ 2s; form submit ≤ 3s; notification ≤ 60s |
| NFR-02 | Availability | ≥ 99.5% uptime during operational hours |
| NFR-03 | Security | ACL enforcement, HTTPS, SAML 2.0 SSO, XSS prevention |
| NFR-04 | Scalability | 50k books, 5k students, 500 requests/day |
| NFR-05 | Maintainability | Scoped app `x_univ_library`; Script Includes for shared logic |
| NFR-06 | Usability | WCAG 2.1 AA; mobile-responsive 320px–2560px |
| NFR-07 | Compliance | 7-year record retention; GDPR right-to-erasure support |

---

## 4. Acceptance Criteria Traceability

| Req ID | Key Acceptance Criteria |
|--------|------------------------|
| FR-01 | Unique ISBN; auto-available on create; availability status auto-maintained |
| FR-02 | Unique category names; deactivation warning with book count |
| FR-03 | Auto-create profile on role assignment; default limit = 5 |
| FR-04 | Auto-create profile on role assignment; reassign approvals on deactivation |
| FR-05 | Validate availability, overdue, limit, duplicate; request# = LIB-00000001 |
| FR-06 | 48h SLA escalation; mandatory rejection reason; override with notes |
| FR-07 | Auto-calculate return date; issue date + loan_period_days |
| FR-08 | Restore availability on return; damage report notification to Manager |
| FR-09 | Daily job; Day 1/3/7/14 reminders; Day 14+ escalation to Manager |
| FR-10 | 13 notification templates; retry up to 3x within 15 min; log every send |
| FR-11 | 8 reports; CSV + PDF export; weekly scheduled delivery |
| FR-12 | 5-min refresh; drill-down to reports; student self-service dashboard |
| FR-13 | 4 roles; ACL enforcement; 90-day denial log retention |
| FR-14 | Immutable log; 3-year retention; read-only for all users |
| FR-15 | Portal search ≤ 2s; WCAG 2.1 AA; mobile responsive |
| FR-16 | 8 configuration parameters with defaults; admin-only write access |
| FR-17 | Admin console; role management; manual job trigger; notification log view |

---

## 5. Technical Constraints

| ID | Constraint |
|----|-----------|
| C-01 | Application scope prefix: `x_univ_library` |
| C-02 | No modifications to base ServiceNow tables |
| C-03 | All scripts must pass ServiceNow ATF lint |
| C-04 | Compatible with ServiceNow Washington DC or later |
| C-05 | Service Portal uses Angular framework only; no external JS libraries |
| C-06 | Dashboard data may be delayed up to 5 minutes (PA collection interval) |

---

## 6. Assumptions

| ID | Assumption |
|----|-----------|
| A-01 | University has ServiceNow subscription including Flow Designer, PA, Service Portal |
| A-02 | A ServiceNow PDI will be provisioned before implementation begins |
| A-03 | University email server supports SMTP relay |
| A-04 | All users have existing ServiceNow accounts or will be provisioned via LDAP |
| A-05 | Book cover images are externally hosted; the system does not store image files |

---

*Full requirements detail: [requirements.md](../.kiro/specs/smart-library-request-workflow/requirements.md)*  
*Phase 3: [Project Design Phase](../3.%20Project%20Design%20Phase/)*
