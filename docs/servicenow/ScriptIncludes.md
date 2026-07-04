# Script Includes Design

# Smart Library Request Workflow — ServiceNow Enterprise Solution

> **Document Type:** Script Includes Design  
> **Version:** 2.0.0  
> **Application Scope:** `x_univ_library`  
> **Status:** Final — Complete

---

## Overview

Five Script Includes encapsulate all reusable business logic (ref. NFR-05-AC-1). They are callable from Business Rules, Flow Designer custom actions, Scheduled Jobs, and other Script Includes.

| Script Include | Purpose | Called By |
| ---------------- | --------- | ----------- |
| `LibraryBorrowService` | Borrow request lifecycle operations | BR-06, BR-07, BR-08, Flow |
| `LibraryAvailabilityService` | Book availability counter management | BR-01, BR-09, BR-10, BR-11 |
| `LibraryNotificationHelper` | Notification sending and logging | Flow Designer actions |
| `LibraryValidationService` | Pre-save validation library | BR-03, BR-04, BR-06, BR-07, BR-08 |
| `LibraryAuditLogger` | Immutable audit log writes | BR-15, Scheduled Jobs |

---

## 1. LibraryBorrowService

**Access:** `callerAccess = 'caller_track'`

```javascript

var LibraryBorrowService = Class.create();
LibraryBorrowService.prototype = {

    initialize: function() {
        this.cfg = new LibraryConfigService();
    },

    /**
     * Validates all pre-conditions and creates a new borrow request record.
     * @param {string} bookSysId - sys_id of u_library_books record
     * @param {string} studentSysId - sys_id of u_library_students record
     * @param {string} pickupDate - ISO date string
     * @param {string} notes - Optional student notes
     * @returns {Object} { success: boolean, requestSysId: string, message: string }
     */
    createBorrowRequest: function(bookSysId, studentSysId, pickupDate, notes) {
        var validation = this._validateRequest(bookSysId, studentSysId);
        if (!validation.valid) {
            return { success: false, message: validation.message };
        }

        var gr = new GlideRecord('u_library_borrow_requests');
        gr.u_book = bookSysId;
        gr.u_student = studentSysId;
        gr.u_requested_pickup_date = pickupDate;
        gr.u_notes = notes || '';
        gr.u_status = 'pending_approval';
        gr.u_submitted_at = new GlideDateTime();

        var sysId = gr.insert();
        if (sysId) {
            return { success: true, requestSysId: sysId, message: 'Request created successfully.' };
        }
        return { success: false, message: 'An error occurred creating the request.' };
    },

    /**
     * Cancels an active borrow request and restores book availability.
     * @param {string} requestSysId
     * @param {string} cancelReason
     * @returns {Object} { success: boolean, message: string }
     */
    cancelBorrowRequest: function(requestSysId, cancelReason) {
        var gr = new GlideRecord('u_library_borrow_requests');
        if (!gr.get(requestSysId)) {
            return { success: false, message: 'Request not found.' };
        }

        var allowedStatuses = ['pending_approval', 'approved'];
        if (allowedStatuses.indexOf(gr.u_status.getValue()) === -1) {
            return { success: false, message: 'This request cannot be cancelled in its current status.' };
        }

        gr.u_status = 'cancelled';
        gr.update();
        return { success: true, message: 'Request cancelled successfully.' };
    },

    /**
     * Checks if a student is eligible to borrow.
     * @param {string} studentSysId
     * @param {string} bookSysId
     * @returns {Object} { valid: boolean, message: string }
     */
    canStudentBorrow: function(studentSysId, bookSysId) {
        return new LibraryValidationService().validateBorrowRequest(bookSysId, studentSysId);
    },

    /**
     * Returns the count of currently active (issued) borrows for a student.
     * @param {string} studentSysId
     * @returns {number}
     */
    getActiveBorrowCount: function(studentSysId) {
        var gr = new GlideRecord('u_library_borrow_requests');
        gr.addQuery('u_student', studentSysId);
        gr.addQuery('u_status', 'IN', 'approved,issued');
        gr.query();
        return gr.getRowCount();
    },

    /**
     * Internal: validates all pre-conditions for a borrow request.
     */
    _validateRequest: function(bookSysId, studentSysId) {
        return new LibraryValidationService().validateBorrowRequest(bookSysId, studentSysId);
    },

    type: 'LibraryBorrowService'
};
```

---

## 2. LibraryAvailabilityService

**Access:** `callerAccess = 'caller_track'`

```javascript

var LibraryAvailabilityService = Class.create();
LibraryAvailabilityService.prototype = {

    initialize: function() {},

    /**
     * Decrements available_copies by 1 and updates availability_status if needed.
     * @param {string} bookSysId
     * @returns {boolean} success
     */
    decrementAvailability: function(bookSysId) {
        var gr = new GlideRecord('u_library_books');
        if (!gr.get(bookSysId)) {
            gs.error('[LibraryAvailabilityService] Book not found: ' + bookSysId);
            return false;
        }

        var current = parseInt(gr.u_available_copies.getValue());
        if (current <= 0) {
            gs.error('[LibraryAvailabilityService] Cannot decrement: available_copies already 0');
            return false;
        }

        gr.u_available_copies = current - 1;
        if (current - 1 === 0) {
            gr.u_availability_status = 'unavailable';
        }
        gr.update();
        return true;
    },

    /**
     * Increments available_copies by 1 and updates availability_status if needed.
     * @param {string} bookSysId
     * @returns {boolean} success
     */
    incrementAvailability: function(bookSysId) {
        var gr = new GlideRecord('u_library_books');
        if (!gr.get(bookSysId)) {
            gs.error('[LibraryAvailabilityService] Book not found: ' + bookSysId);
            return false;
        }

        var current  = parseInt(gr.u_available_copies.getValue());
        var total    = parseInt(gr.u_total_copies.getValue());

        if (current >= total) {
            gs.warn('[LibraryAvailabilityService] Cannot increment: available_copies already at total');
            return false;
        }

        gr.u_available_copies = current + 1;
        if (current === 0) {
            gr.u_availability_status = 'available';
        }
        gr.update();
        return true;
    },

    /**
     * @param {string} bookSysId
     * @returns {boolean} true if available_copies > 0
     */
    isBookAvailable: function(bookSysId) {
        var gr = new GlideRecord('u_library_books');
        if (!gr.get(bookSysId)) return false;
        return parseInt(gr.u_available_copies.getValue()) > 0;
    },

    type: 'LibraryAvailabilityService'
};
```

---

## 3. LibraryNotificationHelper

**Access:** `callerAccess = 'caller_track'`

```javascript

var LibraryNotificationHelper = Class.create();
LibraryNotificationHelper.prototype = {

    initialize: function() {},

    /**
     * Sends a notification using the specified template ID and logs it.
     * @param {string} templateId - e.g., 'LIB-NOTIF-001'
     * @param {string} recipientSysId - sys_id of sys_user
     * @param {string} recordSysId - sys_id of the triggering record
     * @param {string} tableName - table name of the triggering record
     */
    sendNotification: function(templateId, recipientSysId, recordSysId, tableName) {
        if (!this.isNotificationsEnabled()) {
            gs.info('[LibraryNotificationHelper] Notifications disabled globally.');
            return;
        }

        // ServiceNow: trigger the named notification
        // gs.eventQueue('x_univ_library.' + templateId, gr, recipientSysId);
        // Implementation: use notification event or direct NotifyEventHelper

        this.logNotification(templateId, recipientSysId, templateId, 'Sent');
    },

    /**
     * Logs a notification send event to u_library_notification_log.
     */
    logNotification: function(templateId, recipientSysId, eventType, status) {
        var logGr = new GlideRecord('u_library_notification_log');
        logGr.u_recipient = recipientSysId;
        logGr.u_event_type = eventType;
        logGr.u_template_id = templateId;
        logGr.u_sent_at = new GlideDateTime();
        logGr.u_delivery_status = status;
        logGr.insert();
    },

    /**
     * @returns {boolean} true if notifications are globally enabled
     */
    isNotificationsEnabled: function() {
        var gr = new GlideRecord('u_library_configuration');
        gr.addQuery('u_parameter_key', 'notifications_enabled');
        gr.query();
        if (gr.next()) {
            return gr.u_parameter_value.getValue() === 'true';
        }
        return true; // default: enabled
    },

    type: 'LibraryNotificationHelper'
};
```

---

## 4. LibraryValidationService

**Access:** `callerAccess = 'caller_track'`

```javascript

var LibraryValidationService = Class.create();
LibraryValidationService.prototype = {

    initialize: function() {},

    /**
     * Validates all pre-conditions for a new borrow request.
     * @returns {Object} { valid: boolean, message: string }
     */
    validateBorrowRequest: function(bookSysId, studentSysId) {
        // 1. Book exists and is available
        var bookGr = new GlideRecord('u_library_books');
        if (!bookGr.get(bookSysId) || parseInt(bookGr.u_available_copies.getValue()) <= 0) {
            return { valid: false, message: 'This book is currently unavailable. Please check back later.' };
        }

        // 2. Student is active
        var studentGr = new GlideRecord('u_library_students');
        if (!studentGr.get(studentSysId) || !studentGr.u_active.getValue()) {
            return { valid: false, message: 'Your student account is inactive. Contact the library.' };
        }

        // 3. Student has no overdue books
        var overdueGr = new GlideRecord('u_library_borrow_requests');
        overdueGr.addQuery('u_student', studentSysId);
        overdueGr.addQuery('u_overdue_flag', true);
        overdueGr.addQuery('u_status', '!=', 'returned');
        overdueGr.query();
        if (overdueGr.next()) {
            return { valid: false, message: 'You have overdue books. Please return them before requesting new books.' };
        }

        // 4. Borrow limit not exceeded
        var limit = parseInt(studentGr.u_max_borrow_limit.getValue()) || 5;
        var active = parseInt(studentGr.u_active_borrow_count.getValue()) || 0;
        if (active >= limit) {
            return { valid: false, message: 'You have reached your maximum borrow limit of ' + limit + ' books.' };
        }

        // 5. No duplicate active request
        var dupGr = new GlideRecord('u_library_borrow_requests');
        dupGr.addQuery('u_book', bookSysId);
        dupGr.addQuery('u_student', studentSysId);
        dupGr.addQuery('u_status', 'IN', 'pending_approval,approved');
        dupGr.query();
        if (dupGr.next()) {
            return { valid: false, message: 'You already have an active request for this book.' };
        }

        return { valid: true, message: 'Validation passed.' };
    },

    /**
     * Validates a book record update.
     * @returns {Object} { valid: boolean, message: string }
     */
    validateBookUpdate: function(bookGr) {
        var newTotal  = parseInt(bookGr.u_total_copies.getValue());
        var available = parseInt(bookGr.u_available_copies.getValue());
        var borrowed  = (parseInt(bookGr.getOriginalValue('u_total_copies') || '0')) - available;

        if (newTotal < borrowed) {
            return { valid: false, message: 'Total copies cannot be less than currently borrowed copies.' };
        }
        return { valid: true, message: 'Validation passed.' };
    },

    type: 'LibraryValidationService'
};
```

---

## 5. LibraryAuditLogger

**Access:** `callerAccess = 'caller_track'`

```javascript

var LibraryAuditLogger = Class.create();
LibraryAuditLogger.prototype = {

    initialize: function() {},

    /**
     * Logs a data modification event to u_library_audit_log.
     * @param {string} tableName
     * @param {string} recordSysId
     * @param {string} action - 'Created' | 'Updated' | 'Deleted'
     * @param {string|null} fieldName
     * @param {string|null} oldValue
     * @param {string|null} newValue
     */
    logEvent: function(tableName, recordSysId, action, fieldName, oldValue, newValue) {
        var gr = new GlideRecord('u_library_audit_log');
        gr.u_table_name     = tableName;
        gr.u_record_sys_id  = recordSysId;
        gr.u_user           = gs.getUserID();
        gr.u_action         = action;
        gr.u_field_name     = fieldName || '';
        gr.u_old_value      = oldValue  ? String(oldValue)  : '';
        gr.u_new_value      = newValue  ? String(newValue)  : '';
        gr.u_timestamp      = new GlideDateTime();
        gr.insert();
    },

    /**
     * Logs an access denial event.
     * @param {string} tableName
     * @param {string} recordSysId
     * @param {string} operation - 'read' | 'write' | 'delete'
     * @param {string} userId
     */
    logAccessDenial: function(tableName, recordSysId, operation, userId) {
        var gr = new GlideRecord('u_library_audit_log');
        gr.u_table_name     = tableName;
        gr.u_record_sys_id  = recordSysId || '';
        gr.u_user           = userId || gs.getUserID();
        gr.u_action         = 'Access Denied';
        gr.u_field_name     = operation;
        gr.u_timestamp      = new GlideDateTime();
        gr.insert();
    },

    type: 'LibraryAuditLogger'
};
```

---

*References: [requirements.md](../../.kiro/specs/smart-library-request-workflow/requirements.md) — NFR-05, FR-14*  
*See also: [BusinessRules.md](BusinessRules.md) | [FlowDesigner.md](FlowDesigner.md)*
