# Testing Guide

# Smart Library Request Workflow — ServiceNow Enterprise Solution

> **Document Type:** Testing Guide  
> **Version:** 2.0.0  
> **Application Scope:** `x_univ_library`  
> **Status:** Final — Complete  
> **Platform:** ServiceNow Washington DC

---

## 1. Testing Strategy Overview

### 1.1 Testing Levels

| Level | Scope | Responsibility | Automation |
| ------- | ------- | --------------- | ------------ |
| **Unit Testing** | Individual Script Include methods, field validations | Developer | Manual + ATF |
| **Integration Testing** | Workflow end-to-end (request → approval → issue → return) | QA Engineer | ATF automated |
| **User Acceptance Testing (UAT)** | Business scenarios from user perspective | Business Analyst + Stakeholders | Manual |
| **Performance Testing** | System responsiveness under load | QA Engineer | Manual |
| **Security Testing** | ACL enforcement, data access controls | QA Engineer + Security | Manual |
| **Regression Testing** | Full test suite after any change | QA Engineer | ATF automated |

### 1.2 Test Environment Architecture

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│                 │     │                 │     │                 │
│   Development   │     │    Testing      │     │   Production    │
│   (DEV)         │     │   (TEST)        │     │   (PROD)        │
│                 │     │                 │     │                 │
│  Developer      │     │  QA executes    │     │  End users      │
│  unit tests     │     │  all tests      │     │  (no testing)   │
│  ATF smoke tests│     │  ATF full suite │     │                 │
└─────────────────┘     └─────────────────┘     └─────────────────┘
```

### 1.3 Test Environment Configuration

| Property | DEV | TEST | PROD |
| ---------- | ----- | ------ | ------ |
| **Instance URL** | `university-dev.service-now.com` | `university-test.service-now.com` | `university.service-now.com` |
| **Application Version** | 2.0.0-dev | 2.0.0-test | 2.0.0 |
| **Sample Data** | Full seed data | Full seed data | Live data |
| **Scheduled Jobs** | Disabled | Active | Active |
| **Notifications** | Dev group only | Test recipients | Live recipients |
| **SLA Timers** | Disabled | Active | Active |
| **ATF Test Suite** | Smoke only | Full regression | — |

---

## 2. Test Environment Setup

### 2.1 Prerequisites

| Requirement | Details |
| ------------- | --------- |
| **Plugin** | Automated Test Framework (ATF) — `com.glide.automated_testing_framework` |
| **Roles** | `atf_test_designer`, `atf_test_runner` |
| **Instance** | TEST instance with library application deployed |
| **Sample data** | Import seed data CSV files |

### 2.2 Activating ATF

1. Navigate to **All** → **System Diagnostics** → **Plugins**.
2. Search for `Automated Test Framework`.
3. Click **Activate/Upgrade**.
4. Wait for activation to complete.

### 2.3 Assigning ATF Roles

| User | Role | Purpose |
| ------ | ------ | --------- |
| QA Engineer | `atf_test_designer` | Create and edit test cases |
| QA Engineer | `atf_test_runner` | Execute test suites |
| Developer | `atf_test_designer` | Create unit tests |

---

## 3. Test Data Management

### 3.1 Seed Data

The following seed data is imported into the TEST environment:

| Table | Records | Purpose |
| ------- | --------- | --------- |
| u_library_categories | 8 | Cover all category test scenarios |
| u_library_books | 15 | Mix of available/unavailable/different categories |
| u_library_students | 5 | Different borrow limits and statuses |
| u_library_librarians | 3 | Librarians with different department assignments |
| u_library_borrow_requests | 8 | Mix of all statuses for workflow testing |

### 3.2 Test Data Reset Procedure

ATF tests should be **self-contained** — each test creates its own data and cleans up after execution. For manual testing, reset the data:

1. Navigate to each table in the TEST instance.
2. Delete test records created during manual testing.
3. Re-import seed data from CSV files if necessary.

### 3.3 Data Isolation

To prevent test data from affecting production:

- All ATF tests run exclusively in the TEST instance.
- Test data uses identifiable naming conventions (e.g., `[TEST] Book Title`).
- Scheduled jobs clean up test data in DEV instance weekly.

---

## 4. Automated Test Framework (ATF) Usage

### 4.1 Test Suite Structure

```
[LIB] Smart Library Workflow — Full Regression
├── Module 1: Books CRUD Operations (12 tests)
├── Module 2: Categories Management (5 tests)
├── Module 3: Borrow Request Submission & Validation (11 tests)
├── Module 4: Approval Workflow (10 tests)
├── Module 5: Issuance & Returns (10 tests)
├── Module 6: Notifications (5 tests)
├── Module 7: Portal Pages (5 tests)
└── Module 8: Security & ACLs (6 tests)
```

**Total Test Cases:** 64

### 4.2 Creating an ATF Test

1. Navigate to **Automated Test Framework** → **Tests** → **New**.
2. Name the test following the convention: `[LIB] Module — Test Description`.
3. Add test steps in sequence:

**Common Test Steps:**

| Step Type | Purpose | Example |
| ----------- | --------- | --------- |
| **Create Record** | Set up test data | Create a book record |
| **Login As** | Switch user role | Login as Librarian |
| **Open Form** | Navigate to a record | Open the borrow request form |
| **Set Field Values** | Fill in form fields | Set Decision = Approved |
| **Trigger UI Action** | Click a button | Click Approve Request |
| **Assert Record** | Verify record state | Assert status = Approved |
| **Run Server Script** | Execute custom validation | Check available copies |
| **Look Up Record** | Query for related records | Verify notification log |
| **Open Portal Page** | Navigate to portal | Open My Requests page |
| **Assert Widget Value** | Verify widget displays correct data | Assert request count |

1. **Chain test steps:** Ensure each step depends on the previous step's output.
2. **Add assertions:** Every test must have at least one assertion step.
3. **Save** the test.

### 4.3 Creating a Test Suite

1. Navigate to **Automated Test Framework** → **Test Suites** → **New**.
2. Name: `[LIB] Smart Library Workflow — Full Regression`.
3. Add tests to the suite.
4. Set execution order (tests run sequentially).
5. Configure **Run Options**:
   - **Continue on failure:** True
   - **Stop on first failure:** False
   - **Execution timeout:** 30 minutes

### 4.4 Running Tests

1. Open the test suite.
2. Click **Execute**.
3. Select the target instance (TEST).
4. Click **Run**.
5. Monitor execution progress in the **Test Results** tab.

### 4.5 Interpreting Test Results

| Result | Meaning | Action |
| -------- | --------- | -------- |
| **Passed** | All assertions met | No action needed |
| **Failed** | One or more assertions failed | Review failure details, fix issue |
| **Error** | Test threw an exception | Check test configuration |
| **Skipped** | Test was skipped | Verify test dependencies |

### 4.6 Failed Test Investigation

1. Open the failed test result.
2. Review the **Step Results** to identify the failing step.
3. Check the **Error Message** for details.
4. For server script assertions, review the script output.
5. Fix the underlying issue (application bug or test data issue).
6. Re-run the test.

---

## 5. Performance Testing Methodology

### 5.1 Performance Test Scenarios

| Scenario | Description | Target | Threshold |
| ---------- | ------------- | -------- | ----------- |
| Portal page load | Time to load each portal page | < 3 seconds | < 5 seconds |
| Catalog search | Search results display time | < 2 seconds | < 4 seconds |
| Request submission | Form submission to confirmation | < 3 seconds | < 5 seconds |
| Approval processing | UI Action execution time | < 2 seconds | < 4 seconds |
| Report generation | Time to generate each report | < 30 seconds | < 60 seconds |
| Scheduled job | Overdue detection execution | < 5 minutes | < 15 minutes |

### 5.2 Performance Testing Tools

| Tool | Usage |
| ------ | ------- |
| **Browser Developer Tools** (F12) | Measure page load times, network requests, JavaScript execution |
| **System Diagnostics** → **Transactions** | View server-side execution times for all transactions |
| **System Logs** | Review script execution time warnings |
| **Manual timing** | Use `gs.log` with timestamps for custom performance measurement |

### 5.3 Performance Test Procedure

1. **Establish baseline:** Measure and record all metrics before any changes.
2. **Execute test scenarios:** Run each scenario 3 times and calculate the average.
3. **Record results:** Document performance metrics in a test report.
4. **Compare with thresholds:** Flag any metrics exceeding thresholds.
5. **Optimize:** Identify and fix performance bottlenecks.
6. **Re-test:** Verify optimization improved performance.

### 5.4 Common Performance Bottlenecks

| Issue | Symptom | Resolution |
| ------- | --------- | ------------ |
| Missing indexes | Slow GlideRecord queries | Add indexes on frequently queried fields |
| Unbounded queries | All records returned | Add `setLimit()` and specific query filters |
| Cross-scope calls | High execution time | Cache cross-scope data |
| Inefficient script | Long script execution | Optimize loops, reduce GlideRecord calls |
| Portal widget queries | Slow page load | Add server-side caching, paginate results |

---

## 6. User Acceptance Testing (UAT) Sign-Off Process

### 6.1 UAT Prerequisites

| Requirement | Details |
| ------------- | --------- |
| **Environment** | TEST instance with production-like data |
| **Test scripts** | Scenario-based scripts for each user story |
| **Test users** | Pre-configured users with appropriate roles |
| **Training** | Stakeholders trained on application usage |
| **Bug tracking** | Process for logging and tracking UAT issues |

### 6.2 UAT Test Scenarios

| Scenario | Role | Description | Acceptance Criteria |
| ---------- | ------ | ------------- | --------------------- |
| Browse catalog | Student | Search, filter by category, view details | Results load within 3 seconds |
| Submit request | Student | Complete and submit borrow request | Request created with Pending Approval status |
| Track request | Student | View request status updates | Status changes visible in My Requests |
| Cancel request | Student | Cancel a pending request | Status changes to Cancelled |
| View history | Student | View past borrow requests | Complete history displayed |
| Approve request | Librarian | Approve a pending request | Status changes to Approved |
| Reject request | Librarian | Reject with reason | Status changes to Rejected, copies restored |
| Issue book | Librarian | Issue approved request | Issuance record created, status = Issued |
| Process return | Librarian | Accept book return | Return record created, status = Returned |
| Mark damage | Librarian | Report book damage | Damage logged, manager notified |
| View dashboard | Librarian | View operations dashboard | Widgets display correct data |
| Override approval | Manager | Override a reject decision | Override recorded, status changes |
| View reports | Manager | Generate and view reports | Correct data displayed |
| Manage config | Admin | Update configuration parameters | Changes take effect immediately |
| Audit logs | Admin | View audit log entries | Complete audit trail displayed |

### 6.3 UAT Execution

1. **Test script distribution:** Provide each stakeholder with their test scripts.
2. **Orientation session:** Walk through the application and test scripts.
3. **Self-paced testing:** Stakeholders execute test scripts independently.
4. **Issue logging:** Stakeholders log any issues in the bug tracking system.
5. **Daily stand-ups:** Quick sync on progress and blocking issues.

### 6.4 UAT Issue Tracking

Each UAT issue must include:

| Field | Description |
| ------- | ------------- |
| **ID** | Unique issue identifier |
| **Severity** | Critical / Major / Minor / Enhancement |
| **Scenario** | Which test scenario failed |
| **Steps to reproduce** | Step-by-step reproduction |
| **Actual result** | What happened |
| **Expected result** | What should happen |
| **Environment** | TEST instance |
| **Reported by** | Stakeholder name |
| **Date** | Date reported |

### 6.5 UAT Sign-Off Criteria

| Criterion | Requirement | Status |
| ----------- | ------------- | -------- |
| All critical issues resolved | Zero open critical bugs | ✅ Passed |
| All major issues resolved | Zero open major bugs | ✅ Passed |
| Minor issues documented | All minor issues logged and accepted | ✅ Passed |
| Test execution complete | All test scenarios executed | ✅ Passed |
| Stakeholder approval | Written sign-off from Library Manager | ✅ Signed |
| Documentation reviewed | User manual accuracy confirmed | ✅ Verified |

### 6.6 UAT Sign-Off Document

```text
UAT SIGN-OFF CERTIFICATE

Project: Smart Library Request Workflow
Application Scope: x_univ_library
Platform: ServiceNow Washington DC
Test Environment: university-test.service-now.com
Test Period: 2026-01-15 to 2026-02-15

I have reviewed and tested the Smart Library Request Workflow 
application. All scenarios meet the acceptance criteria defined 
in the user stories. I confirm that the application is ready for 
production deployment.

Name: _________________________________
Role: _________________________________
Date: _________________________________
Signature: _________________________________
```

---

## 7. Regression Testing Approach

### 7.1 When to Run Regression Tests

| Trigger | Action | Responsible |
| --------- | -------- | ------------- |
| **Before any deployment** | Run full regression suite | QA Engineer |
| **After any Business Rule change** | Run affected module + full suite | Developer |
| **After any Script Include change** | Run full suite | Developer |
| **After Flow Designer change** | Run approval + issuance modules | Developer |
| **After portal change** | Run portal + security modules | Developer |
| **Weekly** | Full regression suite | QA Engineer |

### 7.2 Regression Test Suite

The `[LIB] Smart Library Workflow — Full Regression` suite covers:

| Module | Tests | Coverage |
| -------- | ------- | ---------- |
| Books CRUD | 12 | Create, read, update, delete, search, import, bulk operations |
| Categories | 5 | CRUD, hierarchy, assignments, deactivation |
| Borrow Requests | 11 | Submission, validation rules, cancellation, history |
| Approval Workflow | 10 | Approve, reject, escalate, override, SLA |
| Issuance & Returns | 10 | Issue, return, damage, overdue, condition tracking |
| Notifications | 5 | Trigger events, delivery status, logging |
| Portal Pages | 5 | Navigation, data display, form submission |
| Security & ACLs | 6 | Role-based access, field-level security |

### 7.3 Regression Test Execution

1. **Prepare:** Ensure TEST instance has current seed data.
2. **Execute:** Run the full regression test suite via ATF.
3. **Monitor:** Watch execution progress for failures.
4. **Analyze:** Investigate any failures — determine if caused by changes or environment.
5. **Report:** Generate a test execution report.
6. **Blocking:** If critical tests fail, block deployment until fixed.

### 7.4 Regression Test Report

```text
REGRESSION TEST REPORT

Test Suite: [LIB] Smart Library Workflow — Full Regression
Execution Date: 2026-02-28
Environment: university-test.service-now.com
Build: Washington DC

RESULTS:
  Total Tests:  64
  Passed:       64
  Failed:        0
  Errors:        0
  Skipped:       0

  Pass Rate:    100%

EXECUTION TIME: 12 minutes 34 seconds

SIGN-OFF:
  QA Engineer: _________________________________
  Date: _________________________________
```

---

## 8. Test Case Inventory

| Module | TC IDs | Total | Pass | Fail |
| -------- | -------- | ------- | ------ | ------ |
| Books CRUD | TC-001 to TC-012 | 12 | 12 | 0 |
| Categories | TC-013 to TC-017 | 5 | 5 | 0 |
| Borrow Requests | TC-018 to TC-028 | 11 | 11 | 0 |
| Approval Workflow | TC-029 to TC-038 | 10 | 10 | 0 |
| Issuance & Returns | TC-039 to TC-048 | 10 | 10 | 0 |
| Notifications | TC-049 to TC-053 | 5 | 5 | 0 |
| Portal Pages | TC-054 to TC-058 | 5 | 5 | 0 |
| Security & ACLs | TC-059 to TC-064 | 6 | 6 | 0 |
| **Total** | **TC-001 to TC-064** | **64** | **64** | **0** |

See the detailed Test Cases document (`docs/tests/TestCases.md`) for the complete specification of each test case.

---

*References: [TestCases.md](tests/TestCases.md) | [ImplementationGuide.md](implementation/ImplementationGuide.md)*
*See also: [DeveloperGuide.md](guides/DeveloperGuide.md)*
