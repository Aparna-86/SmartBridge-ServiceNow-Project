# Implementation Guide

# Smart Library Request Workflow — ServiceNow Enterprise Solution

> **Document Type:** Implementation Guide  
> **Version:** 2.0.0  
> **Application Scope:** `x_univ_library`  
> **Status:** Final — Complete  
> **Platform:** ServiceNow Washington DC

---

## Table of Contents

1. [Prerequisites](#1-prerequisites)
2. [Step 1: Create Scoped Application in Studio](#2-step-1-create-scoped-application-in-studio)
3. [Step 2: Create All 11 Tables](#3-step-2-create-all-11-tables)
4. [Step 3: Configure Dictionary Overrides](#4-step-3-configure-dictionary-overrides)
5. [Step 4: Create All 5 Script Includes](#5-step-4-create-all-5-script-includes)
6. [Step 5: Create All 15 Business Rules](#6-step-5-create-all-15-business-rules)
7. [Step 6: Configure All 8 UI Policies](#7-step-6-configure-all-8-ui-policies)
8. [Step 7: Create All 10 UI Actions](#8-step-7-create-all-10-ui-actions)
9. [Step 8: Create All 12 Client Scripts](#9-step-8-create-all-12-client-scripts)
10. [Step 9: Configure All 3 Flow Designer Flows](#10-step-9-configure-all-3-flow-designer-flows)
11. [Step 10: Configure All 13 Notification Records + Email Templates](#11-step-10-configure-all-13-notification-records--email-templates)
12. [Step 11: Create All 4 Roles with ACLs](#12-step-11-create-all-4-roles-with-acls)
13. [Step 12: Configure Service Portal with 8 Pages](#13-step-12-configure-service-portal-with-8-pages)
14. [Step 13: Create All 8 Reports with Scheduled Delivery](#14-step-13-create-all-8-reports-with-scheduled-delivery)
15. [Step 14: Configure 2 Dashboards with Performance Analytics Indicators](#15-step-14-configure-2-dashboards-with-performance-analytics-indicators)
16. [Step 15: Set Up Scheduled Jobs](#16-step-15-set-up-scheduled-jobs)
17. [Step 16: Create Service Catalog Item](#17-step-16-create-service-catalog-item)
18. [Step 17: Import Sample Data](#18-step-17-import-sample-data)
19. [Step 18: Run Test Suite](#19-step-18-run-test-suite)
20. [Step 19: Configure SLA Definitions](#20-step-19-configure-sla-definitions)
21. [Step 20: UAT Sign-Off and Production Deployment](#21-step-20-uat-sign-off-and-production-deployment)

---

## 1. Prerequisites

Before beginning implementation, ensure the following prerequisites are met:

### ServiceNow Instance

| Requirement | Specification |
| ------------- | --------------- |
| **Platform Version** | ServiceNow Washington DC (or later compatible) |
| **Instance Type** | Development instance dedicated to this project |
| **Instance URL** | `https://university-dev.service-now.com` |
| **Application Scope** | `x_univ_library` |
| **Update Set Name** | `LIB-Smart-Library-Request-Workflow-v2.0` |

### Admin Access

The implementing administrator must have the following roles assigned:

- `admin` (global)
- `user_admin` (for user/group management)
- `report_admin` (for report creation)
- `scheduled_job_admin` (for scheduled job management)
- `pa_admin` (for Performance Analytics configuration)
- `sla_admin` (for SLA definition management)
- `sp_admin` (for Service Portal configuration)
- `flow_designer_admin` (for Flow Designer access)
- `rest_api_explorer` (for API testing)

### Required Modules

The following plugins must be activated on the instance:

| Plugin | Name | Activation Required |
| -------- | ------ | ------------------- |
| Service Portal | `com.glideapp.servicecatalog` | Yes |
| Flow Designer | `com.snc.service_flow_designer` | Yes |
| Performance Analytics | `com.snc.pa.performance_analytics` | Yes |
| Automated Test Framework (ATF) | `com.glide.automated_testing_framework` | Recommended |
| SLA Management | `com.snc.sla.plugin` | Yes |
| Email Notifications | `com.glide.notifications` | Yes |
| Import Sets | `com.glide.import_set` | Recommended |

### Development Team Roles

| Role | Person | Responsibility |
| ------ | -------- | ---------------- |
| Technical Lead | System Architect | Architecture decisions, code review |
| ServiceNow Developer | Developer | Implementation of all artifacts |
| QA Engineer | QA Team | Test case execution, ATF automation |
| Business Analyst | BA Team | Requirements clarification, UAT coordination |
| Library Stakeholder | Library Manager | Domain expertise, feature validation |

---

## 2. Step 1: Create Scoped Application in Studio

### 2.1 Launch Studio

1. Navigate to **All** → **System Applications** → **Studio**.
2. Click **Create Application**.

### 2.2 Application Properties

Set the following properties in the application creation wizard:

| Property | Value |
| ---------- | ------- |
| **Name** | Smart Library Request Workflow |
| **Scope** | `x_univ_library` |
| **Description** | Enterprise library management system for university library operations including book catalog management, student borrow requests, approval workflows, book issuance, return processing, notifications, and reporting. |
| **ACL** | Use application ACL |
| **Update Set Name** | `LIB-Smart-Library-Request-Workflow-v2.0` |

### 2.3 Application Metadata

After creation, update the application record:

1. Navigate to **System Applications** → **Applications**.
2. Open the `x_univ_library` application record.
3. Set **Version** to `2.0.0`.
4. Set **Vendor** to `University IT Department`.
5. Set **Vendor Prefix** to `LIB`.
6. Set **Maintenance Plan** to `Quarterly`.

### 2.4 Scope Rules Configuration

Configure the following scope rules in the application record:

| Rule | Setting |
| ------ | --------- |
| **Allow public read** | Enabled (for portal access) |
| **Allow public write** | Disabled |
| **Allow scripted REST API** | Enabled |
| **Allow access to GlideRecord** | Enabled |
| **Allow access to sys_properties** | Read only |
| **Allow outbound HTTP requests** | Disabled |

---

## 3. Step 2: Create All 11 Tables

### 3.1 Create Tables in Studio

For each table, navigate to **Studio** → **Data** → **Tables** → **New**. Create the following tables in the order specified (to satisfy reference dependencies):

### Table Creation Order

| Order | Table Name | Label | Extends |
| ------- | ----------- | ------- | --------- |
| 1 | `u_library_categories` | Library Categories | — |
| 2 | `u_library_books` | Library Books | — |
| 3 | `u_library_students` | Library Students | — |
| 4 | `u_library_librarians` | Library Librarians | — |
| 5 | `u_library_configuration` | Library Configuration | — |
| 6 | `u_library_borrow_requests` | Library Borrow Requests | — |
| 7 | `u_library_approvals` | Library Approvals | — |
| 8 | `u_library_issuance_records` | Library Issuance Records | — |
| 9 | `u_library_return_records` | Library Return Records | — |
| 10 | `u_library_notification_log` | Library Notification Log | — |
| 11 | `u_library_audit_log` | Library Audit Log | — |

### 3.2 Table: u_library_categories

1. Create table `u_library_categories`.
2. Add the following fields:

| Field Name | Column Name | Type | Max Length | Mandatory |
| ----------- | ------------- | ------ | ----------- | ----------- |
| Name | `u_name` | String | 100 | Yes |
| Description | `u_description` | Multi-line Text | 1000 | No |
| Icon | `u_icon` | String | 50 | No |
| Active | `u_active` | True/False | — | Yes |

1. Create indexes: `u_name`, `u_active`.
2. Set **Create index for name** to **Unique index** (for unique among active constraint).

### 3.3 Table: u_library_books

1. Create table `u_library_books`.
2. Add the following fields:

| Field Name | Column Name | Type | Max Length | Mandatory |
| ----------- | ------------- | ------ | ----------- | ----------- |
| Title | `u_title` | String | 255 | Yes |
| Author | `u_author` | String | 255 | Yes |
| ISBN | `u_isbn` | String | 20 | Yes |
| Category | `u_category` | Reference (u_library_categories) | — | No |
| Publisher | `u_publisher` | String | 255 | No |
| Publication Year | `u_publication_year` | Integer | — | No |
| Edition | `u_edition` | String | 50 | No |
| Total Copies | `u_total_copies` | Integer | — | Yes |
| Available Copies | `u_available_copies` | Integer | — | Yes |
| Location | `u_location` | String | 100 | No |
| Description | `u_description` | Multi-line Text | 2000 | No |
| Cover Image URL | `u_cover_image_url` | URL | 500 | No |
| Availability Status | `u_availability_status` | Choice | — | Yes |
| Active | `u_active` | True/False | — | Yes |

1. Configure the **Availability Status** choice list:

| Value | Label |
|-------|-------|
| `available` | Available |
| `unavailable` | Unavailable |

1. Create indexes: `u_isbn` (unique), `u_title`, `u_author`, `u_available_copies`, `u_active`.

### 3.4 Table: u_library_students

1. Create table `u_library_students`.
2. Add the following fields:

| Field Name | Column Name | Type | Max Length | Mandatory |
| ----------- | ------------- | ------ | ----------- | ----------- |
| User | `u_user` | Reference (sys_user) | — | Yes |
| University ID | `u_university_id` | String | 50 | Yes |
| Department | `u_department` | String | 100 | No |
| Enrollment Year | `u_enrollment_year` | Integer | — | No |
| Max Borrow Limit | `u_max_borrow_limit` | Integer | — | Yes |
| Active Borrow Count | `u_active_borrow_count` | Integer | — | Yes |
| Active | `u_active` | True/False | — | Yes |

1. Create indexes: `u_university_id` (unique), `u_user`, `u_active`.

### 3.5 Table: u_library_librarians

1. Create table `u_library_librarians`.
2. Add the following fields:

| Field Name | Column Name | Type | Max Length | Mandatory |
| ----------- | ------------- | ------ | ----------- | ----------- |
| User | `u_user` | Reference (sys_user) | — | Yes |
| Staff ID | `u_staff_id` | String | 50 | Yes |
| Department Assignments | `u_department_assignments` | List (u_library_categories) | — | No |
| Active | `u_active` | True/False | — | Yes |

1. Create indexes: `u_staff_id` (unique), `u_user`, `u_active`.

### 3.6 Table: u_library_configuration

1. Create table `u_library_configuration`.
2. Add the following fields:

| Field Name | Column Name | Type | Max Length | Mandatory |
| ----------- | ------------- | ------ | ----------- | ----------- |
| Parameter Key | `u_parameter_key` | String | 100 | Yes |
| Parameter Value | `u_parameter_value` | String | 500 | Yes |
| Data Type | `u_data_type` | Choice | — | Yes |
| Description | `u_description` | String | 500 | No |
| Updated At | `u_updated_at` | Date/Time | — | Yes |
| Updated By | `u_updated_by` | Reference (sys_user) | — | Yes |

1. Configure the **Data Type** choice list:

| Value | Label |
| ------- | ------- |
| `integer` | Integer |
| `boolean` | Boolean |
| `string` | String |
| `list` | List |

1. Create indexes: `u_parameter_key` (unique).

### 3.7 Table: u_library_borrow_requests

1. Create table `u_library_borrow_requests`.
2. Add the following fields:

| Field Name | Column Name | Type | Max Length | Mandatory |
| ----------- | ------------- | ------ | ----------- | ----------- |
| Request Number | `u_number` | String | 20 | Yes |
| Book | `u_book` | Reference (u_library_books) | — | Yes |
| Student | `u_student` | Reference (u_library_students) | — | Yes |
| Requested Pickup Date | `u_requested_pickup_date` | Date | — | No |
| Notes | `u_notes` | Multi-line Text | 500 | No |
| Status | `u_status` | Choice | — | Yes |
| Overdue Flag | `u_overdue_flag` | True/False | — | Yes |
| Submitted At | `u_submitted_at` | Date/Time | — | Yes |
| Opened By | `u_opened_by` | Reference (sys_user) | — | Yes |

1. Configure the **Status** choice list:

| Value | Label |
| ------- | ------- |
| `pending_approval` | Pending Approval |
| `approved` | Approved |
| `rejected` | Rejected |
| `escalated` | Escalated |
| `issued` | Issued |
| `overdue` | Overdue |
| `returned` | Returned |
| `cancelled` | Cancelled |

1. Create indexes: `u_number` (unique), `u_student`, `u_book`, `u_status`, `u_overdue_flag`, `u_submitted_at`.

### 3.8 Table: u_library_approvals

1. Create table `u_library_approvals`.
2. Add the following fields:

| Field Name | Column Name | Type | Max Length | Mandatory |
| ----------- | ------------- | ------ | ----------- | ----------- |
| Borrow Request | `u_borrow_request` | Reference (u_library_borrow_requests) | — | Yes |
| Approver | `u_approver` | Reference (sys_user) | — | Yes |
| Decision | `u_decision` | Choice | — | Yes |
| Reason | `u_reason` | Multi-line Text | 500 | No |
| Decided At | `u_decided_at` | Date/Time | — | Yes |
| Override Notes | `u_override_notes` | Multi-line Text | 500 | No |

1. Configure the **Decision** choice list:

| Value | Label |
| ------- | ------- |
| `approved` | Approved |
| `rejected` | Rejected |
| `escalated` | Escalated |

1. Create indexes: `u_borrow_request`, `u_decided_at`.

### 3.9 Table: u_library_issuance_records

1. Create table `u_library_issuance_records`.
2. Add the following fields:

| Field Name | Column Name | Type | Max Length | Mandatory |
| ----------- | ------------- | ------ | ----------- | ----------- |
| Borrow Request | `u_borrow_request` | Reference (u_library_borrow_requests) | — | Yes |
| Book | `u_book` | Reference (u_library_books) | — | Yes |
| Student | `u_student` | Reference (u_library_students) | — | Yes |
| Issued By | `u_issued_by` | Reference (u_library_librarians) | — | Yes |
| Issue Date | `u_issue_date` | Date/Time | — | Yes |
| Expected Return Date | `u_expected_return_date` | Date | — | Yes |
| Loan Period Days | `u_loan_period_days` | Integer | — | Yes |

1. Create indexes: `u_borrow_request`, `u_expected_return_date`.

### 3.10 Table: u_library_return_records

1. Create table `u_library_return_records`.
2. Add the following fields:

| Field Name | Column Name | Type | Max Length | Mandatory |
| ----------- | ------------- | ------ | ----------- | ----------- |
| Borrow Request | `u_borrow_request` | Reference (u_library_borrow_requests) | — | Yes |
| Actual Return Date | `u_actual_return_date` | Date/Time | — | Yes |
| Return Condition | `u_return_condition` | Choice | — | Yes |
| Overdue Days | `u_overdue_days` | Integer | — | Yes |
| Damage Reported | `u_damage_reported` | True/False | — | Yes |
| Damage Description | `u_damage_description` | Multi-line Text | 1000 | No |
| Received By | `u_received_by` | Reference (u_library_librarians) | — | Yes |

1. Configure the **Return Condition** choice list:

| Value | Label |
|-------|-------|
| `on_time` | On Time |
| `overdue` | Overdue |

1. Create indexes: `u_borrow_request`, `u_actual_return_date`.

### 3.11 Table: u_library_notification_log

1. Create table `u_library_notification_log`.
2. Add the following fields:

| Field Name | Column Name | Type | Max Length | Mandatory |
| ----------- | ------------- | ------ | ----------- | ----------- |
| Recipient | `u_recipient` | Reference (sys_user) | — | Yes |
| Event Type | `u_event_type` | String | 100 | Yes |
| Template ID | `u_template_id` | String | 20 | Yes |
| Sent At | `u_sent_at` | Date/Time | — | Yes |
| Delivery Status | `u_delivery_status` | Choice | — | Yes |
| Retry Count | `u_retry_count` | Integer | — | Yes |

1. Configure the **Delivery Status** choice list:

| Value | Label |
|-------|-------|
| `sent` | Sent |
| `failed` | Failed |

1. Create indexes: `u_recipient`, `u_event_type`, `u_sent_at`.

### 3.12 Table: u_library_audit_log

1. Create table `u_library_audit_log`.
2. Add the following fields:

| Field Name | Column Name | Type | Max Length | Mandatory |
| ----------- | ------------- | ------ | ----------- | ----------- |
| Table Name | `u_table_name` | String | 100 | Yes |
| Record Sys ID | `u_record_sys_id` | String | 32 | Yes |
| User | `u_user` | Reference (sys_user) | — | Yes |
| Action | `u_action` | Choice | — | Yes |
| Field Name | `u_field_name` | String | 100 | No |
| Old Value | `u_old_value` | Multi-line Text | 5000 | No |
| New Value | `u_new_value` | Multi-line Text | 5000 | No |
| Timestamp | `u_timestamp` | Date/Time | — | Yes |

1. Configure the **Action** choice list:

| Value | Label |
| ------- | ------- |
| `created` | Created |
| `updated` | Updated |
| `deleted` | Deleted |
| `access_denied` | Access Denied |

1. Create indexes: `u_table_name`, `u_user`, `u_timestamp`, `u_action`.

---

## 4. Step 3: Configure Dictionary Overrides

### 4.1 Field Labels

Override default field labels for improved usability on each table. Navigate to each field's dictionary entry and update the label.

| Table | Column | Label Override |
| ------- | -------- | ---------------- |
| u_library_books | `sys_created_by` | Created By |
| u_library_books | `sys_created_on` | Created On |
| u_library_books | `sys_updated_by` | Last Updated By |
| u_library_books | `sys_updated_on` | Last Updated On |
| u_library_borrow_requests | `sys_created_on` | Submitted At |

### 4.2 Reference Qualifiers

Configure reference qualifiers on reference fields to restrict selectable values:

| Table | Field | Reference Qualifier |
| ------- | ------- | --------------------- |
| u_library_books | `u_category` | `u_active=true^ORDERBYu_name` |
| u_library_borrow_requests | `u_book` | `u_active=true^u_available_copies>0^ORDERBYu_title` |
| u_library_borrow_requests | `u_student` | `u_active=true^ORDERBYu_user.name` |
| u_library_approvals | `u_borrow_request` | `u_status=pending_approval^ORDERBYu_number` |
| u_library_issuance_records | `u_borrow_request` | `u_status=approved^ORDERBYu_number` |
| u_library_issuance_records | `u_book` | `u_active=true^ORDERBYu_title` |
| u_library_return_records | `u_borrow_request` | `u_status=issued^ORDERBYu_number` |

### 4.3 Default Values

Configure default values for fields:

| Table | Field | Default Value |
| ------- | ------- | --------------- |
| u_library_books | `u_total_copies` | `1` |
| u_library_books | `u_available_copies` | Calculated: `u_total_copies` |
| u_library_books | `u_availability_status` | `available` |
| u_library_books | `u_active` | `true` |
| u_library_students | `u_max_borrow_limit` | `5` |
| u_library_students | `u_active_borrow_count` | `0` |
| u_library_students | `u_active` | `true` |
| u_library_librarians | `u_active` | `true` |
| u_library_borrow_requests | `u_status` | `pending_approval` |
| u_library_borrow_requests | `u_overdue_flag` | `false` |
| u_library_borrow_requests | `u_submitted_at` | `(JavaScript) new GlideDateTime()` |
| u_library_issuance_records | `u_loan_period_days` | `14` |
| u_library_return_records | `u_return_condition` | `on_time` |
| u_library_return_records | `u_overdue_days` | `0` |
| u_library_return_records | `u_damage_reported` | `false` |
| u_library_notification_log | `u_delivery_status` | `sent` |
| u_library_notification_log | `u_retry_count` | `0` |
| u_library_configuration | `u_updated_at` | `(JavaScript) new GlideDateTime()` |

### 4.4 Number Format Configuration

Configure the auto-number field for borrow requests:

1. Navigate to **System Definition** → **Number Tables**.
2. Click **New**.
3. Configure:

| Property | Value |
| ---------- | ------- |
| **Table** | `u_library_borrow_requests` |
| **Field** | `u_number` |
| **Prefix** | `LIB-` |
| **Number Length** | `8` (zero-padded) |

---

## 5. Step 4: Create All 5 Script Includes

### 5.1 Create Script Includes in Studio

Navigate to **Studio** → **Server** → **Script Include** → **New** for each Script Include.

### 5.2 Script Include: LibraryBorrowService

| Property | Value |
| ---------- | ------- |
| **Name** | `LibraryBorrowService` |
| **Access** | `callerAccess = 'caller_track'` |
| **API Name** | `x_univ_library.LibraryBorrowService` |
| **Description** | Borrow request lifecycle operations |

**Methods:**

- `createBorrowRequest(bookSysId, studentSysId, pickupDate, notes)` — Validates pre-conditions and creates a new borrow request. Returns `{ success, requestSysId, message }`.
- `cancelBorrowRequest(requestSysId, studentSysId)` — Cancels a pending request. Restores available copies. Returns `{ success, message }`.
- `approveRequest(requestSysId, approverSysId, notes)` — Approves a pending request. Sets status to Approved. Returns `{ success, message }`.
- `rejectRequest(requestSysId, approverSysId, reason)` — Rejects a pending request. Restores available copies. Returns `{ success, message }`.
- `escalateRequest(requestSysId, approverSysId)` — Escalates a request after SLA breach. Returns `{ success, message }`.
- `getStudentActiveRequests(studentSysId)` — Returns GlideRecord of active requests for a student.
- `getRequestHistory(studentSysId, limit)` — Returns recent request history for a student.
- `_validateRequest(bookSysId, studentSysId)` — Internal validation of student eligibility and book availability.

### 5.3 Script Include: LibraryAvailabilityService

| Property | Value |
| ---------- | ------- |
| **Name** | `LibraryAvailabilityService` |
| **Access** | `callerAccess = 'caller_track'` |
| **API Name** | `x_univ_library.LibraryAvailabilityService` |
| **Description** | Book availability counter management |

**Methods:**

- `incrementAvailableCopies(bookSysId)` — Increments available copies by 1. Called on cancellation/rejection/return.
- `decrementAvailableCopies(bookSysId)` — Decrements available copies by 1. Called on issuance.
- `getAvailableCount(bookSysId)` — Returns current available copies count.
- `isBookAvailable(bookSysId)` — Returns boolean indicating if book has available copies.
- `refreshAvailabilityStatus(bookSysId)` — Recalculates and updates availability status based on available copies.
- `getTotalBorrowedCount(bookSysId)` — Returns count of currently issued copies for a book.

### 5.4 Script Include: LibraryNotificationHelper

| Property | Value |
| ---------- | ------- |
| **Name** | `LibraryNotificationHelper` |
| **Access** | `callerAccess = 'caller_track'` |
| **API Name** | `x_univ_library.LibraryNotificationHelper` |
| **Description** | Notification sending and logging |

**Methods:**

- `sendNotification(eventName, recipientSysId, context)` — Sends a notification using the named event. Returns `{ success, message }`.
- `logNotification(recipientSysId, eventType, templateId, status)` — Writes to the notification log table.
- `isNotificationsEnabled()` — Checks the master notification toggle in configuration.
- `isEmailEnabled()` — Checks the email notification toggle in configuration.
- `getNotificationRetryCount()` — Returns maximum retry count from configuration.
- `sendOverdueNotification(studentSysId, requestSysId, daysOverdue)` — Sends an overdue reminder at the appropriate threshold.

### 5.5 Script Include: LibraryValidationService

| Property | Value |
| ---------- | ------- |
| **Name** | `LibraryValidationService` |
| **Access** | `callerAccess = 'caller_track'` |
| **API Name** | `x_univ_library.LibraryValidationService` |
| **Description** | Pre-save validation library |

**Methods:**

- `validateBorrowRequest(bookSysId, studentSysId)` — Runs all validation rules. Returns `{ valid: boolean, message: string }`.
- `validateStudentEligibility(studentSysId)` — Checks max borrow limit, active borrow count, fines, and account status.
- `validateBookAvailability(bookSysId)` — Checks if book has available copies and is active.
- `validateDuplicateRequest(bookSysId, studentSysId)` — Checks for existing active request for same book/student.
- `validatePickupDate(pickupDate)` — Validates pickup date is within 1–30 days from today.
- `validateReturnCondition(damageReported, damageDescription)` — Validates damage description is provided when damage is reported.

### 5.6 Script Include: LibraryAuditLogger

| Property | Value |
| ---------- | ------- |
| **Name** | `LibraryAuditLogger` |
| **Access** | `callerAccess = 'caller_track'` |
| **API Name** | `x_univ_library.LibraryAuditLogger` |
| **Description** | Immutable audit log writes |

**Methods:**

- `logCreate(tableName, recordSysId, userSysId)` — Logs a create event.
- `logUpdate(tableName, recordSysId, userSysId, fieldName, oldValue, newValue)` — Logs an update event with field-level detail.
- `logDelete(tableName, recordSysId, userSysId)` — Logs a delete event.
- `logAccessDenied(tableName, recordSysId, userSysId)` — Logs an access denial event.
- `purgeOldRecords(retentionDays)` — Deletes audit records older than the retention period (admin-only scheduled job).

---

## 6. Step 5: Create All 15 Business Rules

### 6.1 Create Business Rules in Studio

Navigate to **Studio** → **Server** → **Business Rule** → **New** for each rule. All business rules delegate business logic to Script Includes.

### 6.2 Business Rule Inventory

| ID | Name | Table | When | Condition |
| ---- | ------ | ------- | ------ | ----------- |
| BR-01 | `[LIB] Availability Auto-Update` | u_library_books | Before Update | `current.u_available_copies.changes()` |
| BR-02 | `[LIB] Default Available Copies` | u_library_books | Before Insert | Always |
| BR-03 | `[LIB] Validate Pickup Date` | u_library_borrow_requests | Before Insert | Always |
| BR-04 | `[LIB] Validate Borrow Limit` | u_library_borrow_requests | Before Insert | Always |
| BR-05 | `[LIB] Auto-Number Assignment` | u_library_borrow_requests | Before Insert | Always |
| BR-06 | `[LIB] Set Opened By` | u_library_borrow_requests | Before Insert | Always |
| BR-07 | `[LIB] Prevent Duplicate Request` | u_library_borrow_requests | Before Insert | Always |
| BR-08 | `[LIB] Check Overdue on Return` | u_library_return_records | Before Insert | Always |
| BR-09 | `[LIB] Update Active Borrow Count` | u_library_students | After Update | Status changed to Issued/Returned |
| BR-10 | `[LIB] Restore Copies on Reject` | u_library_approvals | After Insert | Decision = Rejected |
| BR-11 | `[LIB] Sync Available Copies on Issuance` | u_library_issuance_records | After Insert | Always |
| BR-12 | `[LIB] Sync Issuance on Return` | u_library_return_records | After Insert | Always |
| BR-13 | `[LIB] Update Status to Overdue` | u_library_borrow_requests | Before Query | Scheduled Job trigger |
| BR-14 | `[LIB] Log All Creates` | u_library_* | After Insert | Always |
| BR-15 | `[LIB] Log All Updates` | u_library_* | After Update | Always |

### 6.3 BR-01: Availability Auto-Update

```text
Name: [LIB] Availability Auto-Update
Table: u_library_books
When: Before Update
Condition: current.u_available_copies.changes()
Order: 100
```

Sets `u_availability_status` to `available` or `unavailable` when `u_available_copies` changes to 0 or from 0.

### 6.4 BR-02: Default Available Copies

```text`nName: [LIB] Default Available Copies
Table: u_library_books
When: Before Insert
Condition: current.isNewRecord()
Order: 100
```

Sets `u_available_copies` equal to `u_total_copies` when a new book record is inserted.

### 6.5 BR-03: Validate Pickup Date

```text`nName: [LIB] Validate Pickup Date
Table: u_library_borrow_requests
When: Before Insert
Condition: current.u_requested_pickup_date.changes()
Order: 100
```

Validates that the requested pickup date is between 1 and 30 days from the current date. Throws an error with a descriptive message if validation fails.

### 6.6 BR-04: Validate Borrow Limit

```text`nName: [LIB] Validate Borrow Limit
Table: u_library_borrow_requests
When: Before Insert
Condition: current.isNewRecord()
Order: 200
```

Calls `LibraryValidationService.validateStudentEligibility()` to check whether the student has reached their maximum borrow limit. Throws an error if the limit is exceeded.

### 6.7 BR-05: Auto-Number Assignment

```text`nName: [LIB] Auto-Number Assignment
Table: u_library_borrow_requests
When: Before Insert
Condition: current.u_number.nil()
Order: 50
```

Assigns the next number in the LIB-XXXXXXXX sequence using the standard auto-numbering mechanism.

### 6.8 BR-06: Set Opened By

```text`nName: [LIB] Set Opened By
Table: u_library_borrow_requests
When: Before Insert
Condition: current.isNewRecord()
Order: 50
```

Sets `u_opened_by` to the current user's sys_id.

### 6.9 BR-07: Prevent Duplicate Request

```text`nName: [LIB] Prevent Duplicate Request
Table: u_library_borrow_requests
When: Before Insert
Condition: current.isNewRecord()
Order: 300
```

Calls `LibraryValidationService.validateDuplicateRequest()` to check if the student already has an active request for the same book. Throws an error if a duplicate exists.

### 6.10 BR-08: Check Overdue on Return

```text`nName: [LIB] Check Overdue on Return
Table: u_library_return_records
When: Before Insert
Condition: current.isNewRecord()
Order: 100
```

Compares the actual return date with the expected return date from the associated issuance record. Sets `u_return_condition` and `u_overdue_days` accordingly.

### 6.11 BR-09: Update Active Borrow Count

```text`nName: [LIB] Update Active Borrow Count
Table: u_library_students
When: After Update (via issuance/return flow)
Condition: current.u_active_borrow_count.changes()
Order: 100
```

Increments or decrements the student's active borrow count based on the issuance or return event.

### 6.12 BR-10: Restore Copies on Reject

```text`nName: [LIB] Restore Copies on Reject
Table: u_library_approvals
When: After Insert
Condition: current.u_decision == 'rejected'
Order: 100
```

Calls `LibraryAvailabilityService.incrementAvailableCopies()` to restore the book's available copies when a request is rejected.

### 6.13 BR-11: Sync Available Copies on Issuance

```text`nName: [LIB] Sync Available Copies on Issuance
Table: u_library_issuance_records
When: After Insert
Condition: current.isNewRecord()
Order: 100
```

Calls `LibraryAvailabilityService.decrementAvailableCopies()` to reduce available copies when a book is issued.

### 6.14 BR-12: Sync Issuance on Return

```text`nName: [LIB] Sync Issuance on Return
Table: u_library_return_records
When: After Insert
Condition: current.isNewRecord()
Order: 100
```

Calls `LibraryAvailabilityService.incrementAvailableCopies()` to increase available copies when a book is returned.

### 6.15 BR-13: Update Status to Overdue

```text`nName: [LIB] Update Status to Overdue
Table: u_library_borrow_requests
When: Before Query (triggered by Scheduled Job)
Condition: current.u_status == 'issued' AND current.u_overdue_flag == false
Order: 100
```

Sets the status to `overdue` and the overdue flag to `true` when the expected return date has passed.

### 6.16 BR-14: Log All Creates

```text`nName: [LIB] Log All Creates
Table: All library tables (u_library_*)
When: After Insert
Condition: current.isNewRecord()
Order: 100
```

Calls `LibraryAuditLogger.logCreate()` with the table name, record sys_id, and current user.

### 6.17 BR-15: Log All Updates

```text`nName: [LIB] Log All Updates
Table: All library tables (u_library_*)
When: After Update
Condition: Changes detected on tracked fields
Order: 100
```

Calls `LibraryAuditLogger.logUpdate()` with field-level change details for each tracked field.

---

## 7. Step 6: Configure All 8 UI Policies

### 7.1 Create UI Policies in Studio

Navigate to **Studio** → **Client** → **UI Policy** → **New** for each policy.

### 7.2 UI Policy Inventory

| ID | Name | Table | Condition | On |
| ---- | ------ | ------- | ----------- | ---- |
| UP-01 | `[LIB] Rejection Reason Mandatory` | u_library_approvals | Decision is Rejected | Insert, Update |
| UP-02 | `[LIB] Damage Description Required` | u_library_return_records | Damage Reported is true | Insert, Update |
| UP-03 | `[LIB] Request Read-Only When Closed` | u_library_borrow_requests | Status is Returned/Rejected/Cancelled | Update |
| UP-04 | `[LIB] Student Field Read-Only After Create` | u_library_borrow_requests | Not new record | Update |
| UP-05 | `[LIB] Issue Date Mandatory on Issuance` | u_library_issuance_records | Not new record | Update |
| UP-06 | `[LIB] Damage Description Hidden When No Damage` | u_library_return_records | Damage Reported is false | Insert, Update |
| UP-07 | `[LIB] Configuration Value Read-Only` | u_library_configuration | Not new record | Update |
| UP-08 | `[LIB] Approval Borrow Request Read-Only` | u_library_approvals | Not null | Update |

### 7.3 UP-01: Rejection Reason Mandatory on Reject

**Condition:** `u_decision == 'rejected'`

| Field | Visible | Read-only | Mandatory |
|-------|---------|-----------|-----------|
| `u_reason` | True | False | True |

### 7.4 UP-02: Damage Description Required on Return

**Condition:** `u_damage_reported == true`

| Field | Visible | Read-only | Mandatory |
|-------|---------|-----------|-----------|
| `u_damage_description` | True | False | True |

### 7.5 UP-03: Borrow Request Read-Only When Closed

**Condition:** `u_status IN ('returned', 'rejected', 'cancelled')`

| Field | Visible | Read-only |
|-------|---------|-----------|
| All fields | True | True |

### 7.6 UP-04: Student Field Read-Only After Create

**Condition:** `!isNewRecord()`

| Field | Visible | Read-only |
|-------|---------|-----------|
| `u_student` | True | True |

### 7.7 UP-05: Issue Date Mandatory on Issuance

**Condition:** `!isNewRecord()`

| Field | Visible | Read-only | Mandatory |
|-------|---------|-----------|-----------|
| `u_issue_date` | True | False | True |

### 7.8 UP-06: Damage Description Hidden When No Damage

**Condition:** `u_damage_reported == false`

| Field | Visible | Read-only |
|-------|---------|-----------|
| `u_damage_description` | False | True |

### 7.9 UP-07: Configuration Value Read-Only

**Condition:** `!isNewRecord()`

| Field | Visible | Read-only |
| ------- | --------- | ----------- |
| `u_parameter_key` | True | True |
| `u_parameter_value` | True | False |
| `u_data_type` | True | True |

### 7.10 UP-08: Approval Borrow Request Read-Only

**Condition:** `u_borrow_request != ''`

| Field | Visible | Read-only |
|-------|---------|-----------|
| `u_borrow_request` | True | True |

---

## 8. Step 7: Create All 10 UI Actions

### 8.1 Create UI Actions in Studio

Navigate to **Studio** → **Client** → **UI Action** → **New** for each action.

### 8.2 UI Action Inventory

| ID | Name | Table | Type | Action |
| ---- | ------ | ------- | ------ | -------- |
| UA-01 | `[LIB] Issue Book` | u_library_borrow_requests | List/Form | Set status = Issued, create issuance record |
| UA-02 | `[LIB] Process Return` | u_library_borrow_requests | List/Form | Create return record, set status = Returned |
| UA-03 | `[LIB] Cancel Request` | u_library_borrow_requests | Form | Set status = Cancelled, restore available copies |
| UA-04 | `[LIB] Approve Request` | u_library_borrow_requests | Form | Create approval record with Approved decision |
| UA-05 | `[LIB] Reject Request` | u_library_borrow_requests | Form | Create approval record with Rejected decision |
| UA-06 | `[LIB] Add to Catalog` | u_library_books | Form | Create catalog item from book record |
| UA-07 | `[LIB] Print Borrow Slip` | u_library_issuance_records | Form | Generate printable borrow slip |
| UA-08 | `[LIB] Send Reminder` | u_library_borrow_requests | Form | Manually trigger overdue reminder notification |
| UA-09 | `[LIB] Create Return from Issuance` | u_library_issuance_records | Form | Navigate to new return record pre-populated with issuance data |
| UA-10 | `[LIB] Export to CSV` | u_library_books | List | Export current filtered list to CSV |

### 8.3 UA-01: Issue Book

**Details:**

- **Name:** `[LIB] Issue Book`
- **Table:** `u_library_borrow_requests`
- **Type:** List Button, Form Button
- **Conditions:** `current.u_status == 'approved'`
- **Action:** Creates a new issuance record linked to the borrow request, sets borrow request status to `issued`, and calls `LibraryAvailabilityService.decrementAvailableCopies()`.
- **Script:** Delegates to `LibraryBorrowService` and `LibraryAvailabilityService`.

### 8.4 UA-02: Process Return

**Details:**

- **Name:** `[LIB] Process Return`
- **Table:** `u_library_borrow_requests`
- **Type:** List Button, Form Button
- **Conditions:** `current.u_status == 'issued'`
- **Action:** Creates a new return record linked to the borrow request, sets borrow request status to `returned`, and calls `LibraryAvailabilityService.incrementAvailableCopies()`.

### 8.5 UA-03: Cancel Request

**Details:**

- **Name:** `[LIB] Cancel Request`
- **Table:** `u_library_borrow_requests`
- **Type:** Form Button
- **Conditions:** `current.u_status == 'pending_approval'`
- **Action:** Sets status to `cancelled`, restores available copies via `LibraryAvailabilityService`.

### 8.6 UA-04 — UA-10

These UI Actions follow the same pattern. Each is a form or list button with specific conditions and scripts that delegate to the appropriate Script Include methods. Refer to the UI Actions Design document for full script details.

---

## 9. Step 8: Create All 12 Client Scripts

### 9.1 Create Client Scripts in Studio

Navigate to **Studio** → **Client** → **Client Script** → **New** for each script.

### 9.2 Client Script Inventory

| ID | Name | Table | Type | Field |
| ---- | ------ | ------- | ------ | ------- |
| CS-01 | `[LIB] Validate Pickup Date` | u_library_borrow_requests | onChange | u_requested_pickup_date |
| CS-02 | `[LIB] Notes Character Count` | u_library_borrow_requests | onChange | u_notes |
| CS-03 | `[LIB] Confirm Cancel` | u_library_borrow_requests | onSubmit | — |
| CS-04 | `[LIB] Calculate Expected Return Date` | u_library_issuance_records | onLoad | — |
| CS-05 | `[LIB] Auto-Populate Issue Date` | u_library_issuance_records | onLoad | — |
| CS-06 | `[LIB] Damage Description Required` | u_library_return_records | onChange | u_damage_reported |
| CS-07 | `[LIB] Auto-Populate Return Date` | u_library_return_records | onLoad | — |
| CS-08 | `[LIB] Confirm Submit` | u_library_borrow_requests | onSubmit | — |
| CS-09 | `[LIB] Warn Overdue Threshold` | u_library_borrow_requests | onLoad | — |
| CS-10 | `[LIB] Validate ISBN Format` | u_library_books | onChange | u_isbn |
| CS-11 | `[LIB] Confirm Delete Book` | u_library_books | onSubmit | — |
| CS-12 | `[LIB] Show Active Borrow Count` | u_library_students | onLoad | — |

### 9.3 CS-01: Validate Pickup Date

**Type:** onChange on `u_requested_pickup_date`

Validates that the pickup date is at least 1 day from today and not more than 30 days in the future. Displays inline error messages using `g_form.showFieldMsg()`.

### 9.4 CS-02: Notes Character Count

**Type:** onChange on `u_notes`

Displays a character counter showing the current count and remaining characters (max 500). Updates dynamically as the user types.

### 9.5 CS-03 through CS-12

Each Client Script follows the standard pattern for its type. Refer to the Client Scripts Design document (`docs/servicenow/ClientScripts.md`) for full implementation details of each script.

---

## 10. Step 9: Configure All 3 Flow Designer Flows

### 10.1 Enable Flow Designer

1. Navigate to **All** → **Process Automation** → **Flow Designer**.
2. Ensure the Flow Designer plugin is activated.

### 10.2 Flow Inventory

| ID | Name | Trigger | Purpose |
| ---- | ------ | --------- | --------- |
| FLOW-01 | `[LIB] Borrow Request Lifecycle` | Record Created (u_library_borrow_requests) | Main orchestration flow |
| FLOW-02 | `[LIB] Book Issuance Sub-Flow` | Called by Flow 1 | Issue book after approval |
| FLOW-03 | `[LIB] Overdue Management` | Scheduled (daily) | Detect and notify overdue books |

### 10.3 FLOW-01: Borrow Request Lifecycle

**Trigger:** Record Created → `u_library_borrow_requests` with condition `u_status == 'pending_approval'`

**Flow Steps:**

1. **Send Notification:** Send LIB-NOTIF-001 (submission confirmation) to the student.
2. **Send Notification:** Send LIB-NOTIF-010 (new request) to the librarian group.
3. **Create Approval:** Generate an approval task assigned to the `librarian_library` group.
4. **Wait for Decision:** Wait for approval/rejection with a 48-hour timeout.
5. **Approved Branch:** Set status to `approved`, record approver details, send LIB-NOTIF-002 to student, call Book Issuance Sub-Flow.
6. **Rejected Branch:** Set status to `rejected`, record rejection reason, restore available copies, send LIB-NOTIF-003 to student.
7. **Escalation Branch (48h):** Set status to `escalated`, reassign to `library_manager` group, send LIB-NOTIF-011 to manager.
8. **Manager Decision Wait:** 72-hour timeout for manager decision.
9. **Manager Approves:** Set status to `approved`, record override, proceed as approved.
10. **Manager Rejects:** Set status to `rejected`, record override reason, restore copies.
11. **Admin Follow-up (72h breach):** Create follow-up task for `library_admin` group, send LIB-NOTIF-013.

### 10.4 FLOW-02: Book Issuance Sub-Flow

**Trigger:** Called by FLOW-01

**Inputs:** `requestSysId` (sys_id of the borrow request), `approverSysId`

**Flow Steps:**

1. **Create Issuance Record:** Create record in `u_library_issuance_records` with borrow request, book, student, issued by, issue date, expected return date.
2. **Decrement Availability:** Call `LibraryAvailabilityService.decrementAvailableCopies()`.
3. **Update Student Borrow Count:** Increment `u_active_borrow_count` on the student record.
4. **Send Notification:** Send LIB-NOTIF-004 (book issued) to the student.
5. **Return:** Return control to parent flow.

### 10.5 FLOW-03: Overdue Management

**Trigger:** Schedule (daily at 6:00 AM)

**Flow Steps:**

1. **Query Issued Books:** Query `u_library_borrow_requests` where `u_status == 'issued'` and `u_overdue_flag == false`.
2. **Check Expected Return Date:** For each record, compare current date with expected return date from associated issuance record.
3. **Mark Overdue:** If past expected return date, set status to `overdue` and `u_overdue_flag` to `true`.
4. **Calculate Overdue Days:** Compute days past due.
5. **Determine Notification Threshold:** Based on `overdue_reminder_days` configuration parameter (1, 3, 7, 14 days).
6. **Send Notification:** Send the appropriate overdue notification (LIB-NOTIF-006 through LIB-NOTIF-009) based on the threshold.
7. **Log:** Write notification to `u_library_notification_log`.

---

## 11. Step 10: Configure All 13 Notification Records + Email Templates

### 11.1 Create Notification Records

Navigate to **System Notification** → **Email** → **Notifications** → **New** for each notification.

### 11.2 Notification Inventory

| ID | Name | Trigger | Recipient |
| ---- | ------ | --------- | ----------- |
| LIB-NOTIF-001 | `[LIB] Borrow Request Submitted` | Record Created (borrow_request) | Student (opened_by) |
| LIB-NOTIF-002 | `[LIB] Request Approved` | Flow: Approved branch | Student |
| LIB-NOTIF-003 | `[LIB] Request Rejected` | Flow: Rejected branch | Student |
| LIB-NOTIF-004 | `[LIB] Book Issued` | Record Updated (status → issued) | Student |
| LIB-NOTIF-005 | `[LIB] Book Returned` | Record Created (return_record) | Student |
| LIB-NOTIF-006 | `[LIB] Overdue Reminder — Day 1` | Scheduled Job | Student |
| LIB-NOTIF-007 | `[LIB] Overdue Reminder — Day 3` | Scheduled Job | Student |
| LIB-NOTIF-008 | `[LIB] Overdue Reminder — Day 7` | Scheduled Job | Student |
| LIB-NOTIF-009 | `[LIB] Overdue Reminder — Day 14` | Scheduled Job | Student |
| LIB-NOTIF-010 | `[LIB] New Pending Request` | Record Created (borrow_request) | Librarian Group |
| LIB-NOTIF-011 | `[LIB] Approval Escalated to Manager` | Flow: Escalation branch | Library Manager Group |
| LIB-NOTIF-012 | `[LIB] Damage Report Created` | Record Updated (damage_reported = true) | Library Manager |
| LIB-NOTIF-013 | `[LIB] Admin Follow-up Required` | Flow: 72h SLA breach | Admin Group |

### 11.3 Configure Email Templates

For each notification, create an email template:

1. Navigate to **System Notification** → **Email** → **Templates** → **New**.
2. Create a template with the appropriate subject and body HTML for each notification.
3. Link the template to the notification record.

### 11.4 Email Template Content

Each email template includes:

- Professional HTML formatting with university branding
- Dynamic fields from the GlideRecord context
- Clear call-to-action buttons or links
- Footer with contact information and auto-generated disclaimer

Refer to the Email Templates Design document (`docs/EmailTemplates.md`) for the full HTML content of each template.

### 11.5 Notification Configuration Parameters

Ensure the following configuration parameters exist in `u_library_configuration`:

| Key | Value | Data Type |
|-----|-------|-----------|
| `notifications_enabled` | `true` | Boolean |
| `email_notifications_enabled` | `true` | Boolean |

---

## 12. Step 11: Create All 4 Roles with ACLs

### 12.1 Create Roles

Navigate to **User Administration** → **Roles** → **New** for each role.

| Role Name | ID | Description |
| ----------- | ---- | ------------- |
| Student | `x_univ_library.student_library` | University student with a registered library profile |
| Librarian | `x_univ_library.librarian_library` | Library staff managing day-to-day operations |
| Library Manager | `x_univ_library.library_manager` | Senior staff overseeing all operations |
| Administrator | `x_univ_library.library_admin` | IT/system administrator with full access |

### 12.2 Create ACLs

For each table, create ACL records (read, write, create, delete) for each role. Navigate to **System Security** → **Access Controls** (ACLs) → **New**.

**ACL Type:** `record` (table-level) and `field` (field-level) as needed.

### 12.3 Permission Matrix Implementation

Implement the permission matrix using ACL scripts. Example ACL script pattern:

```javascript
// Example: Student read access on u_library_books
// Table: u_library_books, Operation: read, Role: student_library
(function evaluateRule() {
    if (gs.hasRole('x_univ_library.library_admin')) return true;
    if (gs.hasRole('x_univ_library.library_manager')) return true;
    if (gs.hasRole('x_univ_library.librarian_library')) return true;
    if (gs.hasRole('x_univ_library.student_library')) {
        return current.u_active == true;
    }
    return false;
})();
```

### 12.4 ACL Record Count

| Table | ACL Records | Total |
| ------- | ------------- | ------- |
| u_library_books | read (student), read (librarian), write (librarian), delete (manager), full (admin) | 5 |
| u_library_categories | read (all), write (manager), delete (manager), full (admin) | 4 |
| u_library_students | read own (student), read all (librarian), write (manager), full (admin) | 4 |
| u_library_librarians | read own (librarian), read (manager), full (admin) | 3 |
| u_library_borrow_requests | read own (student), create (student), read all (librarian), write (librarian), full (admin) | 5 |
| u_library_approvals | read (librarian), write (librarian), full (admin) | 3 |
| u_library_issuance_records | read own (student), read/write (librarian), full (admin) | 3 |
| u_library_return_records | read own (student), read/write (librarian), full (admin) | 3 |
| u_library_notification_log | read/write (admin) | 1 |
| u_library_configuration | read/write (admin) | 1 |
| u_library_audit_log | read (admin), write (nobody) | 2 |

Refer to the Roles and Permissions Design document for the complete ACL script implementations.

---

## 13. Step 12: Configure Service Portal with 8 Pages

### 13.1 Create Portal

1. Navigate to **Service Portal** → **Portals** → **New**.
2. Configure:

| Property | Value |
| ---------- | ------- |
| **Portal ID** | `x_univ_library_portal` |
| **Title** | University Library |
| **Base URL** | `/library` |
| **Theme** | SNow Seismic (customized) |
| **Logo** | Upload university library logo |
| **CSS** | Custom SCSS |

### 13.2 Portal Pages

Create the following pages using the Service Portal Designer:

| Page ID | URL | Name | Widgets Used |
| --------- | ----- | ------ | ------------ |
| PG-001 | `/library` | Library Home | Search, Featured Books, Quick Links |
| PG-002 | `/library/catalog` | Book Catalog | Catalog Grid, Category Filter, Search |
| PG-003 | `/library/book/{sys_id}` | Book Detail | Book Detail, Availability, Request Button |
| PG-004 | `/library/request` | Borrow Request Form | Request Form, Book Picker, Date Picker |
| PG-005 | `/library/my-requests` | My Requests | Request List, Status Badges |
| PG-006 | `/library/history` | My Borrowing History | History List, Filter Controls |
| PG-007 | `/library/operations` | Library Operations | Approvals List, Pending Issuances, Returns |
| PG-008 | `/library/profile` | My Profile | Profile Details, Borrow Limit, Active Requests |

### 13.3 Widget Implementations

Each page uses custom and standard widgets:

**Custom Widgets Developed:**

- `library-catalog-grid` — Displays books in a card grid layout with search and category filtering
- `library-book-detail` — Shows full book information with availability status
- `library-request-form` — Multi-step borrow request form with validation
- `library-request-list` — Filterable list of borrow requests with status badges
- `library-operations-panel` — Librarian operations dashboard for approvals, issuances, and returns
- `library-borrower-info` — Student profile card with borrow limit and active count

### 13.4 Page ACLs

Configure page access restrictions:

| Page | Allowed Roles |
| ------ | --------------- |
| PG-001 — Library Home | All authenticated users |
| PG-002 — Book Catalog | `student_library` |
| PG-003 — Book Detail | `student_library` |
| PG-004 — Borrow Request Form | `student_library` |
| PG-005 — My Requests | `student_library` |
| PG-006 — My Borrowing History | `student_library` |
| PG-007 — Library Operations | `librarian_library`, `library_manager` |
| PG-008 — My Profile | `student_library` |

---

## 14. Step 13: Create All 8 Reports with Scheduled Delivery

### 14.1 Create Reports

Navigate to **Reports** → **Create New** → **Report** for each report.

### 14.2 Report Inventory

| ID | Name | Type | Table |
| ---- | ------ | ------ | ------- |
| RPT-001 | Books Borrowed per Month | Bar Chart + Table | u_library_borrow_requests |
| RPT-002 | Most Borrowed Books | Horizontal Bar | u_library_borrow_requests |
| RPT-003 | Overdue Books | List | u_library_borrow_requests |
| RPT-004 | Student Activity | Summary Table | u_library_borrow_requests |
| RPT-005 | Inventory Status | Summary Table | u_library_books |
| RPT-006 | Notification Log Summary | Pie Chart | u_library_notification_log |
| RPT-007 | Audit Log Summary | List | u_library_audit_log |
| RPT-008 | Approval Processing Time | Bar Chart | u_library_approvals |

### 14.3 Configure Scheduled Delivery

For each report that requires scheduled delivery:

1. Open the report.
2. Click **Schedule**.
3. Configure:

| Report | Schedule | Recipients | Format |
| -------- | ---------- | ------------ | -------- |
| RPT-001 — Books Borrowed per Month | 1st of month at 8:00 AM | Library Manager | PDF |
| RPT-003 — Overdue Books | Daily at 7:00 AM | Librarian Group | CSV |
| RPT-004 — Student Activity | Weekly on Monday at 8:00 AM | Library Manager | PDF |
| RPT-005 — Inventory Status | 1st of quarter at 9:00 AM | Library Manager, Admin | PDF |
| RPT-006 — Notification Log Summary | Weekly on Sunday at 10:00 PM | Admin | CSV |
| RPT-007 — Audit Log Summary | Monthly on 1st at 11:00 PM | Admin | CSV |

---

## 15. Step 14: Configure 2 Dashboards with Performance Analytics Indicators

### 15.1 Enable Performance Analytics

1. Navigate to **Performance Analytics** → **Home**.
2. Ensure the Performance Analytics plugin is activated.

### 15.2 Create PA Indicators

Create the following Performance Analytics indicators:

| Indicator | Table | Metric | Aggregate | Schedule |
| ----------- | ------- | -------- | ----------- | ---------- |
| Active Borrow Requests | u_library_borrow_requests | Count where status = issued | Count | Daily |
| Overdue Books Count | u_library_borrow_requests | Count where status = overdue | Count | Daily |
| Pending Approvals | u_library_borrow_requests | Count where status = pending_approval | Count | Daily |
| Books Returned On Time | u_library_return_records | Count where condition = on_time | Count | Daily |
| Books Returned Overdue | u_library_return_records | Count where condition = overdue | Count | Daily |
| Average Approval Time | u_library_approvals | Average of time between creation and decision | Average | Daily |

### 15.3 Dashboard 1: Library Operations Dashboard

Create a dashboard with the following widgets:

| Widget | Type | Data Source |
| -------- | ------ | ------------- |
| Active Requests Count | Gauge/Number | PA Indicator: Active Borrow Requests |
| Overdue Books Count | Gauge/Number | PA Indicator: Overdue Books Count |
| Available Books Count | Gauge/Number | Aggregate from u_library_books |
| Pending Approvals | Gauge/Number | PA Indicator: Pending Approvals |
| Books Borrowed by Category | Horizontal Bar Chart | Report RPT-001 |
| Daily Borrow Volume | Line Chart (30 days) | Report RPT-001 (daily variant) |
| Recent Activity Feed | List (10 items) | Filtered from u_library_borrow_requests |

### 15.4 Dashboard 2: Student Self-Service Dashboard

Create a dashboard visible on the student portal:

| Widget | Type | Data Source |
| -------- | ------ | ------------- |
| My Active Requests | List | u_library_borrow_requests filtered by current user |
| My Borrowing History | List | u_library_borrow_requests filtered by current user |
| Currently Issued Books | List | u_library_issuance_records filtered by current student |
| My Profile Summary | Card | u_library_students record for current user |

---

## 16. Step 15: Set Up Scheduled Jobs

### 16.1 Create Scheduled Jobs

Navigate to **System Definition** → **Scheduled Jobs** → **New** for each job.

### 16.2 Scheduled Job Inventory

| ID | Name | Schedule | Script Include | Description |
| ---- | ------ | ---------- | --------------- | ------------- |
| SJ-01 | `[LIB] Overdue Detection` | Daily at 2:00 AM | LibraryAvailabilityService | Detect overdue books and update status |
| SJ-02 | `[LIB] Overdue Notification — Day 1` | Daily at 6:00 AM | LibraryNotificationHelper | Send first overdue reminder |
| SJ-03 | `[LIB] Overdue Notification — Day 3` | Daily at 6:00 AM | LibraryNotificationHelper | Send 3-day overdue reminder |
| SJ-04 | `[LIB] Overdue Notification — Day 7` | Daily at 6:00 AM | LibraryNotificationHelper | Send 7-day overdue reminder |
| SJ-05 | `[LIB] Overdue Notification — Day 14` | Daily at 6:00 AM | LibraryNotificationHelper | Send 14-day overdue reminder |
| SJ-06 | `[LIB] Report Delivery` | Per report schedule | — | Triggers report generation and email delivery |
| SJ-07 | `[LIB] Audit Log Cleanup` | Monthly on 1st at 1:00 AM | LibraryAuditLogger | Purge audit records older than 3 years |
| SJ-08 | `[LIB] Configuration Cache Refresh` | Weekly on Monday at 5:00 AM | LibraryConfigService | Refresh configuration parameter cache |

### 16.3 SJ-01: Overdue Detection

```text
Name: [LIB] Overdue Detection
Schedule: Daily at 2:00 AM
Condition: Runs on all nodes (single execution)
```

Queries all borrow requests with status `issued` and `u_overdue_flag == false`. For each record, checks if the expected return date (from the associated issuance record) has passed. If overdue, sets `u_status` to `overdue` and `u_overdue_flag` to `true`.

### 16.4 SJ-02 through SJ-05: Overdue Notifications

Each job queries borrow requests where status is `overdue` and calculates the number of days overdue. Based on the configuration parameter `overdue_reminder_days`, the appropriate notification is sent. Only overdue records matching the specific day threshold are processed to avoid duplicate notifications.

### 16.5 SJ-06: Audit Log Cleanup

```text`nName: [LIB] Audit Log Cleanup
Schedule: Monthly on 1st at 1:00 AM
Retention: 3 years (1095 days)
```

Calls `LibraryAuditLogger.purgeOldRecords(1095)` to remove audit log entries older than the retention period. All deletions are logged to the system log for compliance purposes.

---

## 17. Step 16: Create Service Catalog Item

### 17.1 Create Catalog Item

1. Navigate to **Service Catalog** → **Catalog Definitions** → **Maintain Items** → **New**.
2. Select **Record Producer** type.

### 17.2 Catalog Item Properties

| Property | Value |
| ---------- | ------- |
| **Name** | Borrow a Book |
| **Catalog** | University Library Catalog |
| **Category** | Book Services |
| **Short Description** | Request to borrow a book from the university library |
| **Table** | `u_library_borrow_requests` |
| **Quantity** | 1 (single request) |

### 17.3 Record Producer Configuration

**Variables:**

| Variable | Type | Mandatory | Reference |
| ---------- | ------ | ----------- | ----------- |
| Book | Reference | Yes | u_library_books (with qualifier: active=true, available_copies>0) |
| Student | Reference (read-only) | Yes | u_library_students (auto-populated from current user) |
| Requested Pickup Date | Date | No | — |
| Notes | Multi-line Text | No | — |

**Variable Scripts:**

On the Book variable, set the reference qualifier using the following script to exclude books the student already has an active request for:

```javascript
(function() {
    var studentGr = new GlideRecord('u_library_students');
    studentGr.addQuery('u_user', gs.getUserID());
    studentGr.query();
    if (studentGr.next()) {
        var studentSysId = studentGr.getUniqueValue();
        var activeRequests = [];
        var reqGr = new GlideRecord('u_library_borrow_requests');
        reqGr.addQuery('u_student', studentSysId);
        reqGr.addQuery('u_status', 'IN', 'pending_approval,approved,issued');
        reqGr.query();
        while (reqGr.next()) {
            activeRequests.push(reqGr.getValue('u_book'));
        }
        if (activeRequests.length > 0) {
            return 'u_active=true^u_available_copies>0^sys_idNOT IN' + activeRequests.join(',');
        }
    }
    return 'u_active=true^u_available_copies>0';
})();
```

**On Submit Script:**

```javascript
(function() {
    current.u_status = 'pending_approval';
    current.u_opened_by = gs.getUserID();
    current.u_submitted_at = new GlideDateTime();
})();
```

### 17.4 Catalog Item UI

Configure the catalog item with:

- A descriptive image of the library
- Availability information displayed as a banner
- Clear instructions for the student

---

## 18. Step 17: Import Sample Data

### 18.1 Prepare CSV Import Files

Place the CSV import files in a location accessible from the instance:

- `books.csv`
- `categories.csv`
- `students.csv`
- `librarians.csv`
- `borrow_requests.csv`

### 18.2 Create Import Set Tables

1. Navigate to **System Import Sets** → **Import Set Tables** → **New**.
2. Create a staging table for each CSV file.
3. Map each staging table field to the target application table field.

### 18.3 Run Import

1. Navigate to **System Import Sets** → **Import Sets** → **New**.
2. Upload each CSV file as a new import set.
3. Run the import transform map.
4. Verify imported records in each target table.

### 18.4 Seed Data Summary

| Table | Records | Notes |
| ------- | --------- | ------- |
| u_library_categories | 8 | Fiction, Non-Fiction, Science, etc. |
| u_library_books | 15 | Sample books across all categories |
| u_library_students | 5 | Active student profiles |
| u_library_librarians | 3 | Staff profiles |
| u_library_borrow_requests | 8 | Mix of statuses for testing |

---

## 19. Step 18: Run Test Suite

### 19.1 Test Execution

1. Navigate to **Automated Test Framework** → **Test Suites**.
2. Open the `[LIB] Smart Library Workflow — Full Regression` test suite.
3. Click **Execute**.
4. Select the development instance as the target.
5. Review results after execution completes.

### 19.2 Test Suite Contents

| Module | Test Cases | Focus |
| -------- | ----------- | ------- |
| Books CRUD | 12 | Create, read, update, delete, search, import |
| Categories | 5 | CRUD, hierarchy, assignments |
| Borrow Requests | 11 | Submission, validation, cancellation, history |
| Approval Workflow | 10 | Approve, reject, escalate, override |
| Issuance & Returns | 10 | Issue, return, damage, overdue |
| Notifications | 5 | Trigger conditions, delivery, logging |
| Portal Pages | 5 | Navigation, data display, form submission |
| Security & ACLs | 6 | Role-based access, field-level security |

### 19.3 Manual Test Cases

Execute manual test cases for scenarios that cannot be automated:

- Cross-browser compatibility (Chrome, Firefox, Edge)
- Mobile responsiveness of portal pages
- Email delivery and formatting
- Performance under concurrent user load

### 19.4 Test Results

All 64 automated test cases passed during the final test execution. See the Test Cases document for detailed results.

---

## 20. Step 19: Configure SLA Definitions

### 20.1 Create SLA Definitions

1. Navigate to **SLA** → **SLA Definitions** → **New**.

### 20.2 SLA Inventory

| SLA | Table | Condition | Duration | Breach Action |
| ----- | ------- | ----------- | ---------- | --------------- |
| `[LIB] Approval SLA — 48 Hours` | u_library_borrow_requests | Status = pending_approval | 48 hours (business hours) | Escalate to Library Manager |
| `[LIB] Manager Escalation SLA — 72 Hours` | u_library_borrow_requests | Status = escalated | 72 hours (business hours) | Create admin follow-up task |
| `[LIB] Book Loan Period` | u_library_issuance_records | Status = issued (via parent) | 14 days (calendar days) | Set overdue flag |

### 20.3 SLA 1: Approval SLA — 48 Hours

| Property | Value |
| ---------- | ------- |
| **Name** | `[LIB] Approval SLA — 48 Hours` |
| **Table** | `u_library_borrow_requests` |
| **Condition** | `u_status == 'pending_approval'` |
| **Duration** | 48 hours |
| **Business Hours** | University library hours: Mon-Fri 8:00 AM — 8:00 PM |
| **Breach Action** | `current.u_status = 'escalated'`; write to escalation log |
| **On Start** | Create SLA timer, start tracking |
| **On Breach** | Trigger escalation flow; send LIB-NOTIF-011 |
| **On Complete** | Stop SLA timer, record completion time |

### 20.4 SLA 2: Manager Escalation SLA — 72 Hours

| Property | Value |
| ---------- | ------- |
| **Name** | `[LIB] Manager Escalation SLA — 72 Hours` |
| **Table** | `u_library_borrow_requests` |
| **Condition** | `u_status == 'escalated'` |
| **Duration** | 72 hours |
| **Business Hours** | Same as above |
| **Breach Action** | Create follow-up task for `library_admin` group |
| **On Breach** | Send LIB-NOTIF-013 to admin group |

### 20.5 SLA 3: Book Loan Period

| Property | Value |
| ---------- | ------- |
| **Name** | `[LIB] Book Loan Period` |
| **Table** | `u_library_issuance_records` |
| **Condition** | Always (when record created) |
| **Duration** | 14 calendar days (configurable via `default_loan_period_days`) |
| **On Start** | Set `u_expected_return_date` = issue date + loan period |
| **On Breach** | Set parent borrow request `u_overdue_flag = true` and `u_status = 'overdue'` |

---

## 21. Step 20: UAT Sign-Off and Production Deployment

### 21.1 UAT Execution

1. Schedule UAT sessions with library stakeholders.
2. Provide test scripts covering all user stories.
3. Library staff execute UAT scenarios:
   - Student: Submit, track, cancel requests
   - Librarian: Approve requests, issue books, process returns
   - Manager: Override approvals, view reports and dashboards
   - Admin: Configure parameters, manage roles, review audit logs
4. Collect feedback and address issues.
5. Obtain formal sign-off from all stakeholders.

### 21.2 UAT Sign-Off Criteria

| Criterion | Status |
| ----------- | -------- |
| All 20 user stories tested and accepted | Passed |
| All 10 student scenarios working | Passed |
| All 5 librarian scenarios working | Passed |
| All 3 manager scenarios working | Passed |
| All 2 admin scenarios working | Passed |
| All 8 portal pages load correctly | Passed |
| All 8 reports generate correct data | Passed |
| All 13 notifications delivered correctly | Passed |
| All 3 SLA definitions trigger correctly | Passed |

### 21.3 Production Deployment Steps

1. Generate update set from development instance.
2. Retrieve update set XML from **System Update Sets** → **Update Sets** → **Export to XML**.
3. Import update set into production instance.
4. Preview update set to verify no conflicts.
5. Commit update set.
6. Perform post-deployment verification.
7. Activate scheduled jobs.
8. Enable notifications.
9. Conduct smoke test on production.

### 21.4 Post-Deployment Verification

Verify the following after production deployment:

- All 11 tables exist with correct fields
- All 5 Script Includes are accessible
- All 15 Business Rules trigger correctly
- All 8 UI Policies apply as expected
- All 10 UI Buttons visible on forms
- All 12 Client Scripts execute without errors
- All 3 Flow Designer flows activate on trigger
- All 13 notifications are sent on appropriate events
- All 4 roles grant correct permissions
- All 8 portal pages render properly
- All 8 reports generate and deliver on schedule
- Both dashboards display correct data
- All 3 SLA definitions start tracking
- Service Catalog item is visible and functional
- Scheduled jobs run on their defined schedules

---

*References: [TechnicalBlueprint.md](../TechnicalBlueprint.md) | [DataDictionary.md](../database/DataDictionary.md) | [Tables.md](../Tables.md)*
*See also: [DeploymentGuide.md](DeploymentGuide.md) | [TestingGuide.md](../TestingGuide.md) | [Troubleshooting.md](Troubleshooting.md)*
