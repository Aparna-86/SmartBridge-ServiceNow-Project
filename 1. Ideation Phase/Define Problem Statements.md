# Define Problem Statements
# Smart Library Request Workflow — SmartBridge × ServiceNow

> **Phase:** 1 — Ideation  
> **Document:** Define Problem Statements  
> **Project:** Smart Library Request Workflow  
> **Version:** 1.0.0

---

## 1. Context

The University Library currently operates using a manual, paper-based book borrowing system. This document formally defines the problem statements identified during stakeholder interviews and site observations, which form the basis for the solution designed in this project.

---

## 2. Primary Problem Statement

> **"University library students and staff lack a centralized, automated digital system for managing the complete book borrowing lifecycle — from catalog discovery through request, approval, issuance, return, and overdue management — resulting in inefficiency, lost books, policy inconsistencies, and poor stakeholder experience."**

---

## 3. Stakeholder Problem Statements

### 3.1 Student Pain Points

| ID | Problem | Impact |
|----|---------|--------|
| SP-01 | Students must physically visit the library to check book availability | Wasted time; students may find book already borrowed |
| SP-02 | No digital mechanism to request or reserve books in advance | Students miss books that others check out first |
| SP-03 | No visibility into request approval status after submission | Students repeatedly call or visit the library for updates |
| SP-04 | No proactive reminders about upcoming return deadlines | Students forget due dates and accumulate overdue status |
| SP-05 | Students don't know their current borrow count or limit | Students are surprised when requests are rejected |

### 3.2 Librarian Pain Points

| ID | Problem | Impact |
|----|---------|--------|
| LP-01 | All borrow requests handled via phone, email, or in-person | No audit trail; decisions are inconsistent |
| LP-02 | No system to enforce borrowing limits per student | Students borrow more books than policy allows |
| LP-03 | Overdue tracking done via manual spreadsheet | Hours of manual work per week; many overdue books go unnoticed |
| LP-04 | Book availability counts updated manually in Excel | Inventory is often inaccurate; books listed as available are actually borrowed |
| LP-05 | No automated reminders sent to students about overdue books | Librarians must make individual phone calls |

### 3.3 Library Manager Pain Points

| ID | Problem | Impact |
|----|---------|--------|
| MP-01 | No centralized dashboard for real-time library metrics | Managers cannot monitor operations without manual data gathering |
| MP-02 | Reports require manual spreadsheet compilation | Weekly reports take 3–4 hours to produce |
| MP-03 | Approval escalations handled verbally | No audit trail for escalated decisions |
| MP-04 | No data on most-borrowed books or student demand patterns | Collection development decisions are based on guesswork |

---

## 4. Root Cause Analysis (5 Whys)

### Why are books frequently lost or long-overdue?

1. **Why?** Students don't return books on time.
2. **Why?** They don't receive reminders about due dates.
3. **Why?** There is no automated notification system.
4. **Why?** The library uses manual processes and spreadsheets.
5. **Why?** There is no centralized library management platform.

**Root Cause:** Absence of a centralized, automated library management system integrated with student communication channels.

---

## 5. Problem Statement Validation

Each problem statement was validated with at least two stakeholders:

| Problem ID | Validated By | Method |
|-----------|-------------|--------|
| SP-01 to SP-05 | 5 students (survey) | Google Form survey |
| LP-01 to LP-05 | 3 librarians | 30-min interviews |
| MP-01 to MP-04 | Library Manager | 60-min workshop |

**Consensus Score:** 4.7/5 — all stakeholders agreed these problems are real and significantly impact daily operations.

---

## 6. Problem Statement → Solution Mapping

| Problem | Solution Feature |
|---------|----------------|
| SP-01: No availability check | Service Portal with real-time availability badges |
| SP-02: No digital request | Service Catalog borrow request item |
| SP-03: No status visibility | "My Requests" portal page with live status |
| SP-04: No due date reminders | Automated notifications: LIB-NOTIF-006 through 009 |
| SP-05: No borrow limit visibility | Student self-service dashboard widget |
| LP-01: No audit trail | Immutable audit log (u_library_audit_log) |
| LP-02: No limit enforcement | Business Rule BR-06 (Borrow Limit Check) |
| LP-03: Manual overdue tracking | Scheduled job + Flow 3 (Overdue Management) |
| LP-04: Manual inventory | Auto-maintained available_copies via BR-09, BR-10, BR-11 |
| LP-05: Manual reminders | Automated overdue notifications (Day 1, 3, 7, 14) |
| MP-01: No dashboard | Library Operations Dashboard (2 dashboards, 9 widgets) |
| MP-02: Manual reports | 8 scheduled/on-demand reports |
| MP-03: No escalation trail | Approval table with override audit trail |
| MP-04: No demand data | Most Borrowed Books report (RPT-002) |

---

*Next Phase: [2. Requirement Analysis](../2.%20Requirement%20Analysis/)*  
*See also: [Empathy Map Canvas](Empathy%20Map%20Canvas.md)*
