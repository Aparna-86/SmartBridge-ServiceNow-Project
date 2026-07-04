# Security Policy

## 🔒 Supported Versions

| Version | Supported |
|---------|-----------|
| 2.0.x (Implementation Complete) | ✅ Active |
| < 2.0 | ❌ Not Supported |

---

## 🚨 Reporting a Vulnerability

The SmartBridge Technologies security team takes all security vulnerabilities seriously.

### How to Report

**Please do NOT report security vulnerabilities through public GitHub issues.**

Instead, please report them by:

1. **Email**: `security@smartbridge-technologies.example.com`
2. **Subject Line**: `[SECURITY] Smart Library Request Workflow — <brief description>`
3. **Response Time**: You will receive an acknowledgment within 48 hours and a detailed response within 7 business days.

### What to Include

Please include as much of the following information as possible:

- Type of issue (SQL injection, XSS, CSRF, privilege escalation, etc.)
- Full path of the affected source file(s)
- Location of the affected source code (tag/branch/commit/URL)
- Step-by-step instructions to reproduce the issue
- Proof-of-concept or exploit code (if possible)
- Impact assessment of the issue

---

## 🛡️ Security Design Principles

This application is built on the following security principles:

### ServiceNow Platform Security

- **Scoped Application**: All components run within an isolated application scope (`x_univ_library`)
- **ACL Enforcement**: Every table and field protected by role-based Access Control Lists
- **No Direct SQL**: All data access via ServiceNow GlideRecord API (prevents SQL injection)
- **Input Validation**: Server-side validation on all Business Rules
- **Script Isolation**: Client scripts cannot access server-side data directly

### Role-Based Access Control

| Role | Principle |
| ------ | ----------- |
| `student_library` | Least privilege — read books, manage own requests only |
| `librarian_library` | Operational access — manage inventory and approvals |
| `library_manager` | Elevated access — full operational + reporting |
| `library_admin` | Administrative access — configuration and user management |

### Data Protection

- No Personally Identifiable Information (PII) stored outside ServiceNow's encrypted platform
- All user data subject to university data retention policies
- Audit logs retained for minimum 3 years and are immutable
- No external API calls that transmit user data

### Network Security

- All ServiceNow communication over HTTPS/TLS 1.2+
- Service Portal authentication via ServiceNow session management
- No direct database connections from client-side code

---

## 📋 Security Compliance

This solution is designed to comply with:

- FERPA (Family Educational Rights and Privacy Act) for student data
- University IT Security Policy
- ServiceNow Security Best Practices Guide
- OWASP Top 10 mitigation guidelines

---

## 🔍 Security Audit

Security review checklist:

- [x] ACL design reviewed
- [x] Script Include access controls verified
- [x] No hardcoded credentials
- [x] Sensitive fields marked appropriately
- [x] Penetration test — completed
- [x] FERPA compliance review — completed

---

*For full security design documentation, see [docs/SecurityDesign.md](docs/SecurityDesign.md)*
