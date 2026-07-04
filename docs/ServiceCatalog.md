# Service Catalog — Smart Library Request Workflow

**Scope:** `x_univ_library`
**Platform:** ServiceNow Washington DC
**Version:** 1.0.0

---

## 1. Catalog Category

**Name:** Library Services
**Scope:** `x_univ_library`
**Category URL:** `sp_cat_sc_cat_item_list?id=library_services`

The Library Services category is created under the standard Service Catalog and contains the "Borrow a Book" catalog item. The category is visible to all users with the `x_univ_library.student` role and is accessible from the Service Portal's Library Services section.

---

## 2. Catalog Item: "Borrow a Book"

**Name:** Borrow a Book
**Catalog:** Library Services
**Scope:** `x_univ_library`
**Item Number:** `LIB-CAT-001`
**Active:** true
**Portal Category:** Library Services
**Short Description:** Submit a request to borrow a book from the university library.
**Description (Rich Text):** Use this form to request a book from the library catalog. Your request will be reviewed by library staff. You will be notified when your request is approved and your book is ready for pickup.

### 2.1 Variable Definitions

| Order | Variable Name | Type | Length | Mandatory | Reference / Options | Description |
| --- | --- | --- | --- | --- | --- | --- |
| 10 | `book` | Reference | — | Yes | `u_library_books` | Select the book you wish to borrow. Shows only books with `u_available_copies > 0` and `u_active = true`. |
| 20 | `requested_pickup_date` | Date | — | Yes | — | Preferred date to pick up the book from the library. Must be at least 24 hours from now. |
| 30 | `notes` | Multi-line Text | 1000 | No | — | Any additional information or special requests. |

### 2.2 Variable Details

#### `book` (Reference — u_library_books)

| Property | Value |
| --- | --- |
| **Type** | Reference |
| **Reference table** | `u_library_books` |
| **Reference qual** | `u_available_copies > 0^u_active=true^u_status=Available` |
| **Display field** | `u_title` (with `u_author` in secondary display) |
| **Sort by** | `u_title` (ascending) |
| **Mandatory** | true |
| **Default value** | (none — user must select) |

**Reference Qualifier Script:**

```javascript
(function() {
    var qual = '';
    qual += 'u_available_copies > 0';
    qual += '^u_active = true';
    qual += '^u_statusINAvailable,Issued';
    // Exclude books currently borrowed by this student
    var gr = new GlideRecord('u_library_issuance_records');
    gr.addQuery('u_student.sys_user', gs.getUserID());
    gr.addQuery('u_status', 'Issued');
    gr.query();
    var issuedBooks = [];
    while (gr.next()) {
        issuedBooks.push(gr.getValue('u_book'));
    }
    if (issuedBooks.length > 0) {
        qual += '^sys_idNOT IN' + issuedBooks.join(',');
    }
    return qual;
})();
```

#### `requested_pickup_date` (Date)

| Property | Value |
| --- | --- |
| **Type** | Date |
| **Mandatory** | true |
| **Default value** | (none) |
| **Min date** | `javascript: new Date().getTime() + (24 * 60 * 60 * 1000)` (tomorrow) |
| **Max date** | `javascript: new Date().getTime() + (30 * 24 * 60 * 60 * 1000)` (30 days from now) |

#### `notes` (Multi-line Text)

| Property | Value |
| --- | --- |
| **Type** | Multi-line Text |
| **Max length** | 1000 characters |
| **Mandatory** | false |
| **Default value** | (empty) |
| **Rows** | 4 |

---

## 3. Client Scripts

### 3.1 `CS-Borrow Request Form Validation`

| Property | Value |
| --- | --- |
| **Name** | CS-Borrow Request Form Validation |
| **Type** | onSubmit |
| **Table** | `sc_req_item` |
| **Cat Item** | Borrow a Book |
| **Scope** | `x_univ_library` |
| **Order** | 100 |

**Script:**

```javascript
function onSubmit() {
    var book = g_form.getValue('book');
    var pickupDate = g_form.getValue('requested_pickup_date');
    var notes = g_form.getValue('notes');

    if (book === '' || book === undefined) {
        g_form.showFieldMsg('book', 'Please select a book to borrow.', 'error');
        return false;
    }

    if (pickupDate === '') {
        g_form.showFieldMsg('requested_pickup_date', 'Please select a pickup date.', 'error');
        return false;
    }

    var selectedDate = new Date(pickupDate);
    var today = new Date();
    today.setHours(0, 0, 0, 0);
    var minDate = new Date(today.getTime() + (24 * 60 * 60 * 1000));

    if (selectedDate < minDate) {
        g_form.showFieldMsg('requested_pickup_date', 'Pickup date must be at least 24 hours from now.', 'error');
        return false;
    }

    var maxDate = new Date(today.getTime() + (30 * 24 * 60 * 60 * 1000));
    if (selectedDate > maxDate) {
        g_form.showFieldMsg('requested_pickup_date', 'Pickup date cannot be more than 30 days from now.', 'error');
        return false;
    }

    if (notes.length > 1000) {
        g_form.showFieldMsg('notes', 'Notes cannot exceed 1000 characters.', 'error');
        return false;
    }

    return true;
}
```

### 3.2 `CS-Book Reference Display`

| Property | Value |
| --- | --- |
| **Name** | CS-Book Reference Display |
| **Type** | onLoad |
| **Table** | `sc_req_item` |
| **Cat Item** | Borrow a Book |
| **Scope** | `x_univ_library` |
| **Order** | 100 |

**Script:**

```javascript
function onLoad() {
    // When book is selected, show availability info
    function onBookChange() {
        var book = g_form.getValue('book');
        if (book) {
            var ga = new GlideAjax('LibraryCatalogClientHelper');
            ga.addParam('sysparm_name', 'getBookAvailability');
            ga.addParam('sysparm_book_sys_id', book);
            ga.getXMLAnswer(function(answer) {
                var info = JSON.parse(answer);
                if (info.available_copies > 0) {
                    g_form.showFieldMsg('book', info.available_copies + ' copy/copies available.', 'info');
                } else {
                    g_form.showFieldMsg('book', 'This book is currently unavailable.', 'warning');
                }
            });
        }
    }

    g_form.watch('book', onBookChange);
}
```

---

## 4. UI Policies

### 4.1 `UI-Borrow Request Notes Optional`

| Property | Value |
| --- | --- |
| **Name** | UI-Borrow Request Notes Optional |
| **Table** | `sc_req_item` |
| **Cat Item** | Borrow a Book |
| **Type** | onLoad |
| **Scope** | `x_univ_library` |

**Conditions:** Always true

**Actions:** Make `notes` — visible: true, mandatory: false

---

## 5. Catalog Item Variables

### 5.1 Variable Map to Record Fields

When the catalog item is submitted, the following variable-to-field mapping is applied by the Flow Designer flow to populate the `u_library_borrow_requests` record:

| Catalog Variable | Target Field | Mapping Type | Notes |
| --- | --- | --- | --- |
| `book` | `u_book` | Direct (sys_id) | Reference to `u_library_books` |
| `requested_pickup_date` | `u_requested_pickup_date` | Direct (date) | — |
| `notes` | `u_purpose` | Direct (string) | Mapped to purpose field |

The `u_student` field is populated from the current user's linked `u_library_students` record. `u_request_type` is set to `Borrow`. `u_submission_date` is set to the current date/time.

---

## 6. Record Producer: "Quick Request from Book Detail"

**Name:** Quick Request from Book Detail
**Scope:** `x_univ_library`
**Table:** `u_library_borrow_requests`
**Active:** true

### 6.1 Purpose

The Record Producer enables students to submit a borrow request directly from a book's detail page on the Service Portal, eliminating the need to navigate to the catalog form separately.

### 6.2 Trigger Configuration

| Property | Value |
| --- | --- |
| **Table** | `u_library_books` |
| **Trigger** | "Request this Book" button on book detail widget |
| **URL** | `sp_record_producer?id=quick_borrow_request&sysparm_book_id={sys_id}` |
| **Role** | `x_univ_library.student` |

### 6.3 Variable Mapping

| Variable | Source | Target Field |
| --- | --- | --- |
| `book` | URL parameter `sysparm_book_id` | `u_book` |
| `student` | Current user's linked `u_library_students` record | `u_student` |
| `request_type` | Hard-coded "Borrow" | `u_request_type` |
| `status` | Hard-coded "Draft" | `u_status` |

### 6.4 Record Producer Script

```javascript
(function() {
    current.u_book = producer.book;
    current.u_student = getStudentRecord();  // Script Include lookup
    current.u_request_type = 'Borrow';
    current.u_status = 'Draft';
    current.u_submission_date = new GlideDateTime();
    current.u_borrow_duration_days = 14;

    // Set the request number (auto-generated by number field)
    current.setValue('number', 'BR-' + gs.generateNumber('BR'));

    // Validate student eligibility
    var validator = new LibraryValidator();
    if (!validator.isStudentEligible(current.u_student)) {
        gs.addErrorMessage('Your account is not eligible to borrow books.');
        current.setAbortAction(true);
        return;
    }

    current.insert();
})();
```

### 6.5 Portal Integration

The Record Producer is surfaced through the **Book Detail** Service Portal widget. A "Request this Book" button appears on the book detail page when the student is eligible to borrow. The button:

- Is hidden if the student has 0 available borrow slots.
- Is hidden if the student has outstanding fines above the threshold.
- Passes the book sys_id as a URL parameter to the producer form.
- Redirects the student to the "My Requests" page after submission.

---

## 7. Flow Designer Integration

### 7.1 Flow: `LIB-Catalog Item Submission Flow`

| Property | Value |
| --- | --- |
| **Name** | LIB-Catalog Item Submission Flow |
| **Scope** | `x_univ_library` |
| **Trigger** | Catalog Item — "Borrow a Book" |
| **Run As** | System User |

**Flow Steps:**

1. **Trigger: Catalog Item Submitted**
   - Source: Borrow a Book catalog item

2. **Action: Look Up Student Record**
   - Table: `u_library_students`
   - Condition: `u_library_students.sys_user == Requested by.user_sys_id`

3. **Condition: Check Student Eligibility**
   - If `student.u_eligibility_status == Eligible` AND `student.u_borrow_count < student.u_max_borrow` AND `student.u_total_fines < borrowing_restriction_threshold`:
     - Proceed to Step 4
   - Otherwise:
     - Send error notification to student
     - Reject the request item

4. **Action: Create Borrow Request Record**
   - Table: `u_library_borrow_requests`
   - Field mappings from catalog variables:
     - `u_book` → `{{variables.book}}`
     - `u_student` → Student record
     - `u_requested_pickup_date` → `{{variables.requested_pickup_date}}`
     - `u_purpose` → `{{variables.notes}}`
     - `u_request_type` → `"Borrow"`
     - `u_status` → `"Pending"`
     - `u_submission_date` → Current date/time
     - `u_borrow_duration_days` → 14
     - `u_sla_deadline` → Now + 48 hours
     - `u_number` → Auto-generated

5. **Action: Trigger Approval Workflow**
   - Invoke subflow: `LIB-Approval Workflow`
   - Pass borrow request sys_id

6. **Action: Send Notification — LIB-NOTIF-001**
   - Recipient: Student (email + in-platform)
   - Template: Borrow Request Submitted

7. **Action: Update Request Item State**
   - Set request item state to "Fulfillment in Progress"

### 7.2 Subflow: `LIB-Approval Workflow`

Refer to [FlowDesigner.md](servicenow/FlowDesigner.md) for the complete approval workflow definition.

---

## 8. Variable Validation Rules

All validation is performed server-side by the `LibraryValidator` Script Include after catalog item submission:

| Rule | Validation | Error Message |
| --- | --- | --- |
| V-01 | Book exists and is active | "The selected book is no longer available." |
| V-02 | Book has available copies | "No available copies of this book remain." |
| V-03 | Student is eligible to borrow | "Your library account is not eligible to borrow." |
| V-04 | Student within borrow limit | "You have reached your maximum borrowing limit." |
| V-05 | Student fines below threshold | "Your outstanding fine balance exceeds the allowed limit." |
| V-06 | Pickup date is valid (not in past) | "Pickup date must be in the future." |
| V-07 | Pickup date within 30 days | "Pickup date cannot be more than 30 days from today." |
| V-08 | Notes length ≤ 1000 | "Notes must be 1000 characters or fewer." |
| V-09 | No duplicate active request | "You already have an active request for this book." |

---

## 9. Catalog Administration

| Property | Configuration |
| --- | --- |
| **Catalog** | University Service Catalog |
| **Category** | Library Services |
| **Entitlement** | None (all students can access) |
| **Request flow** | Default (Flow Designer) |
| **Auto-calculate** | false (no cost) |
| **Quantity** | 1 (single book per request) |
| **Fulfillment** | 2 business days SLA |
| **Stage** | Fulfillment |

---

*References: [FunctionalRequirements.md](FunctionalRequirements.md) — FR-05, FR-15, [FlowDesigner.md](servicenow/FlowDesigner.md), [ClientScripts.md](servicenow/ClientScripts.md)*
