# Troubleshooting Guide

# Smart Library Request Workflow — ServiceNow Enterprise Solution

> **Document Type:** Troubleshooting Guide  
> **Version:** 2.0.0  
> **Application Scope:** `x_univ_library`  
> **Status:** Final — Complete  
> **Platform:** ServiceNow Washington DC

---

## 1. Update Set Import Fails

### Symptom

The update set import from XML fails with an error message such as "Invalid record" or "Dependency missing."

### Possible Causes and Resolutions

**Cause 1: Missing Plugin Dependencies**

The target instance does not have required plugins activated.

- **Check:** Navigate to **System Diagnostics** → **Plugins** and verify the following are active:
  - Service Portal (`com.glideapp.servicecatalog`)
  - Flow Designer (`com.snc.service_flow_designer`)
  - Performance Analytics (`com.snc.pa.performance_analytics`)
  - SLA Management (`com.snc.sla.plugin`)
- **Resolution:** Activate missing plugins before importing the update set. Plugin activation may require a system restart.

**Cause 2: Conflicting Update Sets**

A previously imported update set contains records with conflicting names.

- **Check:** Navigate to **System Update Sets** → **Update Set Conflicts** and review conflict details.
- **Resolution:** During preview, select **Use Remote** (use incoming version) or **Skip** as appropriate. For critical conflicts, manually resolve by deleting or renaming the conflicting record.

**Cause 3: Scope Mismatch**

The target instance does not have the `x_univ_library` application scope.

- **Check:** Navigate to **System Applications** → **Applications** and search for `x_univ_library`.
- **Resolution:** Create the scoped application manually in Studio first, then import the update set.

**Cause 4: XML File Corruption**

The exported XML file is corrupted or truncated.

- **Check:** Verify the XML file size. The complete update set should be approximately 2.5 MB. Open the XML file and verify it ends with `</update_set>`.
- **Resolution:** Re-export the update set from the development instance. Try downloading the file again. If the issue persists, split the update set into multiple smaller update sets.

---

## 2. ACL Not Working as Expected

### Symptom

Users can access records they should not be able to, or cannot access records they should be able to.

### Possible Causes and Resolutions

**Cause 1: ACL Order/Cache**

ACLs are evaluated in order; a higher-priority ACL may override the intended rule.

- **Check:** Navigate to **System Security** → **Access Controls (ACLs)**. Filter by table. Review the **Order** field for each ACL.
- **Resolution:** Ensure the most restrictive ACLs have a lower order number. Clear ACL cache via **System Security** → **Access Controls (ACLs)** → **Clear Cache**.

**Cause 2: Script Error in ACL Condition**

The ACL script contains a syntax error or throws an exception.

- **Check:** Open the ACL record and review the **Condition** or **Script** field. Look for JavaScript errors.
- **Resolution:** Fix the script error. Test the ACL script in the background script editor before saving. Common issues include:
  - Using `current` when it is `null` (for non-record ACLs)
  - Referencing undefined functions or variables
  - Incorrect role name (must include scope prefix)

**Cause 3: Role Not Assigned to User**

The user does not have the required role.

- **Check:** Open the user record → **Roles** tab. Verify the user has the expected role (e.g., `x_univ_library.student_library`).
- **Resolution:** Assign the correct role to the user via **User Administration** → **Users** → select user → **Roles** → **Edit**.

**Cause 4: ACL Cache Not Cleared**

After modifying ACLs, the cache still holds the old rules.

- **Check:** ACL changes should take effect within a few minutes, but the cache may persist longer.
- **Resolution:** Navigate to **System Security** → **Access Controls (ACLs)** → **Clear Cache**. Alternatively, restart the instance.

---

## 3. Flow Designer Flow Not Triggering

### Symptom

A Flow Designer flow does not execute when the trigger condition is met.

### Possible Causes and Resolutions

**Cause 1: Flow Not Active**

The flow is in draft or inactive state.

- **Check:** Open the flow in Flow Designer. Verify the **Active** toggle is enabled.
- **Resolution:** Turn on the **Active** toggle and publish the flow.

**Cause 2: Trigger Condition Not Met**

The record does not satisfy the flow's trigger condition.

- **Check:** Open the flow → **Trigger** tab. Review the condition. Compare with a sample record that should trigger the flow.
- **Resolution:** Adjust the trigger condition. For example, if the trigger expects `u_status == 'pending_approval'` but the record has `u_status == 'Pending Approval'`, the condition will not match due to choice value versus label mismatch.

**Cause 3: Flow Execution Order**

A Business Rule or other automation modifies the record after the flow trigger fires, causing a race condition.

- **Check:** Review the order of Business Rules on the same table. If a Business Rule modifies the record after the flow trigger, the flow may read stale data.
- **Resolution:** Adjust the Business Rule order so that all modifications happen before the flow trigger fires. Alternatively, use a **Before Query** Business Rule to ensure data consistency.

**Cause 4: Scope Restriction**

The flow cannot access a record or script from a different scope.

- **Check:** Verify that the flow's actions reference records within the `x_univ_library` scope. Cross-scope access restrictions may prevent the flow from reading or writing to global tables.
- **Resolution:** Enable the appropriate scope access in the application record: **System Applications** → **Applications** → `x_univ_library` → **Scope Rules**.

---

## 4. Notifications Not Sending

### Symptom

Users do not receive notifications (email or in-platform) when expected events occur.

### Possible Causes and Resolutions

**Cause 1: Notification Configuration Disabled**

The master notification toggle is turned off.

- **Check:** Navigate to **u_library_configuration** table. Verify the `notifications_enabled` parameter is set to `true`.
- **Resolution:** Set `notifications_enabled` to `true`. If email is also not sending, verify `email_notifications_enabled` is `true`.

**Cause 2: Notification Record Not Active**

The notification record itself is inactive.

- **Check:** Navigate to **System Notification** → **Email** → **Notifications**. Open the notification record and verify the **Active** checkbox is checked.
- **Resolution:** Check the **Active** checkbox and save the record.

**Cause 3: Email Channel Not Configured**

The instance email channel is not properly configured.

- **Check:** Navigate to **System Mail** → **Email** → **Mailboxes**. Verify that an outbound mailbox exists and is active.
- **Resolution:** Configure an outbound email mailbox. Contact the instance administrator if the instance is not configured for email delivery.

**Cause 4: Recipient Not Resolved**

The notification's recipient field does not resolve to a valid user.

- **Check:** Open the notification record → **Who will receive** tab. Verify the recipient configuration (e.g., `opened_by` user, group members). Validate that the recipient has a valid email address in their user record.
- **Resolution:** Update the recipient configuration in the notification record. Ensure all expected recipients have email addresses in their `sys_user` record.

**Cause 5: Flow Designer Notification Action**

Notifications sent via Flow Designer not working because the notification action is misconfigured.

- **Check:** Open the flow in Flow Designer. Locate the **Send Notification** action. Verify the notification name matches an existing notification record.
- **Resolution:** Re-select the notification from the picker. Ensure the notification record is active and in the same scope.

---

## 5. Portal Pages Not Loading

### Symptom

Service Portal pages return a blank page, loading spinner, or HTTP 404 error.

### Possible Causes and Resolutions

**Cause 1: Portal Record Not Active**

The portal record is inactive.

- **Check:** Navigate to **Service Portal** → **Portals**. Open the `x_univ_library_portal` record and verify **Active** is checked.
- **Resolution:** Check the **Active** checkbox and save.

**Cause 2: Page Record Not Active**

The specific portal page is inactive.

- **Check:** Navigate to **Service Portal** → **Pages**. Filter by portal `x_univ_library_portal`. Verify each page has **Active** checked.
- **Resolution:** Check the **Active** checkbox on the non-loading page.

**Cause 3: Widget Instance Error**

A widget on the page has a server-side script error.

- **Check:** Open the page in the portal designer. For each widget instance, check the **Logs** for errors.
- **Resolution:** Open the widget record and fix the server-side script. Common issues include:
  - Invalid GlideRecord queries
  - Referencing fields that do not exist
  - Missing ACL permissions for the current user
  - Cross-scope access violations

**Cause 4: Theme or CSS Issue**

The portal theme or custom CSS contains errors that prevent rendering.

- **Check:** Open the browser developer console (F12) and check for CSS or JavaScript errors.
- **Resolution:** Temporarily switch to the default SNow Seismic theme. If the page loads, the issue is in the custom theme. Review the custom CSS/SCSS for syntax errors.

**Cause 5: ACL on Portal Page**

The current user does not have permission to access the portal page.

- **Check:** Navigate to **Service Portal** → **Pages** → open the page → **Access** tab. Verify the page access configuration.
- **Resolution:** Add the correct role to the page access list. Set **Requires role** to the appropriate library role.

---

## 6. SLA Not Starting

### Symptom

An SLA timer does not start when the trigger condition is met.

### Possible Causes and Resolutions

**Cause 1: SLA Definition Not Active**

The SLA definition is inactive.

- **Check:** Navigate to **SLA** → **SLA Definitions**. Open the SLA definition and verify **Active** is checked.
- **Resolution:** Check the **Active** checkbox and save.

**Cause 2: SLA Condition Not Met**

The record does not satisfy the SLA definition's start condition.

- **Check:** Open the SLA definition → **Conditions** tab. Review the start condition. Compare with a sample record.
- **Resolution:** Adjust the SLA condition to match the expected trigger. Note that SLA conditions use internal values (e.g., `pending_approval`), not display labels.

**Cause 3: SLA Timer Table Configuration**

The SLA is configured on a table that does not have SLA tracking enabled.

- **Check:** Navigate to **SLA** → **SLA Definitions** → open definition → **Table**. Verify SLA is enabled on the table.
- **Resolution:** Navigate to **System Definition** → **Tables** → open the table → **Advanced** → enable **SLA tracking**.

**Cause 4: Business Hours Schedule**

The SLA duration calculation depends on a business hours schedule that is not defined.

- **Check:** Open the SLA definition → **Schedule** tab. If a schedule is selected, navigate to it and verify it is active.
- **Resolution:** Create or activate the business hours schedule, or change the SLA to use **24/7** (no schedule).

---

## 7. Performance Analytics Data Not Appearing

### Symptom

PA indicators show zero values or no data, even though records exist in the source table.

### Possible Causes and Resolutions

**Cause 1: PA Collector Not Running**

The PA data collection job is not running or has not run yet.

- **Check:** Navigate to **Performance Analytics** → **Collectors** → **Jobs**. Verify the collector job for library indicators is active.
- **Resolution:** Run the collector job manually by clicking **Run Now**. Verify it completes without errors.

**Cause 2: Indicator Configuration Error**

The indicator definition has an incorrect filter or aggregate.

- **Check:** Open the indicator in **Performance Analytics** → **Indicators**. Verify the **Conditions** and **Aggregation** fields.
- **Resolution:** Fix the indicator definition. Common issues include:
  - Filter condition uses display values instead of database values
  - Aggregate field name is incorrect
  - Table name is misspelled

**Cause 3: Data Collection Schedule**

The indicator is set to collect data on a schedule (e.g., daily) and has not yet reached the next collection time.

- **Check:** Open the indicator → **Schedule** tab. Verify the collection frequency.
- **Resolution:** Manually run data collection for the indicator, or wait for the next scheduled collection.

**Cause 4: Insufficient Data Points**

The indicator needs a minimum number of data points before displaying trend data (e.g., at least 2 data points for a trend line).

- **Check:** Navigate to **Performance Analytics** → **Indicator Data** and verify data points exist.
- **Resolution:** No action needed — data will appear after the next collection cycle.

---

## 8. Catalog Item Not Appearing

### Symptom

The "Borrow a Book" catalog item does not appear in the service catalog.

### Possible Causes and Resolutions

**Cause 1: Catalog Item Not Active**

The record producer is inactive.

- **Check:** Navigate to **Service Catalog** → **Catalog Definitions** → **Maintain Items**. Open the "Borrow a Book" item and verify **Active** is checked.
- **Resolution:** Check the **Active** checkbox and save.

**Cause 2: Catalog Category Not Active**

The parent catalog category is inactive.

- **Check:** Navigate to **Service Catalog** → **Categories**. Open the "Book Services" category and verify **Active** is checked.
- **Resolution:** Check the **Active** checkbox on the category.

**Cause 3: Portal Not Selected**

The catalog item is not associated with the correct portal.

- **Check:** Open the catalog item → **Portals** tab. Verify `x_univ_library_portal` is selected.
- **Resolution:** Add the portal to the catalog item's portal list.

**Cause 4: User Does Not Have Role**

The current user does not have the role required to view the catalog item.

- **Check:** Open the catalog item → **Roles** tab. Verify `student_library` role is listed for end-user access.
- **Resolution:** Add the `x_univ_library.student_library` role to the catalog item's role list.

---

## 9. Role Assignment Not Creating Profile

### Symptom

When a user is assigned the `student_library` role, the corresponding `u_library_students` profile record is not created.

### Possible Causes and Resolutions

**Cause 1: Role Trigger Business Rule Not Active**

The Business Rule that creates the student profile on role assignment is inactive.

- **Check:** Navigate to **System Definition** → **Business Rules**. Search for `[LIB] Create Student Profile on Role` or similar. Verify it is active.
- **Resolution:** Activate the Business Rule.

**Cause 2: User Already Has Profile**

The user already has a student profile record, so the Business Rule correctly skips creation.

- **Check:** Navigate to `u_library_students` table. Filter by the user's reference.
- **Resolution:** No action needed — the profile already exists.

**Cause 3: Business Rule Script Error**

The Business Rule script encounters an error during execution.

- **Check:** Review the Business Rule logs. Navigate to **System Log** → **All Logs** and filter by the Business Rule name.
- **Resolution:** Fix the script error in the Business Rule. Common issues include:
  - Missing field values
  - Reference field pointing to a non-existent record
  - Cross-scope access restriction

---

## 10. Reference Qualifier Not Filtering

### Symptom

A reference field shows all records instead of filtering based on the reference qualifier.

### Possible Causes and Resolutions

**Cause 1: Dictionary Reference Qualifier Not Set**

The reference qualifier is not configured on the dictionary record.

- **Check:** Navigate to the dictionary record for the reference field. Verify the **Reference Qualifier** field contains the expected condition.
- **Resolution:** Set the reference qualifier in the dictionary record. For example: `u_active=true^u_available_copies>0^ORDERBYu_title`.

**Cause 2: Qualifier Condition Syntax Error**

The reference qualifier contains a syntax error, causing it to be ignored.

- **Check:** Review the qualifier syntax. ServiceNow reference qualifiers use the standard encoded query format: `field=value^field=value` separated by `^`.
- **Resolution:** Correct the syntax. Use the condition builder to construct the qualifier instead of typing it manually.

**Cause 3: Reference Qualifier on Form Overrides Dictionary**

A reference qualifier on the form level overrides the dictionary-level qualifier.

- **Check:** Open the form in the form designer. Select the reference field and check if a **Reference Qualifier** is set at the form level.
- **Resolution:** Remove the form-level qualifier or align it with the dictionary qualifier.

**Cause 4: Database Value vs. Display Value**

The qualifier uses a display value instead of the database value for a choice field.

- **Check:** If the qualifier filters on a choice field, ensure the condition uses the choice value (e.g., `available`) and not the display label (e.g., `Available`).
- **Resolution:** Update the qualifier to use the database value.

---

## 11. General Debugging Tips

### Enable Debugging

| Artifact | Debugging Method |
| ---------- | ----------------- |
| Business Rules | Add `gs.log()` statements; check System Logs |
| Script Includes | Test in **System Definition** → **Scripts Background** |
| Client Scripts | Use browser console (`console.log()`); check Browser Console |
| Flow Designer | Enable **Flow Debugger** in Flow Designer configuration |
| Notifications | Navigate to **System Notification** → **Email** → **Email Logs** |
| ACLs | Enable **ACL Debug** via system property `glide.security.acl.debug` |
| Portal | Use browser developer tools; check portal widget logs |
| SLA | Navigate to **SLA** → **SLA Timer** → view timer details |
| PA Indicators | Navigate to **PA** → **Collectors** → **Job History** |
| Scheduled Jobs | Navigate to **Scheduled Jobs** → **Execution History** |

### Common System Properties for Debugging

```javascript
// Enable ACL debugging (temporarily in DEV/TEST only)
glide.security.acl.debug = true

// Enable workflow/flow debugging
com.glide.workflow.debug = true

// Enable notification debugging
glide.email.debug = true

// Enable script debugging
javascript.debug = true
```

### Log Categories

All library Business Rules and Script Includes log with a consistent prefix:

```javascript
gs.log('[LIB] ' + message, 'LibraryBorrowService');
```

Search for `[LIB]` in **System Log** → **All Logs** to filter library-specific messages.

---

*References: [ImplementationGuide.md](ImplementationGuide.md) | [DeploymentGuide.md](DeploymentGuide.md)*
*See also: [MaintenanceGuide.md](MaintenanceGuide.md)*
