# Service Portal Design

# Smart Library Request Workflow — ServiceNow Enterprise Solution

> **Document Type:** Service Portal Design  
> **Version:** 2.0.0  
> **Application Scope:** `x_univ_library`  
> **Status:** Final — Complete

---

## 1. Portal Configuration

| Attribute | Value |
| ----------- | ------- |
| **Portal ID** | `x_univ_library_portal` |
| **Portal Title** | University Library |
| **Theme** | Custom library theme (based on SNow Seismic) |
| **Logo** | University library logo |
| **CSS** | Custom SCSS for library branding |
| **Authentication** | Inherited from ServiceNow platform |

---

## 2. Portal Page Inventory

| Page ID | URL | Name | Audience |
| --------- | ----- | ------ | ---------- |
| `PG-001` | `/library` | Library Home | All authenticated users |
| `PG-002` | `/library/catalog` | Book Catalog | Students |
| `PG-003` | `/library/book/{sys_id}` | Book Detail | Students |
| `PG-004` | `/library/request` | Borrow Request Form | Students |
| `PG-005` | `/library/my-requests` | My Requests | Students |
| `PG-006` | `/library/history` | My Borrowing History | Students |
| `PG-007` | `/library/operations` | Library Operations | Librarians, Managers |
| `PG-008` | `/library/profile` | My Profile | Students |

---

## 3. Page Designs

### PG-001: Library Home

```
┌─────────────────────────────────────────────────────────────────┐
│  🏛️ University Library                    [Student Name] [Logout] │
├─────────────────────────────────────────────────────────────────┤
│         Welcome to the University Library, [First Name]!         │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │ 🔍 Search for books by title, author, ISBN, or category  │   │
│  │ [___________________________________] [Search]           │   │
│  └──────────────────────────────────────────────────────────┘   │
├───────────────────┬──────────────────────┬──────────────────────┤
│   📋 My Requests  │  📚 Browse Catalog   │  🔔 Notifications    │
│   2 Active        │  500 Books Available │  1 Unread            │
│   [View →]        │  [Browse →]          │  [View →]            │
├───────────────────┴──────────────────────┴──────────────────────┤
│  📚 Featured Books — New Arrivals                                │
│  ┌──────┐  ┌──────┐  ┌──────┐  ┌──────┐                        │
│  │ Book │  │ Book │  │ Book │  │ Book │                        │
│  │ Cover│  │ Cover│  │ Cover│  │ Cover│                        │
│  │ Art  │  │ Art  │  │ Art  │  │ Art  │                        │
│  │Title │  │Title │  │Title │  │Title │                        │
│  │Author│  │Author│  │Author│  │Author│                        │
│  │[Req] │  │[Req] │  │[N/A] │  │[Req] │                        │
│  └──────┘  └──────┘  └──────┘  └──────┘                        │
├─────────────────────────────────────────────────────────────────┤
│  🏷️ Browse by Category                                           │
│  [💻 CS] [📖 Lit] [🔬 Sci] [📐 Math] [📜 Hist] [💼 Biz]       │
└─────────────────────────────────────────────────────────────────┘
```

### PG-002: Book Catalog

```
┌─────────────────────────────────────────────────────────────────┐
│  📚 Book Catalog                                                  │
├────────────────────┬────────────────────────────────────────────┤
│  FILTERS           │  Results: 47 books matching "python"        │
│  ─────────         │  Sort: [Relevance ▼]    [📄 50 per page]   │
│  Category          │  ──────────────────────────────────────    │
│  □ Computer Sci    │  ┌─────────────────────────────────────┐   │
│  □ Literature      │  │ [Cover] Python Crash Course         │   │
│  □ Science         │  │         Eric Matthes                │   │
│  □ Mathematics     │  │         ⭐ Computer Science          │   │
│                    │  │         📦 4 copies available        │   │
│  Availability      │  │                       [Request Book] │   │
│  ○ All             │  └─────────────────────────────────────┘   │
│  ○ Available only  │  ┌─────────────────────────────────────┐   │
│                    │  │ [Cover] Python for Data Analysis    │   │
│  [Apply Filters]   │  │         Wes McKinney                │   │
│                    │  │         ⭐ Computer Science          │   │
│                    │  │         📦 Currently Unavailable    │   │
│                    │  │                    [Unavailable]    │   │
│                    │  └─────────────────────────────────────┘   │
│                    │  [← 1 2 3 ... 10 →]                       │
└────────────────────┴────────────────────────────────────────────┘
```

### PG-004: Borrow Request Form

```
┌─────────────────────────────────────────────────────────────────┐
│  📋 Request Book — Clean Code                                     │
├─────────────────────────────────────────────────────────────────┤
│  Book Details                                                     │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │ [Cover] Clean Code: A Handbook of Agile Software...       │  │
│  │         Author: Robert C. Martin                          │  │
│  │         ISBN: 978-0-13-468599-1                           │  │
│  │         Category: Computer Science                        │  │
│  │         📦 3 copies available                             │  │
│  └───────────────────────────────────────────────────────────┘  │
│                                                                   │
│  Request Details                                                  │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │ Preferred Pickup Date *                                    │  │
│  │ [Jan 20, 2024          📅]  (1–30 days from today)        │  │
│  │                                                            │  │
│  │ Notes (optional)                                           │  │
│  │ [                                          ]               │  │
│  │ [                                          ]  450/500      │  │
│  └───────────────────────────────────────────────────────────┘  │
│                                                                   │
│  ⚠️ Your active borrows: 2 of 5 maximum                          │
│                                                                   │
│  [Cancel]                              [Submit Request]           │
└─────────────────────────────────────────────────────────────────┘
```

### PG-005: My Requests

```
┌─────────────────────────────────────────────────────────────────┐
│  📋 My Library Requests                                           │
├─────────────────────────────────────────────────────────────────┤
│  Filter: [All ▼]  [Last 30 days ▼]                    [+ New]   │
├──────────┬───────────────────────┬──────────────┬───────────────┤
│ Request# │ Book                  │ Status       │ Actions        │
├──────────┼───────────────────────┼──────────────┼───────────────┤
│LIB-00847 │ Clean Code            │ 🟦 Issued    │ [Details]      │
│          │ Due: Jan 28, 2024     │              │               │
├──────────┼───────────────────────┼──────────────┼───────────────┤
│LIB-00846 │ Pragmatic Programmer  │ 🟠 Pending   │ [Cancel]       │
│          │ Submitted: Jan 15     │ Approval     │ [Details]      │
├──────────┼───────────────────────┼──────────────┼───────────────┤
│LIB-00812 │ Python Crash Course   │ ✅ Returned  │ [Details]      │
│          │ Returned: Dec 28      │              │               │
├──────────┼───────────────────────┼──────────────┼───────────────┤
│LIB-00798 │ Data Structures       │ 🔴 Rejected  │ [Details]      │
│          │ Dec 15                │              │               │
└──────────┴───────────────────────┴──────────────┴───────────────┘
```

---

## 4. Accessibility Requirements (ref. NFR-06)

All Portal pages must meet **WCAG 2.1 Level AA**:

| Requirement | Implementation |
| ------------- | --------------- |
| Color contrast ratio ≥ 4.5:1 | Custom CSS color palette verified |
| Keyboard navigation | All interactive elements tab-accessible |
| Screen reader compatibility | ARIA labels on all custom widgets |
| Alt text for images | Book cover images: alt = "Cover: [title] by [author]" |
| Focus indicators | Visible focus rings on all interactive elements |
| Error messages | Inline, adjacent to the invalid field |
| Mobile responsive | 320px to 2560px screen width support |

> Full WCAG validation requires manual testing with assistive technologies and expert accessibility review beyond automated tooling.

---

## 5. Mobile Responsiveness

| Breakpoint | Layout |
| ------------ | -------- |
| < 768px (Mobile) | Single column, stacked cards |
| 768px – 1024px (Tablet) | 2-column grid |
| > 1024px (Desktop) | 3-column grid + sidebar filters |

---

*References: [requirements.md](../../.kiro/specs/smart-library-request-workflow/requirements.md) — FR-15, NFR-06*  
*See also: [ServiceCatalog.md](../ServiceCatalog.md)*
