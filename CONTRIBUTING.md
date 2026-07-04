# Contributing to Smart Library Request Workflow

Thank you for your interest in contributing to this project. This document provides guidelines for contributing effectively.

---

## 📋 Table of Contents

- [Code of Conduct](#code-of-conduct)
- [How to Contribute](#how-to-contribute)
- [Development Workflow](#development-workflow)
- [Documentation Standards](#documentation-standards)
- [ServiceNow Contribution Guidelines](#servicenow-contribution-guidelines)
- [Pull Request Process](#pull-request-process)
- [Commit Message Standards](#commit-message-standards)

---

## Code of Conduct

This project adheres to the [Code of Conduct](CODE_OF_CONDUCT.md). By participating, you agree to uphold these standards.

---

## How to Contribute

### Types of Contributions

| Type | Description |
| ------ | ------------- |
| 🐛 Bug Reports | Found an issue? Open a bug report |
| ✨ Feature Requests | Have an idea? Submit a feature request |
| 📖 Documentation | Improve or expand existing docs |
| 🔧 ServiceNow Scripts | Contribute Business Rules, Client Scripts, etc. |
| 🧪 Test Cases | Add test scenarios |
| 🎨 UI/UX | Portal widget improvements |

### Before You Start

1. **Check existing issues** — your idea may already be tracked
2. **Review the architecture** — understand how components fit together ([docs/architecture/SystemArchitecture.md](docs/architecture/SystemArchitecture.md))
3. **Read the technical blueprint** — understand implementation standards ([docs/TechnicalBlueprint.md](docs/TechnicalBlueprint.md))

---

## Development Workflow

### Setting Up

```bash
# Clone the repository
git clone https://github.com/smartbridge-technologies/smart-library-request-workflow.git
cd smart-library-request-workflow

# Create a feature branch
git checkout -b feature/your-feature-name

# For documentation changes
git checkout -b docs/your-doc-improvement
```

### ServiceNow Development

1. Import the current Update Set into your ServiceNow developer instance
2. Make your changes within the `x_univ_library` application scope
3. Export changes as an Update Set
4. Document the Update Set in your PR

---

## Documentation Standards

### Markdown Conventions

- Use ATX-style headers (`#`, `##`, `###`)
- All tables must have header rows
- Code blocks must specify the language (`\`\`\`javascript`,`\`\`\`sql`, etc.)
- Links must use descriptive anchor text (not "click here")
- Images must have alt text

### Documentation Requirements

Every new feature or change must include:

- [ ] Updated requirements (if functional change)
- [ ] Updated technical documentation
- [ ] Updated data dictionary (if schema change)
- [ ] Updated test cases
- [ ] Changelog entry

---

## ServiceNow Contribution Guidelines

### Coding Standards

```javascript
// ✅ Good — descriptive variable names, error handling
function getBorrowCountForStudent(studentSysId) {
    var gr = new GlideRecord('x_smartbridge_library_borrow_request');
    gr.addQuery('student', studentSysId);
    gr.addQuery('status', 'IN', 'approved,issued');
    gr.query();
    return gr.getRowCount();
}

// ❌ Bad — vague names, no error handling
function getCnt(id) {
    var gr = new GlideRecord('x_sl_br');
    gr.addQuery('std', id);
    gr.query();
    return gr.getRowCount();
}
```

### Business Rules

- Always specify the `When` condition (before/after/async)
- Include a condition to avoid unnecessary execution
- Use `current.isValidField()` before accessing fields
- Log errors with `gs.error()`, not `gs.print()`

### Client Scripts

- Never use `g_form.setValue()` inside `onChange` for the field being changed (infinite loop)
- Always check `g_form.isNewRecord()` where appropriate
- Use `g_form.showFieldMsg()` for user feedback, not `alert()`

### Security Rules

- All new tables require ACLs for `create`, `read`, `write`, `delete`
- Script Includes containing sensitive logic must set `callerAccess` appropriately
- Never use `gs.getSession().isInteractive()` as the sole security check

---

## Pull Request Process

1. **Branch naming**: `feature/`, `bugfix/`, `docs/`, `hotfix/`
2. **PR title**: Follow the format: `[Type] Brief description`
   - Example: `[Feature] Add damage report generation for returned books`
3. **PR description**: Fill out the [PR template](.github/PULL_REQUEST_TEMPLATE.md) completely
4. **Reviews required**: Minimum 1 approval from a project maintainer
5. **CI checks**: All status checks must pass before merge
6. **Documentation**: All relevant docs must be updated in the same PR

---

## Commit Message Standards

This project follows [Conventional Commits](https://www.conventionalcommits.org/):

```text
<type>(<scope>): <subject>

<body>

<footer>
```

### Types

| Type | Usage |
| ------ | ------- |
| `feat` | New feature |
| `fix` | Bug fix |
| `docs` | Documentation only |
| `style` | Formatting, no logic change |
| `refactor` | Code refactoring |
| `test` | Adding or updating tests |
| `chore` | Build process, dependency updates |

### Examples

```text
feat(approvals): add 48-hour escalation to Library Manager

Implemented Business Rule and Flow Designer step to detect approval
tasks older than 48 hours and reassign to Library Manager group.

Closes #42
```

```text
docs(database): update Data Dictionary with new damage_report fields

Added title, type, and description columns for the three new fields
introduced in the damage report table extension.
```

---

## Questions?

Open a [Question issue](.github/ISSUE_TEMPLATE/question.md) or reach out to the project maintainers.

---

*SmartBridge Technologies — ServiceNow Practice*
