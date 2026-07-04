# Dashboards Design

# Smart Library Request Workflow — ServiceNow Enterprise Solution

> **Document Type:** Dashboards Design  
> **Version:** 2.0.0  
> **Application Scope:** `x_univ_library`  
> **Status:** Final — Complete

---

## 1. Dashboard Overview

Two dashboards serve different audiences:

| Dashboard | Audience | Description |
|-----------|----------|-------------|
| **Library Operations Dashboard** | Librarians, Library Managers | Real-time operational KPIs and trends |
| **Student Self-Service Dashboard** | Students | Personal borrow status and history |

---

## 2. Library Operations Dashboard

### Dashboard Layout

```text
┌─────────────────────────────────────────────────────────────────────┐
│  📚 Library Operations Dashboard                    [🔄 Auto-refresh] │
├─────────────┬─────────────┬─────────────┬─────────────────────────── │
│ WGT-001     │ WGT-002     │ WGT-003     │ WGT-004                    │
│ Active      │ Overdue     │ Available   │ Pending                    │
│ Requests    │ Books       │ Books       │ Approvals                  │
│   [ 47 ]    │   [ 8 ]     │  [ 342 ]   │   [ 12 ]                   │
│  ↑ +3 today │  ↑ +1 today │  📦 Stock  │  ⏰ SLA: 12 within 48h    │
├─────────────┴─────────────┴─────────────┴─────────────────────────── │
│                                                                       │
│  WGT-005: Books Borrowed by Category (Current Month)                 │
│  ┌─────────────────────────────────────────────────────────────────┐ │
│  │ Computer Science ████████████████████ 45                        │ │
│  │ Literature       ██████████████ 32                              │ │
│  │ Science          ████████████ 28                                │ │
│  │ Mathematics      ████████ 18                                    │ │
│  │ History          █████ 12                                       │ │
│  └─────────────────────────────────────────────────────────────────┘ │
│                                                                       │
│  WGT-006: Daily Borrow Volume (Last 30 Days)                         │
│  ┌─────────────────────────────────────────────────────────────────┐ │
│  │ 20 ─┐       ╭──╮        ╭──╮                                   │ │
│  │ 15 ─│  ╭────╯  │╭──╮   ╭╯  ╰──╮                               │ │
│  │ 10 ─│──╯       ╰╯  ╰───╯      ╰──────                          │ │
│  │  5 ─│                                                            │ │
│  │  0 ─┴────────────────────────────────────────── Days           │ │
│  └─────────────────────────────────────────────────────────────────┘ │
│                                                                       │
│  WGT-007: Recent Activity (Last 10 Actions)                          │
│  ┌─────────────────────────────────────────────────────────────────┐ │
│  │ 10:32 | LIB-00000847 | Clean Code | Issued to John Smith       │ │
│  │ 10:18 | LIB-00000846 | Approved | Python Basics → Jane Doe     │ │
│  │ 09:55 | LIB-00000845 | Returned | Data Structures — On Time    │ │
│  └─────────────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────────┘
```

### Widget Specifications

#### WGT-001: Active Borrow Requests

| Attribute | Value |
| ----------- | ------- |
| **Widget ID** | `WGT-001` |
| **Type** | KPI Single Count |
| **Data Source** | `u_library_borrow_requests` |
| **Filter** | `u_status IN (pending_approval, approved, issued, escalated)` |
| **Aggregate** | COUNT |
| **Refresh** | 5 minutes |
| **Click Action** | Navigate to All Requests (filtered) |
| **Threshold** | > 50: amber; > 100: red |

#### WGT-002: Overdue Books

| Attribute | Value |
| ----------- | ------- |
| **Widget ID** | `WGT-002` |
| **Type** | KPI Single Count |
| **Data Source** | `u_library_borrow_requests` |
| **Filter** | `u_overdue_flag = true AND u_status != returned` |
| **Aggregate** | COUNT |
| **Refresh** | 5 minutes |
| **Click Action** | Navigate to Overdue Books Report (RPT-003) |
| **Threshold** | > 0: amber; > 10: red |

#### WGT-003: Available Books

| Attribute | Value |
| ----------- | ------- |
| **Widget ID** | `WGT-003` |
| **Type** | KPI Single Count |
| **Data Source** | `u_library_books` |
| **Filter** | `u_availability_status = available AND u_active = true` |
| **Aggregate** | COUNT |
| **Refresh** | 5 minutes |
| **Click Action** | Navigate to Inventory Status Report (RPT-005) |

#### WGT-004: Pending Approvals

| Attribute | Value |
| ----------- | ------- |
| **Widget ID** | `WGT-004` |
| **Type** | KPI Single Count |
| **Data Source** | `u_library_borrow_requests` |
| **Filter** | `u_status = pending_approval` |
| **Aggregate** | COUNT |
| **Refresh** | 5 minutes |
| **Click Action** | Navigate to Pending Approvals queue |
| **Threshold** | > 5: amber; > 20: red |

#### WGT-005: Books Borrowed by Category

| Attribute | Value |
| ----------- | ------- |
| **Widget ID** | `WGT-005` |
| **Type** | Horizontal Bar Chart |
| **Data Source** | `u_library_borrow_requests` |
| **Filter** | `u_status IN (issued, returned)` AND `submitted_at` in current month |
| **Group By** | `u_book.u_category.u_name` |
| **Sort** | COUNT descending |
| **Limit** | Top 10 categories |

#### WGT-006: Daily Borrow Volume

| Attribute | Value |
| ----------- | ------- |
| **Widget ID** | `WGT-006` |
| **Type** | Trend Line |
| **Data Source** | `u_library_borrow_requests` |
| **Filter** | `u_submitted_at` in last 30 days |
| **Group By** | `u_submitted_at` (daily) |
| **Aggregate** | COUNT |

#### WGT-007: Recent Activity

| Attribute | Value |
| ----------- | ------- |
| **Widget ID** | `WGT-007` |
| **Type** | Activity Stream List |
| **Data Source** | `u_library_audit_log` |
| **Filter** | `u_table_name IN (u_library_borrow_requests, u_library_return_records)` |
| **Sort** | `u_timestamp` descending |
| **Limit** | 10 records |
| **Columns** | Timestamp, Request#, Book, Action, User |

---

## 3. Student Self-Service Dashboard

### Dashboard Layout

```text
┌─────────────────────────────────────────────────────────────────┐
│  🎓 My Library — Self-Service Dashboard             [Student Name] │
├─────────────┬─────────────┬───────────────────────────────────── │
│ WGT-008     │ WGT-009     │                                       │
│ My Active   │ Books Due   │  Quick Actions                        │
│ Borrows     │ This Week   │  [📚 Browse Catalog]                  │
│   [ 2 ]     │   [ 1 ]     │  [📋 My Requests]                     │
├─────────────┴─────────────┴───────────────────────────────────── │
│  WGT-010: My Due Dates                                            │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │ ⚠️ Clean Code           — Due Jan 28, 2024 (3 days)        │ │
│  │ ✅ The Pragmatic Programmer — Due Feb 10, 2024 (16 days)   │ │
│  └─────────────────────────────────────────────────────────────┘ │
│  WGT-011: My Recent Requests                                      │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │ LIB-00000847 | Clean Code           | Issued   | Jan 14     │ │
│  │ LIB-00000812 | Python Crash Course  | Returned | Dec 28     │ │
│  │ LIB-00000798 | Data Structures      | Rejected | Dec 15     │ │
│  └─────────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────┘
```

### Widget Specifications

#### WGT-008: My Active Borrows

| Attribute | Value |
| ----------- | ------- |
| **Filter** | `u_student.u_user = current_user AND u_status = issued` |
| **Aggregate** | COUNT |
| **Threshold** | At max_borrow_limit: amber |

#### WGT-009: Books Due This Week

| Attribute | Value |
| ----------- | ------- |
| **Data Source** | `u_library_issuance_records` |
| **Filter** | `u_student.u_user = current_user AND u_expected_return_date BETWEEN today AND today+7` |
| **Aggregate** | COUNT |
| **Threshold** | > 0: amber |

#### WGT-010: My Due Dates

| Attribute | Value |
| ----------- | ------- |
| **Type** | Ordered List |
| **Data Source** | `u_library_issuance_records` |
| **Filter** | `u_student.u_user = current_user AND u_borrow_request.u_status = issued` |
| **Sort** | `u_expected_return_date` ascending |
| **Columns** | Book Title, Expected Return Date, Days Remaining |

#### WGT-011: My Recent Requests

| Attribute | Value |
| ----------- | ------- |
| **Type** | List |
| **Data Source** | `u_library_borrow_requests` |
| **Filter** | `u_opened_by = current_user` |
| **Sort** | `u_submitted_at` descending |
| **Limit** | 10 records |

---

## 4. Performance Analytics Configuration

Performance Analytics indicators were configured for each KPI widget to power the dashboard metrics:

| KPI Widget | Indicator | Collection Schedule |
| ----------- | ----------- | ------------------- |
| Active Requests | `library_active_requests` | Every 5 minutes |
| Overdue Books | `library_overdue_books` | Every 5 minutes |
| Available Books | `library_available_books` | Every 30 minutes |
| Pending Approvals | `library_pending_approvals` | Every 5 minutes |
| Books Borrowed by Category | `library_borrows_by_category` | Daily |

The indicators were configured in **Performance Analytics > Indicators** within the `x_univ_library` scope. Collection runs every 5 minutes during operational hours (08:00–20:00) and every 30 minutes off-peak. Each indicator sources data from the corresponding application table with the widget's defined filter criteria. Data is retained for 12 months for trend analysis.

---

*References: [requirements.md](../../.kiro/specs/smart-library-request-workflow/requirements.md) — FR-12*  
*See also: [Reports.md](Reports.md) | [PortalDesign.md](PortalDesign.md)*
