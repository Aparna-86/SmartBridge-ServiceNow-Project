# Brainstorming — Idea Generation & Prioritization
# Smart Library Request Workflow — SmartBridge × ServiceNow

> **Phase:** 1 — Ideation  
> **Document:** Brainstorming, Idea Generation & Prioritization  
> **Project:** Smart Library Request Workflow  
> **Platform:** ServiceNow Washington DC  
> **Version:** 1.0.0

---

## 1. Problem Discovery Session

### 1.1 How Might We (HMW) Questions

The ideation session started with the following "How Might We" questions generated from initial stakeholder interviews with university library staff and students:

| # | How Might We… |
|---|--------------|
| HMW-01 | How might we help students find and reserve books without visiting the library in person? |
| HMW-02 | How might we reduce the time librarians spend on manual approval and paperwork? |
| HMW-03 | How might we ensure books are returned on time without aggressive follow-up? |
| HMW-04 | How might we give library managers real-time visibility into inventory and demand? |
| HMW-05 | How might we prevent the same student from holding too many books at once? |
| HMW-06 | How might we ensure approval decisions are consistent and auditable? |
| HMW-07 | How might we reduce the number of lost or misplaced books? |
| HMW-08 | How might we onboard new students to the library system automatically? |

---

## 2. Idea Generation

### 2.1 Raw Ideas (Brainstorming Output)

The following ideas were generated in a 45-minute whiteboard brainstorming session:

| ID | Idea | Category |
|----|------|----------|
| ID-01 | Self-service book request portal for students | Portal |
| ID-02 | Automated email notifications for every lifecycle event | Notifications |
| ID-03 | QR code scanning for book issuance and returns | Issuance |
| ID-04 | Automated overdue detection with tiered reminders | Overdue |
| ID-05 | Role-based dashboards for librarians and managers | Analytics |
| ID-06 | ServiceNow Flow Designer for multi-step approvals | Workflow |
| ID-07 | Centralized book catalog with availability indicators | Catalog |
| ID-08 | Borrowing history and reading records per student | History |
| ID-09 | Waitlist / reservation queue for unavailable books | Future |
| ID-10 | Auto-create student profile when role is assigned | Onboarding |
| ID-11 | Scheduled weekly summary reports emailed to managers | Reports |
| ID-12 | Category-based browsing in the student portal | UX |
| ID-13 | SLA escalation to Library Manager after 48 hours | Escalation |
| ID-14 | Damage reporting on book return | Returns |
| ID-15 | Integration with Student Information System (SIS) | Integration |
| ID-16 | Duplicate request prevention | Validation |
| ID-17 | Configurable loan periods per category | Configuration |
| ID-18 | Immutable audit log for compliance | Compliance |
| ID-19 | Performance Analytics dashboard with KPI cards | Analytics |
| ID-20 | Mobile-responsive Service Portal | Accessibility |

---

## 3. Idea Prioritization

### 3.1 Impact vs Effort Matrix

```
HIGH IMPACT
    │
    │  ✅ ID-01 (Portal)      ✅ ID-06 (Flow Designer)
    │  ✅ ID-02 (Notify)      ✅ ID-07 (Catalog)
    │  ✅ ID-04 (Overdue)     ✅ ID-13 (Escalation)
    │  ✅ ID-10 (Onboarding)  ✅ ID-16 (Duplicate)
    │
    │  ⭕ ID-05 (Dashboards)  ⭕ ID-19 (PA)
    │  ⭕ ID-11 (Reports)     ⭕ ID-17 (Loan Config)
    │
    │  🔵 ID-09 (Waitlist)   🔵 ID-03 (QR Code)
    │  🔵 ID-15 (SIS)        🔵 ID-20 (Mobile)
    │
LOW ─────────────────────────────────────── HIGH
    LOW EFFORT                          HIGH EFFORT
```

**Legend:** ✅ = v1 scope | ⭕ = v1 if time allows | 🔵 = v2

### 3.2 MoSCoW Prioritization

| Priority | Ideas | Justification |
|----------|-------|--------------|
| **Must Have** | ID-01, ID-02, ID-04, ID-06, ID-07, ID-10, ID-13, ID-16 | Core workflow requirements; without these the solution has no value |
| **Should Have** | ID-05, ID-08, ID-11, ID-12, ID-17, ID-18, ID-19 | Significantly improve operations but don't block core workflow |
| **Could Have** | ID-14, ID-20 | Useful additions if sprint capacity allows |
| **Won't Have (v1)** | ID-03, ID-09, ID-15 | Deferred to v2 due to complexity or external dependency |

---

## 4. Selected Solution Concept

Based on the prioritization, the team converged on the following solution concept:

> **"A ServiceNow scoped application (`x_univ_library`) that automates the complete book borrowing lifecycle through a student-facing Service Portal, Flow Designer approval workflow with SLA escalation, automated overdue tracking, role-based dashboards, and a comprehensive audit trail."**

### Core Features Selected for v1

1. ✅ Service Portal with book catalog, search, and borrow request form
2. ✅ Service Catalog borrow request item with validation
3. ✅ Flow Designer: Borrow Request Lifecycle (submit → approve → issue → return)
4. ✅ 48-hour SLA escalation to Library Manager
5. ✅ 11 automated notification templates
6. ✅ Daily overdue detection with tiered reminders (Day 1, 3, 7, 14)
7. ✅ 4-role RBAC model with ACL enforcement
8. ✅ 8 pre-built reports + 2 dashboards
9. ✅ Immutable audit log (3-year retention)
10. ✅ Centralized configuration table

---

## 5. Technology Decision

| Decision | Choice | Rationale |
|----------|--------|-----------|
| **Platform** | ServiceNow Washington DC | University already has ServiceNow subscription |
| **Application Type** | Scoped Application (`x_univ_library`) | Isolation, portability, best practices |
| **Portal** | ServiceNow Service Portal (Angular) | Native platform; no external frameworks |
| **Workflow** | Flow Designer | Modern, no-code/low-code; maintainable |
| **Analytics** | Performance Analytics + Report Builder | Native; no BI tool licensing required |
| **Notifications** | ServiceNow Notification Engine | Native SMTP + in-platform |
| **Database** | ServiceNow tables (GlideRecord) | Platform-native; no SQL injection risk |

---

*Next Phase: [2. Requirement Analysis](../2.%20Requirement%20Analysis/)*
