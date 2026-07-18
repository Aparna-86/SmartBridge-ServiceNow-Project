# UI Actions — Smart Library Request Workflow

**Scope:** `x_univ_library`  
**Platform:** ServiceNow Washington DC

---

## 1. Create Book

| Property | Value |
| --- | --- |
| **Name** | Library Books Create Book |
| **Table** | `u_library_books` |
| **Type** | List Button |
| **Condition** | `g_user.hasRole('snc_library_librarian')` |
| **Role** | `snc_library_librarian` |
| **Action** | Client-side: Opens a new record form for `u_library_books` with default values pre-populated (status = "Available", active = true). Librarian fills in title, author, ISBN, category, total copies, and shelf location. On submit, `LibraryBookManager.createBook()` validates ISBN uniqueness and creates the record. |

**Script:**

```javascript
// Client-side: navigate to new book form
action.setRedirectURL('u_library_books.do?sys_id=-1&sysparm_query=u_status=Available^u_active=true');
```

---

## 2. Deactivate Book

| Property | Value |
| --- | --- |
| **Name** | Library Books Deactivate Book |
| **Table** | `u_library_books` |
| **Type** | Form Button |
| **Condition** | `current.u_status != 'Inactive'` |
| **Role** | `snc_library_librarian` |
| **Action** | Sets `u_status = 'Inactive'` and `u_active = false`. Verifies no active issuances exist before deactivation. Logs the change in `u_library_audit_log`. |

**Script:**

```javascript
(function executeAction() {
    var bookManager = new LibraryBookManager();
    var result = bookManager.deactivateBook(current);
    if (result.success) {
        action.setRedirectURL(current);
        gs.addInfoMessage(gs.getMessage('Book deactivated successfully.'));
    } else {
        gs.addErrorMessage(result.errorMessage);
    }
})();
```

---

## 3. Activate Book

| Property | Value |
| --- | --- |
| **Name** | Library Books Activate Book |
| **Table** | `u_library_books` |
| **Type** | Form Button |
| **Condition** | `current.u_status == 'Inactive'` |
| **Role** | `snc_library_librarian` |
| **Action** | Restores `u_status = 'Available'` and `u_active = true`. Logs the change in `u_library_audit_log`. |

**Script:**

```javascript
(function executeAction() {
    current.u_status = 'Available';
    current.u_active = true;
    current.update();
    gs.addInfoMessage(gs.getMessage('Book reactivated successfully.'));
    action.setRedirectURL(current);
})();
```

---

## 4. Cancel Request

| Property | Value |
| --- | --- |
| **Name** | Library Borrow Requests Cancel Request |
| **Table** | `u_library_borrow_requests` |
| **Type** | Form Button |
| **Condition** | `current.u_status == 'Draft' \|\| current.u_status == 'Pending'` |
| **Role** | `snc_library_student` |
| **Action** | Updates `u_status = 'Cancelled'` and sets `u_decision_date` to current timestamp. Cancels all pending approvals linked to the request. Sends cancellation notification via `LibraryNotificationManager`. Only the requesting student or a librarian can cancel. |

**Script:**

```javascript
(function executeAction() {
    var requestManager = new LibraryBorrowRequestManager();
    requestManager.cancelRequest(current, gs.getUserID());
    gs.addInfoMessage(gs.getMessage('Request cancelled.'));
    action.setRedirectURL(current);
})();
```

---

## 5. Process Return

| Property | Value |
| --- | --- |
| **Name** | Library Issuance Records Process Return |
| **Table** | `u_library_issuance_records` |
| **Type** | List Button |
| **Condition** | `current.u_status == 'Issued' \|\| current.u_status == 'Overdue'` |
| **Role** | `snc_library_librarian` |
| **Action** | Opens the Return Record form (`u_library_return_records`) pre-populated with data from the issuance record: book, student, issuer, and due date. The librarian inspects the book condition, sets condition status, calculates any late fee, and submits. On submit, the system updates the issuance record status to "Returned" and increments available copies. |

**Script:**

```javascript
(function executeAction() {
    var returnRec = new GlideRecord('u_library_return_records');
    returnRec.initialize();
    returnRec.u_issuance_record = current.getUniqueValue();
    returnRec.u_book = current.u_book;
    returnRec.u_student = current.u_student;
    returnRec.u_book.setDisplayValue(current.u_book.getDisplayValue());
    returnRec.u_student.setDisplayValue(current.u_student.getDisplayValue());
    var returnId = returnRec.insert();
    action.setRedirectURL('u_library_return_records.do?sys_id=' + returnId);
})();
```

---

## 6. Issue Book

| Property | Value |
| --- | --- |
| **Name** | Library Borrow Requests Issue Book |
| **Table** | `u_library_borrow_requests` |
| **Type** | Form Button |
| **Condition** | `current.u_status == 'Approved'` |
| **Role** | `snc_library_librarian` |
| **Action** | Creates an Issuance Record (`u_library_issuance_records`) for the approved borrow request. Sets issue date to now and due date based on `u_borrow_duration_days`. Decrements `u_available_copies` on the book. Updates borrow request status to "Issued". Sends issuance notification to the student. |

**Script:**

```javascript
(function executeAction() {
    var requestManager = new LibraryBorrowRequestManager();
    var result = requestManager.issueBook(current, gs.getUserID());
    if (result.success) {
        gs.addInfoMessage(gs.getMessage('Book issued successfully.'));
    } else {
        gs.addErrorMessage(result.errorMessage);
    }
    action.setRedirectURL(current);
})();
```

---

## 7. Escalate Approval

| Property | Value |
| --- | --- |
| **Name** | Library Approvals Escalate Approval |
| **Table** | `u_library_approvals` |
| **Type** | Form Button |
| **Condition** | `current.u_approval_group == 'librarian_library' && current.u_decision == 'Pending'` |
| **Role** | `snc_library_manager` |
| **Action** | Escalates the approval to the `library_manager` group. Updates `u_approval_group` to "library_manager", sets `u_escalated_to` to the current manager, records `u_escalation_date`, and extends the SLA deadline by 72 hours. Calls `LibraryApprovalManager.escalate()` to reassign and notify the next-level approver. |

**Script:**

```javascript
(function executeAction() {
    var approvalManager = new LibraryApprovalManager();
    approvalManager.escalate(current, gs.getUserID());
    gs.addInfoMessage(gs.getMessage('Approval escalated to library manager.'));
    action.setRedirectURL(current);
})();
```

---

## 8. Override Decision

| Property | Value |
| --- | --- |
| **Name** | Library Approvals Override Decision |
| **Table** | `u_library_approvals` |
| **Type** | Form Button |
| **Condition** | `current.u_decision != 'Pending' && current.u_decision != ''` |
| **Role** | `snc_library_admin` |
| **Action** | Allows a library admin to override an existing approval decision. Opens a confirmation dialog where the admin provides a reason for override. On confirmation, toggles the decision (Approved ↔ Rejected), logs the override in the audit log, and triggers the appropriate notification and borrow request status update. |

**Script:**

```javascript
(function executeAction() {
    var approvalManager = new LibraryApprovalManager();
    var reason = gs.getProperty('override.reason', '');
    if (reason) {
        approvalManager.overrideDecision(current, gs.getUserID(), reason);
        gs.addInfoMessage(gs.getMessage('Decision overridden successfully.'));
        action.setRedirectURL(current);
    } else {
        gs.addErrorMessage(gs.getMessage('Override reason is required.'));
    }
})();
```

---

## 9. Export Report

| Property | Value |
| --- | --- |
| **Name** | Library Books Export Report |
| **Table** | `u_library_books` |
| **Type** | List Button |
| **Condition** | `g_user.hasRole('snc_library_librarian')` |
| **Role** | `snc_library_librarian` |
| **Action** | Generates a CSV export of the current list view filtered results. Calls `LibraryReportEngine.exportToCSV()` which applies the current filter, aggregates specified fields, and streams the CSV file as a download. Columns include: Book ID, Title, Author, ISBN, Category, Status, Available Copies, Total Copies, Shelf Location. |

**Script:**

```javascript
(function executeAction() {
    var reportEngine = new LibraryReportEngine();
    var csvContent = reportEngine.exportBookListToCSV();
    action.setRedirectURL('/x_univ_library_report_export.do?format=csv&t=' + gs.nowDateTime());
})();
```

---

## 10. Refresh Dashboard

| Property | Value |
| --- | --- |
| **Name** | Library Refresh Dashboard |
| **Table** | — (Homepage/Dashboard context) |
| **Type** | List Button |
| **Condition** | `g_user.hasRole('snc_library_librarian')` |
| **Role** | `snc_library_librarian` |
| **Action** | Invalidates the dashboard cache and triggers a live data refresh for all dashboard panels. Calls `LibraryReportEngine.refreshDashboardData()` which re-queries all report sources, rebuilds the aggregate data, and updates the dashboard panels in-place. Displays a "Dashboard refreshed" confirmation banner. |

**Script:**

```javascript
(function executeAction() {
    var reportEngine = new LibraryReportEngine();
    reportEngine.refreshDashboardData();
    gs.addInfoMessage(gs.getMessage('Dashboard data refreshed successfully.'));
    location.reload();
})();
```
