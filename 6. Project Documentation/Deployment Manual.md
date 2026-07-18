# Deployment Guide

# Smart Library Request Workflow — ServiceNow Enterprise Solution

> **Document Type:** Deployment Guide  
> **Version:** 2.0.0  
> **Application Scope:** `x_univ_library`  
> **Status:** Final — Complete  
> **Platform:** ServiceNow Washington DC

---

## 1. Pre-Deployment Checklist

### 1.1 Environment Prerequisites

| Check | Description | Verified |
| ------- | ------------- | ---------- |
| **Target instance** | Production instance URL confirmed | ✅ |
| **Platform version** | ServiceNow Washington DC or later | ✅ |
| **Admin access** | Admin credentials available for target instance | ✅ |
| **Plugin activation** | All required plugins are active (Service Portal, Flow Designer, PA, SLA, ATF) | ✅ |
| **Instance maintenance** | Maintenance window confirmed with IT operations | ✅ |
| **Backup** | Full instance backup completed before deployment | ✅ |

### 1.2 Artifact Readiness

| Check | Description | Verified |
| ------- | ------------- | ---------- |
| **Update set export** | Update set exported from DEV instance as XML | ✅ |
| **Update set name** | `LIB-Smart-Library-Request-Workflow-v2.0` | ✅ |
| **Update set preview** | Preview executed on TEST instance with no conflicts | ✅ |
| **Update set committed** | Tested on TEST instance with all acceptance criteria passing | ✅ |
| **Sample data** | Sample data CSV files prepared for import | ✅ |
| **ATF test suite** | Full regression test suite executed on TEST | ✅ |

### 1.3 Stakeholder Readiness

| Check | Description | Verified |
| ------- | ------------- | ---------- |
| **UAT signed off** | All UAT criteria met and signed off by library stakeholders | ✅ |
| **Change management** | Change request approved per ITIL process | ✅ |
| **Communication** | Deployment announced to all users | ✅ |
| **Training** | Training materials distributed to librarians and students | ✅ |

### 1.4 Deployment Team

| Role | Person | Responsibility |
| ------ | -------- | ---------------- |
| Deployment Lead | System Admin | Execute deployment, monitor progress |
| Technical Support | ServiceNow Developer | Resolve deployment issues |
| Business Representative | Library Manager | Verify functionality post-deployment |
| QA Representative | QA Engineer | Execute smoke tests post-deployment |

---

## 2. Update Set Export Procedure (Dev Instance)

### 2.1 Prepare Update Set

1. Navigate to **System Update Sets** → **Update Sets** on the source development instance.
2. Locate the update set `LIB-Smart-Library-Request-Workflow-v2.0`.
3. Verify that all application artifacts are captured:
   - **Application:** x_univ_library
   - **Tables:** 11 tables
   - **Script Includes:** 5
   - **Business Rules:** 15
   - **UI Policies:** 8
   - **UI Actions:** 10
   - **Client Scripts:** 12
   - **Flow Designer flows:** 3
   - **Notifications:** 13
   - **Email Templates:** 13
   - **Roles:** 4
   - **ACL records:** ~36
   - **Portal:** 1 portal with 8 pages
   - **Widgets:** 6 custom widgets
   - **Reports:** 8
   - **Dashboards:** 2
   - **PA Indicators:** 6
   - **Scheduled Jobs:** 8
   - **SLA Definitions:** 3
   - **Catalog Items:** 1 record producer
   - **Configuration records:** 8 parameter records
   - **Sample data:** 5 import sets

### 2.2 Verify Update Set Completeness

1. Navigate to **System Update Sets** → **Update Set Validation & Preview**.
2. Select the update set.
3. Run **Update Set Validation** — verify no errors.
4. Run **Retrieve Update Set Report** — review the report for:

| Section | What to Verify |
| --------- | ---------------- |
| **Application files** | All application artifacts are listed |
| **Configuration files** | All configuration items are listed |
| **ACLs** | All ACL records are captured |
| **Properties** | All system properties are captured |
| **System properties** | No missing dependencies |

### 2.3 Export Update Set

1. Navigate to **System Update Sets** → **Update Sets**.
2. Open the update set record.
3. Click **Export to XML**.
4. Choose **All application records** for scope.
5. Click **Export**.
6. Save the XML file as `LIB-Smart-Library-Request-Workflow-v2.0.xml`.
7. Verify the file size is reasonable (>100KB for this application).

### 2.4 Export Supporting Artifacts

In addition to the update set, export the following supporting artifacts separately:

| Artifact | Export Method | File Name |
| ---------- | --------------- | ----------- |
| ATF Test Suite | ATF → Export | `LIB-ATF-TestSuite.xml` |
| PA Scorecards | PA → Export | `LIB-PA-Scorecards.xml` |
| Portal Theme CSS | Style → Export | `LIB-Portal-Theme.css` |

---

## 3. Import Update Set to Production

### 3.1 Import Preparation

1. Log in to the target production instance.
2. Navigate to **System Update Sets** → **Update Sets**.
3. Click **Import Update Set from XML**.
4. Upload the exported XML file.

### 3.2 Preview Update Set

1. Open the imported update set.
2. Click **Preview Update Set**.
3. Review the preview report for:

| Issue | Action |
| ------- | -------- |
| **Conflicts** | Resolve by choosing "Skip" or "Use remote" |
| **Dependencies** | Install missing dependencies first |
| **Skipped records** | Evaluate if acceptable |
| **Errors** | Fix before proceeding to commit |

### 3.3 Commit Update Set

1. After preview passes without blocking issues, click **Commit Update Set**.
2. Monitor the commit progress:
   - **Phase 1:** Application files (tables, fields, dictionary)
   - **Phase 2:** Server scripts (Business Rules, Script Includes)
   - **Phase 3:** Client scripts (UI Policies, Client Scripts, UI Actions)
   - **Phase 4:** Service Portal (pages, widgets, themes)
   - **Phase 5:** Notifications and email templates
   - **Phase 6:** Roles and ACLs
   - **Phase 7:** Reports, dashboards, PA indicators
   - **Phase 8:** Scheduled jobs, SLA definitions
   - **Phase 9:** Catalog items and configurations
3. Verify that all phases complete with **Success** status.
4. Note any warnings — resolve if critical, document if non-critical.

### 3.4 Post-Commit Verification

1. Navigate to **System Applications** → **Applications**.
2. Open `x_univ_library`.
3. Verify the version shows `2.0.0`.

---

## 4. Post-Deployment Verification Steps

### 4.1 Table Verification

| Table Name | Action | Expected Result |
| ----------- | -------- | ----------------- |
| u_library_categories | Navigate to table | Structure correct with all fields |
| u_library_books | Navigate to table | Structure correct with all fields |
| u_library_students | Navigate to table | Structure correct with all fields |
| u_library_librarians | Navigate to table | Structure correct with all fields |
| u_library_borrow_requests | Navigate to table | Structure correct with all fields |
| u_library_approvals | Navigate to table | Structure correct with all fields |
| u_library_issuance_records | Navigate to table | Structure correct with all fields |
| u_library_return_records | Navigate to table | Structure correct with all fields |
| u_library_notification_log | Navigate to table | Structure correct with all fields |
| u_library_configuration | Navigate to table | Structure correct with all fields |
| u_library_audit_log | Navigate to table | Structure correct with all fields |

### 4.2 Configuration Verification

1. Navigate to **u_library_configuration** table.
2. Verify all 8 default parameter records exist:

| Key | Expected Value |
| ----- | --------------- |
| `default_loan_period_days` | `14` |
| `max_borrow_limit_default` | `5` |
| `approval_escalation_hours` | `48` |
| `overdue_reminder_days` | `1,3,7,14` |
| `notifications_enabled` | `true` |
| `email_notifications_enabled` | `true` |
| `portal_search_enabled` | `true` |
| `damage_report_notification_enabled` | `true` |

### 4.3 Script Include Verification

Navigate to **System Definition** → **Script Includes** → filter by `x_univ_library`:

| Script Include | Verification |
| ---------------- | ------------- |
| LibraryBorrowService | Open and verify syntax check passes |
| LibraryAvailabilityService | Open and verify syntax check passes |
| LibraryNotificationHelper | Open and verify syntax check passes |
| LibraryValidationService | Open and verify syntax check passes |
| LibraryAuditLogger | Open and verify syntax check passes |

### 4.4 Business Rule Verification

Navigate to **System Definition** → **Business Rules** → filter by `x_univ_library`:

- Confirm all 15 Business Rules exist and are active.
- Spot-check 3 rules by manually triggering conditions and verifying behavior.

### 4.5 UI Action Verification

Navigate to each table and verify UI Action buttons appear:

| Table | Button | Role Required |
| ------- | -------- | --------------- |
| u_library_borrow_requests | `Issue Book` | Librarian |
| u_library_borrow_requests | `Process Return` | Librarian |
| u_library_borrow_requests | `Cancel Request` | Student (own request) |
| u_library_borrow_requests | `Approve Request` | Librarian |
| u_library_borrow_requests | `Reject Request` | Librarian |
| u_library_books | `Add to Catalog` | Librarian |
| u_library_issuance_records | `Print Borrow Slip` | Librarian |
| u_library_borrow_requests | `Send Reminder` | Librarian |
| u_library_issuance_records | `Create Return from Issuance` | Librarian |
| u_library_books | `Export to CSV` | Librarian |

### 4.6 Flow Designer Verification

1. Navigate to **Process Automation** → **Flow Designer**.
2. Verify all 3 flows exist and are active.
3. For each flow, click **Test** and run the flow in test mode with sample data.
4. Verify flow execution completes without errors.

### 4.7 Service Portal Verification

1. Navigate to `https://[instance]/library`.
2. Verify all 8 pages load without errors.
3. Test navigation between pages.
4. Verify custom widgets render correctly.
5. Test with different roles (switch user).

### 4.8 Notification Verification

1. Navigate to **System Notification** → **Email** → **Notifications**.
2. Filter by `x_univ_library` scope.
3. Verify all 13 notification records exist.
4. Send a test email for each notification.
5. Verify email templates render with correct data.

### 4.9 Role and ACL Verification

1. Navigate to **User Administration** → **Roles**.
2. Verify all 4 roles exist:
   - `x_univ_library.student_library`
   - `x_univ_library.librarian_library`
   - `x_univ_library.library_manager`
   - `x_univ_library.library_admin`
3. For each role, switch user and verify ACLs are enforced per the permission matrix.

### 4.10 Scheduled Job Activation

1. Navigate to **System Definition** → **Scheduled Jobs**.
2. Filter by `x_univ_library`.
3. Verify all 8 scheduled jobs exist.
4. Activate each job by setting **Active** to `true`.
5. Run each job manually to verify it executes without errors.

### 4.11 SLA Verification

1. Navigate to **SLA** → **SLA Definitions**.
2. Verify all 3 SLA definitions exist and are active.
3. Create a test borrow request and verify the SLA timer starts.

### 4.12 Smoke Test

Execute the following smoke test scenarios:

| Scenario | Steps | Expected Result |
| ---------- | ------- | ---------------- |
| Student creates request | 1. Log in as student. 2. Navigate to catalog. 3. Select book. 4. Submit. | Request created with status Pending Approval |
| Librarian approves | 1. Log in as librarian. 2. Open request. 3. Approve. | Status changes to Approved |
| Librarian issues book | 1. Open approved request. 2. Click Issue Book. | Issuance record created, copies decremented |
| Librarian processes return | 1. Open issued request. 2. Click Process Return. | Return record created, copies incremented |
| Student views requests | 1. Log in as student. 2. Navigate to My Requests. | All requests visible with correct statuses |
| Librarian views dashboard | 1. Log in as librarian. 2. Navigate to Operations portal page. | Dashboard widgets display data |

---

## 5. Rollback Procedure

### 5.1 Full Rollback

If critical issues are discovered after deployment, execute a full rollback:

1. **Restore instance backup:** Restore the production instance from the pre-deployment backup.
2. **Verify restoration:** Navigate to key tables and verify pre-deployment data is intact.
3. **Communicate:** Notify stakeholders of the rollback and revised deployment schedule.

### 5.2 Partial Rollback (Update Set Removal)

If only the update set needs to be rolled back (without full instance restore):

1. Navigate to **System Update Sets** → **Update Sets**.
2. Locate the update set `LIB-Smart-Library-Request-Workflow-v2.0`.
3. Click **Rollback Update Set**.
4. Confirm the rollback action.
5. Verify rollback:
   - Tables have been removed (or reverted if they existed before)
   - Business Rules are no longer active
   - Portal pages are removed
   - Roles are removed
   - Reports and dashboards are removed

> **Note:** Rollback can only revert changes made by the update set. Data created by the update set (e.g., sample data, test records) must be deleted manually.

### 5.3 Data Rollback

To remove imported sample data:

1. Navigate to each table:
   - `u_library_categories`
   - `u_library_books`
   - `u_library_students`
   - `u_library_librarians`
   - `u_library_borrow_requests`
2. For each table, select all records imported during deployment.
3. Click **Delete** to remove them.

---

## 6. Environment Promotion Strategy

### 6.1 Environment Architecture

```text
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│                 │     │                 │     │                 │
│   Development   │────▶│    Testing      │────▶│   Production    │
│   (DEV)         │     │   (TEST)        │     │   (PROD)        │
│                 │     │                 │     │                 │
└─────────────────┘     └─────────────────┘     └─────────────────┘
        │                       │                        │
        │                       │                        │
        ▼                       ▼                        ▼
  Developer builds        QA executes tests         End users
  and tests features      UAT verification          use application
```

### 6.2 Promotion Process

| Step | From | To | Action | Responsible |
| ------ | ------ | ---- | -------- | ------------- |
| 1 | DEV | — | Develop and unit test all artifacts | Developer |
| 2 | DEV | — | Create update set | Developer |
| 3 | DEV | TEST | Export update set from DEV, import to TEST | Developer |
| 4 | TEST | — | Preview and commit on TEST | QA Engineer |
| 5 | TEST | — | Execute ATF regression test suite | QA Engineer |
| 6 | TEST | — | Execute UAT with stakeholders | BA Team |
| 7 | TEST | — | Fix defects found in UAT (iterate to Step 1) | Developer |
| 8 | TEST | PROD | Export final update set from DEV, import to PROD | Deployment Lead |
| 9 | PROD | — | Preview and commit on PROD | Deployment Lead |
| 10 | PROD | — | Post-deployment verification | Deployment Team |
| 11 | PROD | — | Smoke test | QA Engineer |

### 6.3 Version Control

Each promotion cycle must maintain version alignment:

| Environment | Application Version | Update Set Version |
| ------------- | ------------------- | ------------------- |
| Development | 2.0.0 | LIB-Smart-Library-Request-Workflow-v2.0 |
| Testing | 2.0.0 | LIB-Smart-Library-Request-Workflow-v2.0 |
| Production | 2.0.0 | LIB-Smart-Library-Request-Workflow-v2.0 |

### 6.4 Hotfix Process

For critical production issues:

1. Create a hotfix branch in the development environment.
2. Implement the fix and create a separate hotfix update set.
3. Test the hotfix in the TEST environment.
4. Import and commit the hotfix update set in production.
5. Merge the hotfix back into the main development branch.

---

## 7. Deployment Artifacts Summary

| Artifact | File Name | Size | Location |
| ---------- | ----------- | ------ | ---------- |
| Main Update Set | `LIB-Smart-Library-Request-Workflow-v2.0.xml` | ~2.5 MB | DEV export |
| ATF Test Suite | `LIB-ATF-TestSuite.xml` | ~500 KB | DEV export |
| PA Scorecards | `LIB-PA-Scorecards.xml` | ~200 KB | DEV export |
| Portal Theme CSS | `LIB-Portal-Theme.css` | ~50 KB | DEV export |
| Sample Data — Categories | `categories.csv` | ~1 KB | Repository |
| Sample Data — Books | `books.csv` | ~3 KB | Repository |
| Sample Data — Students | `students.csv` | ~1 KB | Repository |
| Sample Data — Librarians | `librarians.csv` | ~1 KB | Repository |
| Sample Data — Borrow Requests | `borrow_requests.csv` | ~2 KB | Repository |

---

## 8. Rollback Verification Checklist

After a rollback, verify:

| Item | Verification |
| ------ | -------------- |
| Tables | Deleted or reverted to pre-deployment state |
| Access | Users can log in without errors |
| Other applications | No impact from rollback |
| Instance performance | Normal operation confirmed |
| Data integrity | Pre-deployment data intact |

---

*References: [ImplementationGuide.md](ImplementationGuide.md) | [Troubleshooting.md](Troubleshooting.md)*
*See also: [MaintenanceGuide.md](MaintenanceGuide.md)*
