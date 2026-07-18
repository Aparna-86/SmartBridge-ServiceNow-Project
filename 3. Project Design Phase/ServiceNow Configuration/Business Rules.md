# Business Rules Design

# Smart Library Request Workflow — ServiceNow Enterprise Solution

> **Document Type:** Business Rules Design  
> **Version:** 2.0.0  
> **Application Scope:** `x_univ_library`  
> **Status:** Final — Complete

---

## Overview

This document specifies all 15 Business Rules for the Smart Library Request Workflow. Each rule includes its purpose, trigger conditions, pseudocode logic, and the requirement it satisfies.

All Business Rules delegate reusable logic to Script Includes (ref. NFR-05-AC-1).

---

## BR-01: Availability Auto-Update

| Attribute | Value |
| ----------- | ------- |
| **Name** | `[LIB] Availability Auto-Update` |
| **Table** | `u_library_books` |
| **When** | Before Update |
| **Condition** | `current.u_available_copies.changes()` |
| **Requirement** | FR-01-AC-5, FR-01-AC-6 |

**Purpose:** Automatically set `u_availability_status` to "Available" or "Unavailable" when `u_available_copies` changes.

```javascript
(function executeRule(current, previous) {
    var newCount = parseInt(current.u_available_copies.getValue());
    var oldCount = parseInt(previous.u_available_copies);

    if (newCount === 0 && oldCount > 0) {
        current.u_availability_status = 'unavailable';
    } else if (newCount > 0 && oldCount === 0) {
        current.u_availability_status = 'available';
    }
})(current, previous);
```

---

## BR-02: Default Available Copies on New Book

| Attribute | Value |
| ----------- | ------- |
| **Name** | `[LIB] Default Available Copies` |
| **Table** | `u_library_books` |
| **When** | Before Insert |
| **Condition** | Always (new record) |
| **Requirement** | FR-01-AC-3 |

**Purpose:** When a new book is created, set `available_copies = total_copies`.

```javascript
(function executeRule(current, previous) {
    if (current.u_available_copies.nil()) {
        current.u_available_copies = current.u_total_copies.getValue();
    }
    if (current.u_total_copies > 0) {
        current.u_availability_status = 'available';
    }
})(current, previous);
```

---

## BR-03: ISBN Uniqueness Check

| Attribute | Value |
| ----------- | ------- |
| **Name** | `[LIB] ISBN Uniqueness Check` |
| **Table** | `u_library_books` |
| **When** | Before Insert and Before Update |
| **Condition** | `current.u_isbn.changes() OR current.isNewRecord()` |
| **Requirement** | FR-01-AC-2 |

```javascript
(function executeRule(current, previous) {
    var isbn = current.u_isbn.getValue();
    if (!isbn) return;

    var gr = new GlideRecord('u_library_books');
    gr.addQuery('u_isbn', isbn);
    if (!current.isNewRecord()) {
        gr.addQuery('sys_id', '!=', current.sys_id.getValue());
    }
    gr.query();

    if (gr.next()) {
        current.setAbortAction(true);
        gs.addErrorMessage('A book with this ISBN already exists.');
    }
})(current, previous);
```

---

## BR-04: Total Copies Guard

| Attribute | Value |
| ----------- | ------- |
| **Name** | `[LIB] Total Copies Guard` |
| **Table** | `u_library_books` |
| **When** | Before Update |
| **Condition** | `current.u_total_copies.changesTo() < previous.u_total_copies` |
| **Requirement** | FR-01-AC-4 |

```javascript
(function executeRule(current, previous) {
    var newTotal   = parseInt(current.u_total_copies.getValue());
    var borrowed   = parseInt(previous.u_total_copies) - parseInt(previous.u_available_copies);

    if (newTotal < borrowed) {
        current.setAbortAction(true);
        gs.addErrorMessage('Total copies cannot be less than currently borrowed copies.');
    }
})(current, previous);
```

---

## BR-05: Book Deactivation Guard

| Attribute | Value |
| ----------- | ------- |
| **Name** | `[LIB] Book Deactivation Guard` |
| **Table** | `u_library_books` |
| **When** | Before Update |
| **Condition** | `current.u_active.changesTo(false)` |
| **Requirement** | FR-01-AC-9 |

```javascript
(function executeRule(current, previous) {
    var gr = new GlideRecord('u_library_borrow_requests');
    gr.addQuery('u_book', current.sys_id.getValue());
    gr.addQuery('u_status', 'IN', 'pending_approval,approved,issued');
    gr.query();

    if (gr.getRowCount() > 0) {
        current.setAbortAction(true);
        gs.addErrorMessage('Cannot deactivate this book. ' +
            gr.getRowCount() + ' active borrow request(s) exist.');
    }
})(current, previous);
```

---

## BR-06: Borrow Limit Enforcement

| Attribute | Value |
| ----------- | ------- |
| **Name** | `[LIB] Borrow Limit Enforcement` |
| **Table** | `u_library_borrow_requests` |
| **When** | Before Insert |
| **Condition** | Always (new request) |
| **Requirement** | FR-05-AC-5 |

```javascript
(function executeRule(current, previous) {
    var svc = new LibraryValidationService();
    var result = svc.validateBorrowRequest(
        current.u_book.getValue(),
        current.u_student.getValue()
    );
    if (!result.valid) {
        current.setAbortAction(true);
        gs.addErrorMessage(result.message);
    }
})(current, previous);
```

---

## BR-07: Overdue Student Block

| Attribute | Value |
| ----------- | ------- |
| **Name** | `[LIB] Overdue Student Block` |
| **Table** | `u_library_borrow_requests` |
| **When** | Before Insert |
| **Condition** | Always (new request) |
| **Requirement** | FR-05-AC-6 |

```javascript
(function executeRule(current, previous) {
    var studentSysId = current.u_student.getValue();
    var gr = new GlideRecord('u_library_borrow_requests');
    gr.addQuery('u_student', studentSysId);
    gr.addQuery('u_overdue_flag', true);
    gr.addQuery('u_status', '!=', 'returned');
    gr.query();

    if (gr.next()) {
        current.setAbortAction(true);
        gs.addErrorMessage('You have overdue books. Please return them before requesting new books.');
    }
})(current, previous);
```

---

## BR-08: Duplicate Request Prevention

| Attribute | Value |
| ----------- | ------- |
| **Name** | `[LIB] Duplicate Request Prevention` |
| **Table** | `u_library_borrow_requests` |
| **When** | Before Insert |
| **Condition** | Always (new request) |
| **Requirement** | FR-05-AC-11 |

```javascript
(function executeRule(current, previous) {
    var gr = new GlideRecord('u_library_borrow_requests');
    gr.addQuery('u_book', current.u_book.getValue());
    gr.addQuery('u_student', current.u_student.getValue());
    gr.addQuery('u_status', 'IN', 'pending_approval,approved');
    gr.query();

    if (gr.next()) {
        current.setAbortAction(true);
        gs.addErrorMessage('You already have an active request for this book.');
    }
})(current, previous);
```

---

## BR-09: Availability Decrement on New Request

| Attribute | Value |
| ----------- | ------- |
| **Name** | `[LIB] Availability Decrement on Request` |
| **Table** | `u_library_borrow_requests` |
| **When** | After Insert |
| **Condition** | `current.u_status == 'pending_approval'` |
| **Requirement** | FR-05-AC-7 |

```javascript
(function executeRule(current, previous) {
    var svc = new LibraryAvailabilityService();
    svc.decrementAvailability(current.u_book.getValue());
})(current, previous);
```

---

## BR-10: Availability Restore on Rejection/Cancellation

| Attribute | Value |
| ----------- | ------- |
| **Name** | `[LIB] Availability Restore on Reject` |
| **Table** | `u_library_borrow_requests` |
| **When** | After Update |
| **Condition** | `current.u_status.changesTo('rejected') OR current.u_status.changesTo('cancelled')` |
| **Requirement** | FR-06-AC-3, FR-08 |

```javascript
(function executeRule(current, previous) {
    var svc = new LibraryAvailabilityService();
    svc.incrementAvailability(current.u_book.getValue());
})(current, previous);
```

---

## BR-11: Availability Restore on Return

| Attribute | Value |
| ----------- | ------- |
| **Name** | `[LIB] Availability Restore on Return` |
| **Table** | `u_library_return_records` |
| **When** | After Insert |
| **Condition** | Always (new return record) |
| **Requirement** | FR-08-AC-1 |

```javascript
(function executeRule(current, previous) {
    // Get the borrow request
    var requestGr = new GlideRecord('u_library_borrow_requests');
    if (requestGr.get(current.u_borrow_request.getValue())) {
        // Set request status to Returned
        requestGr.u_status = 'returned';
        requestGr.update();

        // Restore book availability
        var svc = new LibraryAvailabilityService();
        svc.incrementAvailability(requestGr.u_book.getValue());

        // Update student active borrow count
        var studentGr = new GlideRecord('u_library_students');
        if (studentGr.get(requestGr.u_student.getValue())) {
            var count = parseInt(studentGr.u_active_borrow_count.getValue());
            studentGr.u_active_borrow_count = Math.max(0, count - 1);
            studentGr.update();
        }
    }
})(current, previous);
```

---

## BR-12: Overdue Detection (Async/Scheduled)

| Attribute | Value |
| ----------- | ------- |
| **Name** | `[LIB] Overdue Flag Set` |
| **Table** | `u_library_borrow_requests` |
| **When** | Called from Scheduled Job |
| **Condition** | Status = Issued AND expected_return_date < today |
| **Requirement** | FR-09-AC-1, FR-09-AC-2 |

```javascript
// Called from Scheduled Job: "Library - Daily Overdue Detection"
(function detectOverdueRequests() {
    var today = new GlideDateTime();
    var gr = new GlideRecord('u_library_borrow_requests');
    gr.addQuery('u_status', 'issued');

    var issGr = new GlideRecord('u_library_issuance_records');
    // Subquery: find issued requests whose expected_return_date < today
    // Implementation detail: use GlideDateTime comparison

    gr.query();
    while (gr.next()) {
        if (!gr.u_overdue_flag.getValue()) {
            gr.u_overdue_flag = true;
            gr.u_status = 'overdue';
            gr.update();
        }
    }
})();
```

---

## BR-13: Student Profile Auto-Create on Role Assignment

| Attribute | Value |
| ----------- | ------- |
| **Name** | `[LIB] Auto-Create Student Profile` |
| **Table** | `sys_user_has_role` |
| **When** | After Insert |
| **Condition** | `current.role.name == 'student_library'` |
| **Requirement** | FR-03-AC-3 |

```javascript
(function executeRule(current, previous) {
    if (current.role.name != 'student_library') return;

    var userSysId = current.user.getValue();

    // Check if profile already exists
    var gr = new GlideRecord('u_library_students');
    gr.addQuery('u_user', userSysId);
    gr.query();
    if (gr.next()) return; // Profile already exists

    // Create new student profile
    var newGr = new GlideRecord('u_library_students');
    newGr.u_user = userSysId;
    newGr.u_max_borrow_limit = 5; // Default from configuration
    newGr.u_active_borrow_count = 0;
    newGr.u_active = true;
    newGr.insert();
})(current, previous);
```

---

## BR-14: Librarian Profile Auto-Create on Role Assignment

| Attribute | Value |
| ----------- | ------- |
| **Name** | `[LIB] Auto-Create Librarian Profile` |
| **Table** | `sys_user_has_role` |
| **When** | After Insert |
| **Condition** | `current.role.name == 'librarian_library'` |
| **Requirement** | FR-04-AC-3 |

```javascript
(function executeRule(current, previous) {
    if (current.role.name != 'librarian_library') return;

    var userSysId = current.user.getValue();
    var gr = new GlideRecord('u_library_librarians');
    gr.addQuery('u_user', userSysId);
    gr.query();
    if (gr.next()) return;

    var newGr = new GlideRecord('u_library_librarians');
    newGr.u_user = userSysId;
    newGr.u_active = true;
    newGr.insert();
})(current, previous);
```

---

## BR-15: Audit Event Logger

| Attribute | Value |
| ----------- | ------- |
| **Name** | `[LIB] Audit Event Logger` |
| **Table** | Multiple (books, categories, students, borrow_requests, approvals) |
| **When** | After Insert, After Update, After Delete |
| **Condition** | Always |
| **Requirement** | FR-14-AC-1 |

```javascript
(function executeRule(current, previous) {
    var logger = new LibraryAuditLogger();
    var action = current.operation(); // insert / update / delete

    if (action === 'update') {
        // Log each changed field
        var fields = ['u_status', 'u_active', 'u_available_copies', 'u_title'];
        fields.forEach(function(field) {
            if (current[field].changes()) {
                logger.logEvent(
                    current.getTableName(),
                    current.sys_id.getValue(),
                    'Updated',
                    field,
                    previous[field],
                    current[field].getValue()
                );
            }
        });
    } else {
        logger.logEvent(
            current.getTableName(),
            current.sys_id.getValue(),
            action === 'insert' ? 'Created' : 'Deleted',
            null, null, null
        );
    }
})(current, previous);
```

---

*References: [requirements.md](../../.kiro/specs/smart-library-request-workflow/requirements.md) — FR-01 through FR-09, FR-14*  
*See also: [ScriptIncludes.md](ScriptIncludes.md) | [FlowDesigner.md](FlowDesigner.md)*
