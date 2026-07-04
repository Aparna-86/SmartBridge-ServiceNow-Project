# Client Scripts Design

# Smart Library Request Workflow — ServiceNow Enterprise Solution

> **Document Type:** Client Scripts Design  
> **Version:** 2.0.0  
> **Application Scope:** `x_univ_library`  
> **Status:** Final — Complete

---

## Overview

Twelve Client Scripts provide dynamic form behavior for the Smart Library Request Workflow. These scripts run in the user's browser and provide real-time validation, auto-population, and dynamic UI feedback.

---

## CS-01: Pickup Date Validation

| Attribute | Value |
| ----------- | ------- |
| **Name** | `[LIB] Validate Pickup Date` |
| **Table** | `u_library_borrow_requests` |
| **Type** | onChange |
| **Field** | `u_requested_pickup_date` |
| **Requirement** | FR-05-AC-2 |

```javascript
function onChange(control, oldValue, newValue, isLoading) {
    if (isLoading || !newValue) return;

    var today     = new Date();
    var picked    = new Date(newValue);
    var minDate   = new Date(today);
    var maxDate   = new Date(today);

    minDate.setDate(today.getDate() + 1);   // minimum: tomorrow
    maxDate.setDate(today.getDate() + 30);  // maximum: 30 days from now

    if (picked < minDate) {
        g_form.showFieldMsg('u_requested_pickup_date',
            'Pickup date must be at least 1 day from today.', 'error');
        g_form.setValue('u_requested_pickup_date', '');
        return;
    }

    if (picked > maxDate) {
        g_form.showFieldMsg('u_requested_pickup_date',
            'Pickup date cannot be more than 30 days from today.', 'error');
        g_form.setValue('u_requested_pickup_date', '');
        return;
    }

    g_form.hideFieldMsg('u_requested_pickup_date', true);
}
```

---

## CS-02: Notes Character Count

| Attribute | Value |
| ----------- | ------- |
| **Name** | `[LIB] Notes Character Counter` |
| **Table** | `u_library_borrow_requests` |
| **Type** | onChange |
| **Field** | `u_notes` |
| **Requirement** | FR-05-AC-2 |

```javascript
function onChange(control, oldValue, newValue, isLoading) {
    if (isLoading) return;

    var maxLength = 500;
    var remaining = maxLength - (newValue ? newValue.length : 0);

    if (remaining < 0) {
        g_form.showFieldMsg('u_notes',
            'Notes cannot exceed 500 characters. Current length: ' +
            newValue.length, 'error');
    } else if (remaining <= 50) {
        g_form.showFieldMsg('u_notes',
            remaining + ' characters remaining.', 'info');
    } else {
        g_form.hideFieldMsg('u_notes', true);
    }
}
```

---

## CS-03: Book Availability Check on Selection

| Attribute | Value |
| ----------- | ------- |
| **Name** | `[LIB] Book Availability Check` |
| **Table** | `u_library_borrow_requests` |
| **Type** | onChange |
| **Field** | `u_book` |
| **Requirement** | FR-05-AC-4, FR-15-AC-4 |

```javascript
function onChange(control, oldValue, newValue, isLoading) {
    if (isLoading || !newValue) return;

    var ga = new GlideAjax('LibraryValidationAjax');
    ga.addParam('sysparm_name', 'checkBookAvailability');
    ga.addParam('sysparm_book_sys_id', newValue);
    ga.getXMLAnswer(function(answer) {
        var result = JSON.parse(answer);
        if (!result.available) {
            g_form.showFieldMsg('u_book',
                'This book is currently unavailable. Please select a different book.',
                'error');
            g_form.setValue('u_book', '');
        } else {
            g_form.showFieldMsg('u_book',
                result.availableCopies + ' copy/copies available.',
                'info');
        }
    });
}
```

---

## CS-04: Auto-Populate Student from Current User

| Attribute | Value |
| ----------- | ------- |
| **Name** | `[LIB] Auto-Populate Student Field` |
| **Table** | `u_library_borrow_requests` |
| **Type** | onLoad |
| **Requirement** | FR-05-AC-1 (usability) |

```javascript

function onLoad() {
    if (!g_form.isNewRecord()) return;

    // Auto-populate the student field from the current logged-in user
    var ga = new GlideAjax('LibraryValidationAjax');
    ga.addParam('sysparm_name', 'getCurrentStudentProfile');
    ga.getXMLAnswer(function(answer) {
        var result = JSON.parse(answer);
        if (result.studentSysId) {
            g_form.setValue('u_student', result.studentSysId);
            g_form.setReadOnly('u_student', true);
        }
    });
}
```

---

## CS-05: Category Deactivation Book Count Warning

| Attribute | Value |
| ----------- | ------- |
| **Name** | `[LIB] Category Deactivation Warning` |
| **Table** | `u_library_categories` |
| **Type** | onChange |
| **Field** | `u_active` |
| **Requirement** | FR-02-AC-3 |

```javascript
function onChange(control, oldValue, newValue, isLoading) {
    if (isLoading || newValue === '1' || g_form.isNewRecord()) return;
    // newValue === '0' means being deactivated

    var categorySysId = g_form.getUniqueValue();
    var ga = new GlideAjax('LibraryValidationAjax');
    ga.addParam('sysparm_name', 'getBookCountForCategory');
    ga.addParam('sysparm_category_sys_id', categorySysId);
    ga.getXMLAnswer(function(answer) {
        var result = JSON.parse(answer);
        if (result.bookCount > 0) {
            g_form.showFieldMsg('u_active',
                'Warning: ' + result.bookCount + ' book(s) are currently associated with this category. ' +
                'Deactivating will prevent new books from using this category.',
                'warning');
        }
    });
}
```

---

## CS-06: Rejection Reason Validation on Submit

| Attribute | Value |
| ----------- | ------- |
| **Name** | `[LIB] Rejection Reason Required` |
| **Table** | `u_library_approvals` |
| **Type** | onSubmit |
| **Requirement** | FR-06-AC-3 |

```javascript

function onSubmit() {
    var decision = g_form.getValue('u_decision');
    var reason   = g_form.getValue('u_reason');

    if (decision === 'rejected' && (!reason || reason.trim() === '')) {
        g_form.showFieldMsg('u_reason',
            'A rejection reason is required when rejecting a request.', 'error');
        return false; // Prevent form submission
    }

    if (reason && reason.length > 500) {
        g_form.showFieldMsg('u_reason',
            'Rejection reason cannot exceed 500 characters.', 'error');
        return false;
    }

    return true;
}
```

---

## CS-07: Dynamic Status Color Indicator

| Attribute | Value |
| ----------- | ------- |
| **Name** | `[LIB] Status Color Indicator` |
| **Table** | `u_library_borrow_requests` |
| **Type** | onLoad and onChange (`u_status`) |
| **Requirement** | FR-15 (usability) |

```javascript

function onLoad() {
    applyStatusStyle();
}

function onChange(control, oldValue, newValue, isLoading) {
    if (!isLoading) applyStatusStyle();
}

function applyStatusStyle() {
    var status = g_form.getValue('u_status');
    var colorMap = {
        'pending_approval': '#f5a623',  // amber
        'approved':         '#7ed321',  // green
        'rejected':         '#d0021b',  // red
        'escalated':        '#ff5722',  // deep orange
        'issued':           '#4a90e2',  // blue
        'overdue':          '#d0021b',  // red
        'returned':         '#417505',  // dark green
        'cancelled':        '#9b9b9b'   // grey
    };

    var color = colorMap[status] || '#000000';
    var el = document.getElementById('element.u_status');
    if (el) {
        el.style.color = color;
        el.style.fontWeight = 'bold';
    }
}
```

---

## CS-08: Reference Filter — Active Books Only

| Attribute | Value |
| ----------- | ------- |
| **Name** | `[LIB] Filter Active Books` |
| **Table** | `u_library_borrow_requests` |
| **Type** | onLoad |
| **Requirement** | FR-01-AC-7 |

```javascript

function onLoad() {
    // Restrict book reference field to active books with available copies
    g_form.addOption('u_book_filter', 'u_active=true^u_available_copies>0', '');
    // Set reference qual: only active books with availability
    g_form.setRefFilter('u_book', 'u_active=true^u_available_copies>0');
}
```

---

## CS-09: Return Condition Auto-Set

| Attribute | Value |
| ----------- | ------- |
| **Name** | `[LIB] Auto-Set Return Condition` |
| **Table** | `u_library_return_records` |
| **Type** | onLoad |
| **Requirement** | FR-08-AC-2, FR-08-AC-3 |

```javascript

function onLoad() {
    if (!g_form.isNewRecord()) return;

    var requestSysId = g_form.getValue('u_borrow_request');
    if (!requestSysId) return;

    var ga = new GlideAjax('LibraryValidationAjax');
    ga.addParam('sysparm_name', 'getExpectedReturnDate');
    ga.addParam('sysparm_request_sys_id', requestSysId);
    ga.getXMLAnswer(function(answer) {
        var result = JSON.parse(answer);
        var today  = new Date();
        var dueDate = new Date(result.expectedReturnDate);

        if (today > dueDate) {
            g_form.setValue('u_return_condition', 'overdue');
            var overdueDays = Math.floor((today - dueDate) / (1000 * 60 * 60 * 24));
            g_form.setValue('u_overdue_days', overdueDays);
            g_form.showFieldMsg('u_return_condition',
                'This book is ' + overdueDays + ' day(s) overdue.', 'warning');
        } else {
            g_form.setValue('u_return_condition', 'on_time');
        }
    });
}
```

---

*References: [requirements.md](../../.kiro/specs/smart-library-request-workflow/requirements.md) — FR-05, FR-06, FR-08, NFR-06*  
*See also: [UIPolicies.md](UIPolicies.md) | [BusinessRules.md](BusinessRules.md)*
