# Developer Guide

# Smart Library Request Workflow — ServiceNow Enterprise Solution

> **Document Type:** Developer Guide  
> **Version:** 2.0.0  
> **Application Scope:** `x_univ_library`  
> **Audience:** Developers  
> **Platform:** ServiceNow Washington DC

---

## 1. Development Environment Setup

### 1.1 Prerequisites

| Requirement | Details |
| ------------- | --------- |
| **ServiceNow instance** | Washington DC (or later) development instance |
| **Roles** | `admin`, `flow_designer_admin`, `sp_admin`, `pa_admin`, `sla_admin` |
| **Studio** | Built-in — no installation required |
| **Version control** | Git repository for source code tracking |
| **Local tools** | Text editor (VS Code recommended), REST API client (Postman) |

### 1.2 Accessing Studio

1. Navigate to `https://[dev-instance].service-now.com/nav_to.do?uri=%2Fstudio_dashboard.do`
2. Alternatively, from the main UI: **All** → **System Applications** → **Studio**.
3. Open the `x_univ_library` application.

### 1.3 Source Code Management

All server-side JavaScript should be tracked in the project Git repository:

```text
E:\SmartBridge-ServiceNow-Project\
├── docs\                              # Documentation
├── src\                               # Source code (for reference)
│   ├── script-includes\               # Script Include implementations
│   ├── business-rules\                # Business Rule scripts
│   ├── client-scripts\                # Client Script implementations
│   └── flows\                         # Flow Designer JSON definitions
├── sample-data\                       # CSV import files
└── assets\                            # Images, CSS, etc.
```

---

## 2. Scope Conventions and Naming Standards

### 2.1 Scope Prefix

All application artifacts use the `x_univ_library` scope. The scope prefix ensures uniqueness and prevents naming conflicts with other applications.

### 2.2 Naming Conventions

| Artifact | Convention | Example |
| ---------- | ----------- | --------- |
| **Tables** | `u_library_[plural_noun]` | `u_library_books`, `u_library_students` |
| **Fields** | `u_[descriptive_name]` | `u_title`, `u_available_copies` |
| **Business Rules** | `[LIB] [Action Description]` | `[LIB] Availability Auto-Update` |
| **Script Includes** | `PascalCase` | `LibraryBorrowService` |
| **Client Scripts** | `[LIB] [Action Description]` | `[LIB] Validate Pickup Date` |
| **UI Policies** | `[LIB] [Action Description]` | `[LIB] Rejection Reason Mandatory` |
| **UI Actions** | `[LIB] [Action Description]` | `[LIB] Issue Book` |
| **Flow Designer flows** | `[LIB] [Flow Description]` | `[LIB] Borrow Request Lifecycle` |
| **Notifications** | `[LIB] [Event Description]` | `[LIB] Borrow Request Submitted` |
| **Scheduled Jobs** | `[LIB] [Job Description]` | `[LIB] Overdue Detection` |
| **SLA Definitions** | `[LIB] [SLA Description]` | `[LIB] Approval SLA — 48 Hours` |
| **Roles** | `x_univ_library.[role_name]` | `x_univ_library.student_library` |
| **Portal pages** | Lowercase kebab-case URL | `/library/catalog`, `/library/my-requests` |
| **Widgets** | `library-[widget-name]` | `library-catalog-grid` |

### 2.3 JavaScript Coding Standards

| Standard | Guideline |
| ---------- | ----------- |
| **Variables** | `camelCase` for local variables |
| **Constants** | `UPPER_SNAKE_CASE` for constants |
| **Functions** | `camelCase` for method names |
| **Classes** | `PascalCase` for Script Includes |
| **Indentation** | 4 spaces (no tabs) |
| **Semicolons** | Always use semicolons |
| **Comments** | JSDoc-style for public methods |
| **Logging** | Use `gs.log('[LIB] message', 'ScriptName')` |
| **Error handling** | Use `try/catch` for external calls |

### 2.4 Logging Standard

All scripts must log with a consistent format:

```javascript
gs.log('[LIB] Description of log message', 'SourceScriptName');
```

Log levels:

```javascript
// Debug (development only — remove before production)
gs.log('[LIB] [DEBUG] Variable X = ' + value, 'LibraryBorrowService');

// Info (normal operations)
gs.log('[LIB] Borrow request created: ' + sysId, 'LibraryBorrowService');

// Warning (non-critical issue)
gs.warn('[LIB] Student ' + studentId + ' exceeded borrow limit', 'LibraryValidationService');

// Error (critical issue)
gs.error('[LIB] Failed to create borrow request: ' + errorMsg, 'LibraryBorrowService');
```

---

## 3. How to Add a New Table

### 3.1 Create the Table

1. Open **Studio** → **Data** → **Tables** → **New**.
2. Set **Name** to `u_library_[plural_noun]`.
3. Set **Label** to a human-readable name.
4. Click **Create**.

### 3.2 Add Fields

1. In the table designer, click **New Field**.
2. Configure:
   - **Type:** String, Integer, Date, Reference, Choice, True/False, Multi-line Text, etc.
   - **Column Label:** Human-readable field name
   - **Column Name:** Automatically generated from label; adjust if needed (prefix with `u_`)
   - **Max Length:** For string fields
   - **Mandatory:** Check if required
   - **Default Value:** If applicable

3. For **Reference** fields:
   - Set **Reference** to the target table.
   - Set **Reference Qualifier** if needed (e.g., `u_active=true`).

4. For **Choice** fields:
   - Add choice values in the **Choice** tab.

### 3.3 Configure Dictionary Overrides

1. Navigate to the field's dictionary entry.
2. Set:
   - **Label:** Display label on forms
   - **Mandatory:** If required
   - **Default Value:** If applicable
   - **Reference Qualifier:** For reference fields

### 3.4 Create Indexes

| Index Type | When to Use |
| ------------ | ------------- |
| **Unique index** | Fields that must be unique (ISBN, University ID, Staff ID, Request Number) |
| **Regular index** | Frequently queried fields (status, date, student reference) |
| **Composite index** | Multi-field query patterns (student + status + date) |

### 3.5 Configure ACLs

After creating the table, create ACL entries for each role. See the Roles and Permissions Design document for the complete permission matrix.

### 3.6 Deploy the Table

The table and all its configuration are automatically captured in the application update set when created in Studio.

---

## 4. How to Create a New Business Rule

### 4.1 Create in Studio

1. Open **Studio** → **Server** → **Business Rule** → **New**.
2. Configure:

| Property | Value |
| ---------- | ------- |
| **Name** | `[LIB] Descriptive Name` |
| **Table** | Target table |
| **When** | Before/After Insert/Update/Delete/Query |
| **Condition** | Trigger condition (use script or condition builder) |
| **Order** | Execution order (100 = default, lower = earlier) |

### 4.2 Business Rule Pattern

All Business Rules must delegate business logic to Script Includes. The Business Rule itself should only contain:

```javascript
(function executeRule(current, previous) {
    // Delegate to Script Include
    var service = new LibraryBorrowService();
    var result = service.someMethod(current.getValue('field'), param2);

    if (!result.success) {
        gs.addErrorMessage(result.message);
        current.setAbortAction(true);
    }
})(current, previous);
```

### 4.3 Testing

1. Create a test record that matches the trigger condition.
2. Verify the Business Rule executes by checking the record state.
3. Check **System Log** for `[LIB]` messages.
4. Run the ATF test suite to verify no regressions.

---

## 5. How to Modify a Script Include

### 5.1 Open the Script Include

1. Navigate to **System Definition** → **Script Includes**.
2. Search for the Script Include by name (e.g., `LibraryBorrowService`).
3. Open the record.

### 5.2 Modification Guidelines

| Guideline | Details |
| ----------- | --------- |
| **Maintain backward compatibility** | Do not change method signatures without updating all callers |
| **Log changes** | Add log entries for significant operations |
| **Preserve `callerAccess`** | Keep `callerAccess = 'caller_track'` for application access |
| **Keep methods focused** | Each method should do one thing |
| **Add JSDoc** | Document new methods with parameter and return descriptions |

### 5.3 Testing Changes

1. Test the Script Include in **System Definition** → **Scripts Background**.
2. Test all Business Rules and Flows that use the modified methods.
3. Run the ATF regression test suite.

---

## 6. How to Modify a Flow Designer Flow

### 6.1 Open the Flow

1. Navigate to **Process Automation** → **Flow Designer**.
2. Open the flow (e.g., `[LIB] Borrow Request Lifecycle`).

### 6.2 Modification Guidelines

1. **Create a new version** before making changes:
   - Click the flow's context menu → **Create new version**.
   - This preserves the previous version for rollback.

2. **Test in a sub-production instance:**
   - Export the flow as part of an update set.
   - Import and test in the TEST instance.

3. **Use Flow Variables:**
   - Define flow-level variables for reusable data.
   - Reference variables using the `{{ variables.variable_name }}` syntax.

4. **Error Handling:**
   - Add error branches to all actions.
   - Log errors using the **Log** action.

### 6.3 Adding a New Action

1. Click the **+** icon between flow steps.
2. Select the action type:
   - **Send Notification** — Send a platform notification
   - **Create Record** — Create a record in any table
   - **Update Record** — Update an existing record
   - **Look Up Record** — Query for a record
   - **Run Script** — Execute a script (use cautiously)
   - **Call Sub-Flow** — Invoke a sub-flow
3. Configure the action parameters.
4. Connect the action to the flow.

### 6.4 Publishing the Flow

1. After modifications, click **Publish**.
2. The new version becomes active immediately.
3. Verify the flow triggers as expected with test data.

---

## 7. How to Add a New Notification

### 7.1 Create the Notification Record

1. Navigate to **System Notification** → **Email** → **Notifications** → **New**.
2. Configure:

| Property | Value |
| ---------- | ------- |
| **Name** | `[LIB] Event Description` |
| **Table** | Source table |
| **Active** | Checked |
| **When to send** | After Insert / After Update / etc. |
| **Who will receive** | Recipient configuration (user, group, or script) |
| **Email template** | Select or create a template |

### 7.2 Create the Email Template

1. In the notification record, click the **Email Template** field's lookup icon.
2. Click **New** or select an existing template.
3. Design the template:

| Property | Value |
|----------|-------|
| **Name** | `[LIB] Event Description — Email Template` |
| **Subject** | Template with record fields: `${number}` `${u_book.u_title}` |
| **Body** | HTML with inline CSS. Use record fields in `${field_path}` syntax |

### 7.3 Testing the Notification

1. Navigate to the notification record.
2. Click **Test**.
3. Enter a test record sys_id.
4. Click **Send Test**.
5. Verify the email is delivered with correct content.

---

## 8. How to Create a New Portal Page

### 8.1 Create the Page

1. Navigate to **Service Portal** → **Pages** → **New**.
2. Configure:

| Property | Value |
| ---------- | ------- |
| **ID** | Lowercase kebab-case (e.g., `my-requests`) |
| **Title** | Display page title |
| **Portal** | `x_univ_library_portal` |
| **Active** | Checked |

### 8.2 Design the Page

1. Open the page in the Service Portal Designer.
2. Drag widgets onto the page canvas.
3. Configure each widget instance:
   - Widget
   - Options (JSON configuration)
   - CSS class
   - Position and size

### 8.3 Configure Page Access

1. Open the page → **Access** tab.
2. Set **Requires role** to the appropriate library role.
3. Add additional roles as needed.

### 8.4 Testing the Page

1. Navigate to the page URL: `/library/your-page-id`.
2. Verify the page renders correctly.
3. Test with different user roles to verify access controls.

---

## 9. How to Deploy Changes via Update Set

### 9.1 Capture Changes

1. Navigate to **System Update Sets** → **Update Sets**.
2. Create a new update set or use the existing application update set.
3. All changes made within the application scope are automatically captured.

### 9.2 Verify Captured Changes

1. Open the update set.
2. Click **Update Set Report**.
3. Review the list of captured changes.
4. Verify no unintended changes are included.

### 9.3 Export the Update Set

1. Open the update set.
2. Click **Export to XML**.
3. Choose **All application records**.
4. Save the XML file.

### 9.4 Import to Target Instance

1. Log in to the target instance.
2. Navigate to **System Update Sets** → **Update Sets** → **Import Update Set from XML**.
3. Upload the XML file.
4. Preview the update set.
5. Commit the update set.

---

## 10. Testing Standards

### 10.1 Unit Testing

| Standard | Details |
| ---------- | --------- |
| **Scope** | Individual Script Include methods |
| **Tool** | Scripts Background (`/scripts_background.do`) |
| **Coverage** | All public methods, including edge cases |
| **Frequency** | After every change to a Script Include |

### 10.2 Integration Testing

| Standard | Details |
| ---------- | --------- |
| **Scope** | End-to-end workflows (request → approval → issue → return) |
| **Tool** | Automated Test Framework (ATF) |
| **Coverage** | All 15 Business Rules, all 3 Flow Designer flows |
| **Frequency** | Before every deployment |

### 10.3 ATF Test Creation

1. Navigate to **Automated Test Framework** → **Tests** → **New**.
2. Select test step type:
   - **Create Record** — Set up test data
   - **Assert Record** — Verify record state
   - **Run Server Script** — Execute custom validation
   - **Login As** — Test as different roles
   - **Open Portal Page** — Test portal rendering
3. Chain test steps to create end-to-end scenarios.
4. Group related tests into a **Test Suite**.
5. Execute the test suite and verify all tests pass.

### 10.4 Regression Testing

| Standard | Details |
| ---------- | --------- |
| **Suite** | `[LIB] Smart Library Workflow — Full Regression` |
| **Tests** | 64 automated test cases |
| **Execution** | Before every production deployment |
| **Frequency** | Weekly or per-release |

---

## 11. Common Development Patterns

### 11.1 Script Include Pattern

```javascript
var LibraryExampleService = Class.create();
LibraryExampleService.prototype = {
    initialize: function() {
        // Constructor — initialize properties
    },

    /**
     * Description of what this method does.
     * @param {string} paramName - Description of parameter
     * @returns {Object} { success: boolean, message: string }
     */
    exampleMethod: function(paramName) {
        var result = { success: false, message: '' };
        try {
            // Business logic here
            result.success = true;
            result.message = 'Operation completed';
        } catch (e) {
            gs.error('[LIB] exampleMethod failed: ' + e.message, 'LibraryExampleService');
            result.message = e.message;
        }
        return result;
    },

    type: 'LibraryExampleService'
};
```

### 11.2 Business Rule Pattern

```javascript
(function executeRule(current, previous) {
    var service = new LibraryExampleService();
    var result = service.exampleMethod(current.getValue('u_field'));

    if (!result.success) {
        gs.addErrorMessage(result.message);
        current.setAbortAction(true);
    }
})(current, previous);
```

### 11.3 Client Script Pattern

```javascript
function onChange(control, oldValue, newValue, isLoading) {
    if (isLoading || !newValue) return;

    // Validation logic
    if (newValue === oldValue) return;

    // Display field message
    g_form.showFieldMsg('u_field_name', 'Message text', 'info');
    // Clear field message
    g_form.hideFieldMsg('u_field_name', true);
}
```

### 11.4 GlideRecord Query Pattern

```javascript
var gr = new GlideRecord('u_library_books');
gr.addActiveQuery();                 // u_active = true
gr.addQuery('u_available_copies', '>', 0);
gr.addQuery('u_category', categorySysId);
gr.orderByDesc('u_title');
gr.setLimit(10);
gr.query();

while (gr.next()) {
    var title = gr.getValue('u_title');
    var author = gr.getDisplayValue('u_author');
    // Process record
}
```

---

*References: [UserManual.md](UserManual.md) | [AdministratorGuide.md](AdministratorGuide.md)*
*See also: [ImplementationGuide.md](../implementation/ImplementationGuide.md) | [TestingGuide.md](../TestingGuide.md)*
