# Empathy Map Canvas
# Smart Library Request Workflow — SmartBridge × ServiceNow

> **Phase:** 1 — Ideation  
> **Document:** Empathy Map Canvas  
> **Project:** Smart Library Request Workflow  
> **Version:** 1.0.0

---

## Overview

Empathy maps were created for each of the four primary user personas to ensure the solution design centers on real human needs, pain points, and goals.

---

## Persona 1: The Student (Arjun, 2nd Year Engineering)

```
┌─────────────────────────────────────────────────────────────────────┐
│                        EMPATHY MAP: STUDENT                          │
│                    Arjun — 2nd Year Engineering                      │
├──────────────────────────┬──────────────────────────────────────────┤
│         THINKS 💭        │              FEELS 💛                     │
│                          │                                           │
│ "I hope the book I need  │ Frustrated when the book is already      │
│  is still available"     │ borrowed and no one told him             │
│                          │                                           │
│ "I don't know when my    │ Anxious about forgetting return dates    │
│  book is due back"       │ and losing borrowing privileges          │
│                          │                                           │
│ "I wonder if my request  │ Confused about the approval process;    │
│  was even received"      │ no feedback loop                         │
├──────────────────────────┼──────────────────────────────────────────┤
│          SAYS 🗣️         │              DOES 🏃                     │
│                          │                                           │
│ "Can I check if this     │ Walks to the library to check book       │
│  book is available?"     │ availability manually                    │
│                          │                                           │
│ "I've been waiting for   │ Calls/emails the library to ask about   │
│  my request for 2 days"  │ request status                           │
│                          │                                           │
│ "I forgot to return it,  │ Returns books late because no reminder  │
│  sorry!"                 │ was sent                                  │
├──────────────────────────┼──────────────────────────────────────────┤
│        PAIN POINTS 😣    │              GAINS 🌟                    │
│                          │                                           │
│ No self-service portal   │ 24/7 self-service book browsing          │
│ No real-time availability│ Real-time availability on portal         │
│ No request status updates│ Live request status tracking             │
│ No due date reminders    │ Automated email reminders                 │
│ Can't see borrow history │ Full borrowing history available          │
└──────────────────────────┴──────────────────────────────────────────┘
```

---

## Persona 2: The Librarian (Priya, Senior Librarian)

```
┌─────────────────────────────────────────────────────────────────────┐
│                       EMPATHY MAP: LIBRARIAN                         │
│                    Priya — Senior Librarian                          │
├──────────────────────────┬──────────────────────────────────────────┤
│         THINKS 💭        │              FEELS 💛                     │
│                          │                                           │
│ "I have 30 request       │ Overwhelmed by manual workload           │
│  emails to process today"│                                           │
│                          │                                           │
│ "Which books are overdue?│ Stressed about finding overdue books     │
│  I need to check my      │ scattered across multiple spreadsheets   │
│  spreadsheet"            │                                           │
│                          │                                           │
│ "Did I send that student │ Worried about inconsistency in her       │
│  a reminder?"            │ process; no confidence in completeness   │
├──────────────────────────┼──────────────────────────────────────────┤
│          SAYS 🗣️         │              DOES 🏃                     │
│                          │                                           │
│ "I wish I had a system   │ Updates spreadsheets manually for every │
│  that did this for me"   │ borrow transaction                       │
│                          │                                           │
│ "I can't keep up with    │ Sends individual emails for overdue      │
│  all these emails"       │ reminders one by one                     │
│                          │                                           │
│ "I'm not sure if the     │ Manually checks book availability before │
│  inventory is up to date"│ approving each request                   │
├──────────────────────────┼──────────────────────────────────────────┤
│        PAIN POINTS 😣    │              GAINS 🌟                    │
│                          │                                           │
│ Manual approval process  │ Structured approval queue in ServiceNow │
│ Manual overdue tracking  │ Automated daily overdue detection        │
│ No availability dashboard│ Real-time inventory dashboard            │
│ No audit trail           │ Full approval history + audit log        │
│ Time on status inquiries │ Students self-serve via portal           │
└──────────────────────────┴──────────────────────────────────────────┘
```

---

## Persona 3: The Library Manager (Dr. Meena, Library Director)

```
┌─────────────────────────────────────────────────────────────────────┐
│                    EMPATHY MAP: LIBRARY MANAGER                      │
│                   Dr. Meena — Library Director                       │
├──────────────────────────┬──────────────────────────────────────────┤
│         THINKS 💭        │              FEELS 💛                     │
│                          │                                           │
│ "How many books are      │ Frustrated that reports take half a day  │
│  currently overdue       │ to produce manually                      │
│  this month?"            │                                           │
│                          │                                           │
│ "Which books should I    │ Unsure about collection purchasing       │
│  order more copies of?"  │ decisions without demand data            │
│                          │                                           │
│ "Was the right process   │ Concerned about audit risk from          │
│  followed for that       │ undocumented approval overrides          │
│  escalation?"            │                                           │
├──────────────────────────┼──────────────────────────────────────────┤
│          SAYS 🗣️         │              DOES 🏃                     │
│                          │                                           │
│ "I need to know how the  │ Manually compiles reports from           │
│  library is performing   │ spreadsheets weekly                      │
│  at a glance"            │                                           │
│                          │                                           │
│ "Make sure the approval  │ Verbally intervenes in approval          │
│  is handled properly"    │ escalations with no documentation        │
├──────────────────────────┼──────────────────────────────────────────┤
│        PAIN POINTS 😣    │              GAINS 🌟                    │
│                          │                                           │
│ No operational dashboard │ Live KPI dashboard: active, overdue,    │
│ Manual weekly reports    │ available, pending counts                 │
│ No escalation audit trail│ Documented escalation history in system  │
│ No demand analytics      │ "Most Borrowed Books" report (RPT-002)   │
│ No approval override log │ Full override trail in Approvals table   │
└──────────────────────────┴──────────────────────────────────────────┘
```

---

## Persona 4: The Administrator (Vikram, IT Admin)

```
┌─────────────────────────────────────────────────────────────────────┐
│                     EMPATHY MAP: ADMINISTRATOR                       │
│                     Vikram — IT Administrator                        │
├──────────────────────────┬──────────────────────────────────────────┤
│         THINKS 💭        │              FEELS 💛                     │
│                          │                                           │
│ "If the library changes  │ Cautious about deploying changes         │
│  policy, will it break   │ that might affect system behavior        │
│  the system?"            │                                           │
│                          │                                           │
│ "How do I prove no one   │ Responsible for compliance and audit     │
│  tampered with the data?"│ readiness; no tools to easily verify    │
├──────────────────────────┼──────────────────────────────────────────┤
│          SAYS 🗣️         │              DOES 🏃                     │
│                          │                                           │
│ "I need to be able to    │ Manages users and roles manually in      │
│  audit what happened"    │ ServiceNow without application support   │
│                          │                                           │
│ "The configuration       │ Edits system properties directly in      │
│  should be centralized"  │ the platform with no documentation       │
├──────────────────────────┼──────────────────────────────────────────┤
│        PAIN POINTS 😣    │              GAINS 🌟                    │
│                          │                                           │
│ No centralized config UI │ u_library_configuration table with UI    │
│ No audit visibility      │ Immutable audit log (3-year retention)   │
│ Manual role assignment   │ Auto-profile creation on role assignment  │
│ No scheduled job monitor │ Admin dashboard with job health widget   │
└──────────────────────────┴──────────────────────────────────────────┘
```

---

## Key Insights Summary

| Persona | Top Pain Point | Solution Design Influence |
|---------|--------------|--------------------------|
| Student | No real-time visibility into availability or request status | Service Portal + My Requests page with live status |
| Librarian | Manual, repetitive processing with no automation | Flow Designer lifecycle + automated notifications |
| Library Manager | No operational dashboard; manual reporting | PA dashboards + 8 scheduled reports |
| Administrator | No centralized config; no audit confidence | Configuration table + immutable audit log |

---

*Next: [Define Problem Statements](Define%20Problem%20Statements.md)*  
*Phase 2: [Requirement Analysis](../2.%20Requirement%20Analysis/)*
