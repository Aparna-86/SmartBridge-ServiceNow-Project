# System Architecture

# Smart Library Request Workflow — ServiceNow Enterprise Solution

> **Document Type:** System Architecture  
> **Version:** 2.0.0  
> **References:** FR-01 through FR-17, NFR-01 through NFR-07  
> **Status:** Final — Complete

---

## 1. Architecture Overview

The Smart Library Request Workflow is built as a **ServiceNow Scoped Application** with scope prefix `x_univ_library`. It leverages native ServiceNow platform capabilities throughout — no third-party frameworks, external databases, or custom middleware are required.

```mermaid
graph TB
    subgraph "Actors"
        ST[🎓 Students<br/>student_library role]
        LIB[📚 Librarians<br/>librarian_library role]
        MGR[🏛️ Library Manager<br/>library_manager role]
        ADM[⚙️ Administrator<br/>library_admin role]
    end

    subgraph "Presentation Tier"
        SP["🖥️ Service Portal<br/>(x_univ_library_portal)"]
        SC["📋 Service Catalog<br/>Borrow Request Item"]
        NF["📄 Native Forms<br/>(Backend Operations)"]
    end

    subgraph "Integration Tier"
        FD["⚙️ Flow Designer<br/>Borrow Request Flow"]
        NE["📧 Notification Engine<br/>10+ Templates"]
        SJ["⏰ Scheduled Jobs<br/>Overdue Detection"]
    end

    subgraph "Business Logic Tier"
        BR["📜 Business Rules<br/>15 server-side rules"]
        SI["📦 Script Includes<br/>LibraryBorrowService etc."]
        CS["💻 Client Scripts<br/>12 form scripts"]
        UI["🎨 UI Policies<br/>8 visibility rules"]
    end

    subgraph "Data Tier — x_univ_library Scope"
        BK[(u_library_books)]
        CAT[(u_library_categories)]
        STU[(u_library_students)]
        LIBT[(u_library_librarians)]
        BR2[(u_library_borrow_requests)]
        APR[(u_library_approvals)]
        ISS[(u_library_issuance_records)]
        RET[(u_library_return_records)]
        NL[(u_library_notification_log)]
        CFG[(u_library_configuration)]
        AUD[(u_library_audit_log)]
    end

    subgraph "Platform Analytics"
        PA["📊 Performance Analytics<br/>KPI Indicators"]
        RP["📈 Reports Engine<br/>8 Standard Reports"]
    end

    subgraph "External Systems"
        EMAIL["📮 University SMTP Server"]
        LDAP["🔑 LDAP / Active Directory"]
        SIS["🎓 Student Info System"]
    end

    ST --> SP
    LIB --> NF
    MGR --> PA
    ADM --> NF

    SP --> SC
    SC --> FD
    NF --> BR
    FD --> BR
    FD --> NE
    SJ --> BR
    BR --> SI
    SI --> BK & CAT & STU & LIBT & BR2 & APR & ISS & RET & NL & CFG & AUD
    NE --> EMAIL
    PA --> RP
    ADM -.->|Sync| LDAP
    ADM -.->|Import| SIS

    style EMAIL fill:#ffd700,stroke:#b8860b
    style LDAP fill:#ffd700,stroke:#b8860b
    style SIS fill:#ffd700,stroke:#b8860b
```

> **Legend:** Yellow nodes = External systems connected via SMTP, LDAP, and REST integrations

---

## 2. Architectural Layers

### 2.1 Presentation Layer

| Component | Type | Purpose | Audience |
| ----------- | ------ | --------- | ---------- |
| Service Portal | ServiceNow Portal | Student self-service interface | Students |
| Service Catalog Item | Catalog Item | Borrow request submission form | Students |
| Native Backend Forms | ServiceNow Form | Operational data management | Librarians, Managers, Admins |
| Performance Analytics Dashboard | Dashboard | Real-time KPI monitoring | Managers, Librarians |
| Reports | Report Builder | Historical analytics | Managers, Admins |

### 2.2 Business Logic Layer

| Component | Count | Technology | Purpose |
| ----------- | ------- | ----------- | --------- |
| Business Rules | 15 | Server-side GlideScript | Data validation, availability management, status sync |
| Script Includes | 5 | Server-side GlideScript class | Reusable service libraries |
| Client Scripts | 12 | Client-side JavaScript | Form behavior, validation |
| UI Policies | 8 | Declarative rules | Field visibility, mandatory, read-only |
| UI Actions | 10 | Server/client GlideScript | Form and list action buttons |
| Flow Designer Flows | 3 | Flow Designer | Automated lifecycle orchestration |
| Scheduled Jobs | 2 | Scheduled GlideScript | Overdue detection, report delivery |

### 2.3 Data Layer

All tables are created within the `x_univ_library` scope. No modifications are made to base ServiceNow tables.

| Table | Extends | Purpose |
| ------- | --------- | --------- |
| `u_library_books` | None | Book catalog |
| `u_library_categories` | None | Book classification |
| `u_library_students` | `sys_user` | Student profiles |
| `u_library_librarians` | `sys_user` | Staff profiles |
| `u_library_borrow_requests` | None | Request records |
| `u_library_approvals` | None | Approval decisions |
| `u_library_issuance_records` | None | Physical handover records |
| `u_library_return_records` | None | Return records |
| `u_library_notification_log` | None | Notification audit |
| `u_library_configuration` | None | System parameters |
| `u_library_audit_log` | None | Immutable event log |

---

## 3. Security Architecture

```mermaid
graph LR
    subgraph "Authentication"
        SAML[SAML 2.0 SSO]
        LOCAL[ServiceNow Local Auth]
    end

    subgraph "Authorization — ACL Enforcement"
        R1[student_library]
        R2[librarian_library]
        R3[library_manager]
        R4[library_admin]
    end

    subgraph "Data Access"
        ACL1[Books — Read only for students]
        ACL2[Borrow Requests — Own records for students]
        ACL3[Approvals — Librarian/Manager write]
        ACL4[Configuration — Admin only]
        ACL5[Audit Log — Admin read only]
    end

    SAML --> R1 & R2 & R3 & R4
    LOCAL --> R1 & R2 & R3 & R4
    R1 --> ACL1 & ACL2
    R2 --> ACL1 & ACL2 & ACL3
    R3 --> ACL1 & ACL2 & ACL3
    R4 --> ACL1 & ACL2 & ACL3 & ACL4 & ACL5
```

**Key Security Principles (ref. NFR-03):**

- All table access controlled by ACLs in `x_univ_library` scope
- No direct SQL — all queries via GlideRecord API
- Input validation on all user-supplied fields (XSS prevention)
- No PII in URL parameters
- All communication over HTTPS

---

## 4. Integration Architecture

| Integration | Direction | Protocol | Status |
| ------------- | ----------- | ---------- | -------- |
| University Email (SMTP) | Outbound | SMTP/TLS | ✅ Operational |
| LDAP / Active Directory | Inbound | LDAP-S | ✅ Operational |
| Student Info System (SIS) | Inbound | REST / CSV Import | ✅ Operational |
| SAML 2.0 SSO | Inbound | SAML 2.0 | ✅ Operational |

---

## 5. Deployment Architecture

```mermaid
graph LR
    subgraph "Development Instance"
        DEV[ServiceNow Developer Instance<br/>x_univ_library scope]
    end
    subgraph "Artifacts"
        US[Update Set XML<br/>x_univ_library_v1.0.0.xml]
    end
    subgraph "Production Instance"
        PROD[University ServiceNow<br/>Production Instance]
    end

    DEV -->|Export Update Set| US
    US -->|Import & Commit| PROD
```

The application was developed in a ServiceNow developer instance, packaged as a scoped application Update Set (`x_univ_library_v1.0.0.xml`), and deployed to the university's production instance. The Update Set includes all tables, business rules, flows, portal pages, ACLs, roles, notifications, reports, and dashboards.

---

## 6. Performance Architecture (ref. NFR-01, NFR-04)

| Requirement | Architecture Decision |
| ------------- | ---------------------- |
| Portal search ≤ 2s for 10k records | Server-side pagination (50 records/page), indexed `isbn`, `title`, `author` fields |
| Dashboard refresh ≤ 5 min | Performance Analytics collection interval configuration |
| 50k books supported | Index strategy on frequently queried fields |
| 5k concurrent students | ServiceNow platform handles session management natively |
| 100 concurrent Portal sessions | No application-level concurrency controls needed; platform-managed |

---

## 7. Maintainability Architecture (ref. NFR-05)

- **Scoped Application**: Isolated `x_univ_library` scope prevents naming conflicts
- **Script Includes for shared logic**: Prevents duplicate code across Business Rules and Flows
- **Named Configuration constants**: Single `u_library_configuration` table — no hardcoded thresholds
- **Update Set packaging**: All artifacts exportable as a single Update Set for migration
- **Inline code comments**: Required standard on all scripts

---

*References: [requirements.md](../../.kiro/specs/smart-library-request-workflow/requirements.md) — NFR-01 through NFR-07*  
*See also: [ApplicationArchitecture.md](ApplicationArchitecture.md) | [DeploymentGuide.md](../implementation/DeploymentGuide.md)*
