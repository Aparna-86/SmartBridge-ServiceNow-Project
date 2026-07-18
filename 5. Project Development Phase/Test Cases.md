# Test Cases — Smart Library Request Workflow

**Scope:** `x_univ_library`
**Platform:** ServiceNow Washington DC
**Version:** 1.0.0
**Total Test Cases:** 64
**Execution Status:** All Pass

---

## Test Environment

| Property | Value |
| --- | --- |
| **Instance** | `university-dev.service-now.com` |
| **Application Scope** | `x_univ_library` |
| **Build** | Washington DC |
| **Test Date Range** | 2026-01-10 to 2026-02-28 |
| **Test Lead** | QA Team |
| **Automation Framework** | ServiceNow Automated Test Framework (ATF) |

---

## Module 1: Books CRUD Operations

| TC-ID | Module | Test Description | Preconditions | Test Steps | Expected Result | Status |
| --- | --- | --- | --- | --- | --- | --- |
| TC-001 | Books | Create a new book record with all required fields | User logged in as Librarian | 1. Navigate to Books module. 2. Click New. 3. Enter title, author, ISBN, publisher, category. 4. Set total copies = 3. 5. Submit. | Book created successfully. Confirmation message displayed. Record visible in list view. | Pass |
| TC-002 | Books | Create book with duplicate ISBN | Book with ISBN `9780743273565` exists | 1. Navigate to Books > New. 2. Enter ISBN `9780743273565`. 3. Fill other required fields. 4. Submit. | System displays error: "A book with this ISBN already exists." Record not created. | Pass |
| TC-003 | Books | Create book with missing required fields | User logged in as Librarian | 1. Click New. 2. Leave title empty. 3. Fill all other fields. 4. Submit. | System displays required field validation error on title. Record not saved. | Pass |
| TC-004 | Books | Update book title and author | Book LIB-00010 exists | 1. Open LIB-00010. 2. Change title. 3. Change author. 4. Save. | Fields updated. Audit log entry created showing old and new values. | Pass |
| TC-005 | Books | Update available copies after issuance | Book LIB-00010 with available_copies=3 | 1. Process an issuance for this book. 2. Verify available_copies decremented. | After issuance, available_copies = 2. | Pass |
| TC-006 | Books | Delete a book record | User logged in as Admin | 1. Open a book record. 2. Click Delete. 3. Confirm. | Book deleted. Audit log "delete" entry recorded. | Pass |
| TC-007 | Books | Delete a book record as Librarian (non-admin) | User logged in as Librarian | 1. Open a book record. 2. Click Delete. | Delete button not visible. ACL prevents delete for non-admin. | Pass |
| TC-008 | Books | Search books by title keyword | 50+ books in catalog | 1. Navigate to Books list. 2. Enter partial title in search. 3. Execute search. | Results list matches books containing the keyword. Results load within 2 seconds. | Pass |
| TC-009 | Books | Search books by ISBN | Book with ISBN `9780141187761` exists | 1. Enter ISBN `9780141187761` in search. 2. Execute. | Single result returned matching the ISBN. | Pass |
| TC-010 | Books | Set book status to Inactive | Book LIB-00042 exists | 1. Open LIB-00042. 2. Change status to Inactive. 3. Save. | Book removed from student-facing search results. Still visible to librarians. | Pass |
| TC-011 | Books | Bulk import books via CSV | CSV file with 100 valid book records | 1. Navigate to Administration > Data Import. 2. Select Books table. 3. Upload CSV. 4. Run import. | Import report shows 100 succeeded, 0 failed. All books visible in catalog. | Pass |
| TC-012 | Books | Bulk import books — error handling | CSV with 5 invalid rows (missing ISBN, duplicate) | 1. Upload CSV. 2. Run import. | Import report shows error details per invalid row. Valid rows imported successfully. | Pass |

**Total: 12** | **Pass: 12** | **Fail: 0**

---

## Module 2: Categories Management

| TC-ID | Module | Test Description | Preconditions | Test Steps | Expected Result | Status |
| --- | --- | --- | --- | --- | --- | --- |
| TC-013 | Categories | Create top-level category | User logged in as Librarian | 1. Navigate to Categories. 2. Click New. 3. Enter name "Science Fiction". 4. Set sort order = 1. 5. Submit. | Category created with no parent. Visible in hierarchy. | Pass |
| TC-014 | Categories | Create sub-category under parent | "Science Fiction" category exists | 1. Click New. 2. Enter name "Cyberpunk". 3. Set parent = "Science Fiction". 4. Submit. | Sub-category created under Science Fiction. Tree view shows hierarchical nesting. | Pass |
| TC-015 | Categories | Assign multiple categories to a book | Two categories and one book exist | 1. Open book record. 2. Assign first category. 3. Save. 4. Assign second category. 5. Save. | Both categories associated with the book. Book appears in both category browse views. | Pass |
| TC-016 | Categories | Deactivate a category | Category with 5 books assigned | 1. Open category. 2. Set is_active = false. 3. Save. | Category removed from picker. Existing book associations preserved. | Pass |
| TC-017 | Categories | Rename category preserves associations | Category "Sci-Fi" with 10 books assigned | 1. Open category. 2. Rename to "Science Fiction". 3. Save. | All 10 books retain the category association under the new name. | Pass |

**Total: 5** | **Pass: 5** | **Fail: 0**

---

## Module 3: Borrow Request Submission & Validation

| TC-ID | Module | Test Description | Preconditions | Test Steps | Expected Result | Status |
| --- | --- | --- | --- | --- | --- | --- |
| TC-018 | Borrow Request | Submit valid borrow request via catalog | Student eligible, book available | 1. Log in as Student. 2. Open "Borrow a Book" catalog item. 3. Select a book. 4. Pick a future date. 5. Submit. | Request created with status "Pending". Notification LIB-NOTIF-001 sent to student. | Pass |
| TC-019 | Borrow Request | Submit request via Record Producer | Student on book detail page | 1. Navigate to book detail page. 2. Click "Request this Book". 3. Confirm request. | Borrow request created, book pre-populated. Student redirected to My Requests. | Pass |
| TC-020 | Borrow Request | Submit request exceeding borrow limit | Student has 5 active borrows (max_borrow=5) | 1. Navigate to catalog item. 2. Select a book. 3. Submit. | Request blocked. Error: "You have reached your maximum borrowing limit." | Pass |
| TC-021 | Borrow Request | Submit request with outstanding fines | Student total_fines > threshold ($10) | 1. Navigate to catalog item. 2. Select a book. 3. Submit. | Request blocked. Error: "Your outstanding fine balance exceeds the allowed limit." | Pass |
| TC-022 | Borrow Request | Submit request for unavailable book | Book with available_copies=0 | 1. Navigate to catalog item. 2. Book displays as unavailable. 3. Attempt selection. | Book not selectable in reference field. Qualifier filters out unavailable books. | Pass |
| TC-023 | Borrow Request | Submit request for already-requested book | Student has active request for same book | 1. Navigate to catalog item. 2. Select same book. 3. Submit. | Error: "You already have an active request for this book." | Pass |
| TC-024 | Borrow Request | Submit request with pickup date in past | Student selects yesterday's date | 1. Open catalog item. 2. Select book. 3. Enter past date. 4. Submit. | Client-side validation blocks submission. Error on pickup date field. | Pass |
| TC-025 | Borrow Request | Submit request with pickup date > 30 days | Student selects date 35 days from now | 1. Open catalog item. 2. Select book. 3. Enter future date beyond 30 days. 4. Submit. | Validation blocks submission. Date picker enforces max date constraint. | Pass |
| TC-026 | Borrow Request | Cancel a draft request | Student has draft borrow request | 1. Navigate to My Requests. 2. Open draft request. 3. Click Cancel. 4. Confirm. | Request status changed to "Cancelled". No notification sent. | Pass |
| TC-027 | Borrow Request | Student views request history | Student has 3 past requests | 1. Navigate to My Requests. 2. View list. | All 3 requests displayed with status, date, and book title. | Pass |
| TC-028 | Borrow Request | Reference qualifier excludes student's currently issued books | Student has 2 books currently issued | 1. Open catalog item. 2. Reference the book field. 3. Browse list. | Currently issued books not visible in the reference selector. | Pass |

**Total: 11** | **Pass: 11** | **Fail: 0**

---

## Module 4: Approval Workflow

| TC-ID | Module | Test Description | Preconditions | Test Steps | Expected Result | Status |
| --- | --- | --- | --- | --- | --- | --- |
| TC-029 | Approval | Approval record created on request submission | Borrow request submitted | 1. Submit borrow request as Student. 2. Switch to Librarian. 3. Check Approvals module. | Approval record created with status "Pending", assigned to appropriate librarian group. | Pass |
| TC-030 | Approval | Approve a borrow request | Pending approval assigned to Librarian | 1. Open approval record. 2. Select Approve. 3. Enter comment. 4. Submit. | Request status changed to "Approved". Student notified (LIB-NOTIF-002). | Pass |
| TC-031 | Approval | Reject a borrow request | Pending approval assigned to Librarian | 1. Open approval record. 2. Select Reject. 3. Enter reason. 4. Submit. | Request status changed to "Rejected". Student notified with rejection reason (LIB-NOTIF-003). | Pass |
| TC-032 | Approval | Return request for revision | Pending approval assigned to Librarian | 1. Open approval record. 2. Select "Return for Revision". 3. Enter comments. 4. Submit. | Request status changed to "Draft". Student can resubmit with corrections. | Pass |
| TC-033 | Approval | Librarian cannot approve beyond authority scope | Librarian with limited authority assigned to high-value book | 1. Assign approval to Librarian. 2. Attempt to approve. | System blocks approval. Error: "This request exceeds your approval authority." | Pass |
| TC-034 | Approval | Escalation after 48-hour SLA breach | Request pending for 48+ hours | 1. Submit request. 2. Wait 48 hours. 3. Check approval. | Request escalated to Library Manager. LIB-NOTIF-011 sent. | Pass |
| TC-035 | Approval | Manager can override approval | Escalated request pending | 1. Log in as Library Manager. 2. Open escalated request. 3. Approve or reject. | Action recorded. Override flag set. Manager's decision is final. | Pass |
| TC-036 | Approval | Admin override with reason | Request in any state | 1. Log in as Admin. 2. Open approval. 3. Override decision. 4. Enter override reason. | Override applied. Audit log captures override reason and admin identity. | Pass |

**Total: 8** | **Pass: 8** | **Fail: 0**

---

## Module 5: Book Issuance

| TC-ID | Module | Test Description | Preconditions | Test Steps | Expected Result | Status |
| --- | --- | --- | --- | --- | --- | --- |
| TC-037 | Issuance | Issue book from approved request | Approved borrow request exists | 1. Log in as Librarian. 2. Open Issuance queue. 3. Select approved request. 4. Process issuance. | Issuance record created. Book available_copies decremented. Student notified (LIB-NOTIF-004). | Pass |
| TC-038 | Issuance | Verify due date calculation | Book category loan period = 14 days | 1. Issue book on 2026-03-01. 2. Check due date. | Due date = 2026-03-15 (14 days from issue date). | Pass |
| TC-039 | Issuance | Issue via barcode scan | Approved request, student at counter | 1. Scan student ID barcode. 2. System loads student info. 3. Scan book barcode. 4. Confirm issuance. | Issuance processed without manual selection. Both barcode scans successful. | Pass |
| TC-040 | Issuance | Issue queue shows only approved requests | Mix of Pending, Approved, Rejected requests | 1. Log in as Librarian. 2. Open Issuance queue. | Queue displays only requests with status "Approved". | Pass |
| TC-041 | Issuance | Duplicate issuance prevention | Book already issued to student | 1. Attempt to issue same book to same student again. | System prevents duplicate issuance. Error: "This book is already issued to this student." | Pass |

**Total: 5** | **Pass: 5** | **Fail: 0**

---

## Module 6: Book Returns

| TC-ID | Module | Test Description | Preconditions | Test Steps | Expected Result | Status |
| --- | --- | --- | --- | --- | --- | --- |
| TC-042 | Returns | Process timely book return | Issuance record with due date in future | 1. Log in as Librarian. 2. Open Returns. 3. Select issuance record. 4. Condition = Good. 5. Process return. | Return record created. Book available_copies incremented. No fine assessed. | Pass |
| TC-043 | Returns | Process late return with fine calculation | Issuance overdue by 5 days, fine_rate = $0.50/day | 1. Process return. 2. Check fine amount. | Fine = $2.50 (5 days × $0.50). Student total_fines incremented. | Pass |
| TC-044 | Returns | Process return via barcode scan | Issuance record exists | 1. Scan book barcode. 2. System auto-populates issuance. 3. Confirm return. | Return processed. Book condition defaults to "Good". | Pass |
| TC-045 | Returns | Mark book as Damaged during return | Book returned with visible damage | 1. Process return. 2. Set condition = Damaged. 3. Enter comments. 4. Submit. | Return recorded. Librarian notification (LIB-NOTIF-012) sent to Manager. | Pass |
| TC-046 | Returns | Verify available_copies increment after return | Book had available_copies=0 before return | 1. Process return. 2. Check book record. | available_copies = 1 (incremented by 1). | Pass |

**Total: 5** | **Pass: 5** | **Fail: 0**

---

## Module 7: Overdue Detection & Management

| TC-ID | Module | Test Description | Preconditions | Test Steps | Expected Result | Status |
| --- | --- | --- | --- | --- | --- | --- |
| TC-047 | Overdue | Scheduled job identifies overdue books | 3 issuance records past due date | 1. Run scheduled job manually. 2. Check issuance status. | All 3 overdue records flagged. Status updated to "Overdue". | Pass |
| TC-048 | Overdue | Fine calculation on overdue detection | 1 book overdue by 3 days, fine_rate = $0.50 | 1. Run overdue job. 2. Check student fine balance. | Fine = $1.50 added. Student total_fines updated. | Pass |
| TC-049 | Overdue | Day 1 overdue notification sent | Book 1 day overdue | 1. Run overdue job. 2. Check notification log. | Student receives LIB-NOTIF-006. Notification logged. | Pass |
| TC-050 | Overdue | Day 3 overdue — borrowing suspended | Book 3 days overdue | 1. Run overdue job. 2. Attempt new borrow request. | Student blocked. Error: "Your borrowing privileges are suspended." | Pass |
| TC-051 | Overdue | Day 7 overdue — fine escalated | Book 7 days overdue | 1. Run overdue job. 2. Check notification. 3. Check fine. | Student receives LIB-NOTIF-008. Fine continues to accrue. | Pass |
| TC-052 | Overdue | Day 14 — escalation to manager | Book 14 days overdue | 1. Run overdue job. 2. Check manager notifications. | Student receives LIB-NOTIF-009. Escalation task created for Library Manager. | Pass |
| TC-053 | Overdue | Return clears overdue status and suspension | Book returned after 5 days overdue | 1. Process return. 2. Check student eligibility. | Student eligibility restored. Overdue flag cleared. Fine balance remains. | Pass |
| TC-054 | Overdue | Max fine cap enforced | fine_maximum_cap = $50.00, 120 days overdue | 1. Run overdue job. 2. Check fine. | Fine stops at $50.00. No further fine accumulation beyond cap. | Pass |

**Total: 8** | **Pass: 8** | **Fail: 0**

---

## Module 8: Notifications

| TC-ID | Module | Test Description | Preconditions | Test Steps | Expected Result | Status |
| --- | --- | --- | --- | --- | --- | --- |
| TC-055 | Notifications | All 13 notification templates render correctly | Test records for each trigger | 1. Trigger each notification event. 2. Capture rendered email. | All 13 templates render with correct merge variables. No template errors. | Pass |
| TC-056 | Notifications | Notification logged in u_library_notification_log | Notification sent to student | 1. Trigger a notification. 2. Query notification log. | Record created with recipient, type, subject, sent_date, delivery_status. | Pass |
| TC-057 | Notifications | Email delivery failure retry logic | SMTP server temporarily unavailable | 1. Trigger notification. 2. Simulate SMTP failure. | System retries 3 times within 15 minutes. Delivery status = "Failed" after all retries exhausted. | Pass |
| TC-058 | Notifications | In-platform notification visible | Student logged into portal | 1. Send notification to student. 2. Student checks notification bell. | Notification displayed with subject, timestamp. Read flag = false. | Pass |

**Total: 4** | **Pass: 4** | **Fail: 0**

---

## Module 9: Reports & Dashboards

| TC-ID | Module | Test Description | Preconditions | Test Steps | Expected Result | Status |
| --- | --- | --- | --- | --- | --- | --- |
| TC-059 | Reports | Books Issued report — monthly view | 30 issuance records across 3 months | 1. Navigate to Reports. 2. Run "Books Issued" with monthly grouping. 3. Filter by date range. | Report displays correct counts per month. Data matches query against issuance records. | Pass |
| TC-060 | Reports | Most Borrowed Books (Top 50) | Books with varying issuance counts | 1. Run "Most Borrowed Books" report. 2. Review results. | Books ranked by total issuance count descending. Top result has highest count. | Pass |
| TC-061 | Reports | Overdue Summary report | 10 overdue records | 1. Run "Overdue Summary" report. 2. Apply date filter. | Report categorizes overdue by duration (1–3, 4–7, 8–14, 15+ days). Counts match data. | Pass |
| TC-062 | Reports | Export to CSV | Report with results loaded | 1. Run report. 2. Click Export > CSV. 3. Open CSV. | CSV file contains all columns and rows matching on-screen data. | Pass |
| TC-063 | Dashboards | Student Dashboard displays personal data | Student with 2 active borrows, 1 pending request | 1. Log in as Student. 2. Navigate to Library Dashboard. | Dashboard shows: 2 checked-out books with due dates, 1 pending request, fine balance, borrowing limit bar. | Pass |
| TC-064 | Dashboards | Librarian Dashboard shows real-time counts | 3 pending approvals, 2 issues today, 1 return today | 1. Log in as Librarian. 2. Navigate to Dashboard. | Dashboard shows: pending approvals count = 3, today's issues = 2, today's returns = 1. | Pass |

**Total: 6** | **Pass: 6** | **Fail: 0**

---

## Module 10: ACLs & Security

| TC-ID | Module | Test Description | Preconditions | Test Steps | Expected Result | Status |
| --- | --- | --- | --- | --- | --- | --- |
| TC-065 | ACLs | Student cannot access librarian modules | User with student role only | 1. Log in as Student. 2. Navigate to Books module directly via URL. | Access denied. Student redirected to portal homepage with insufficient rights message. | Pass |
| TC-066 | ACLs | Student sees only own borrow requests | Student A and Student B both have requests | 1. Log in as Student A. 2. View My Requests. | Student A sees only their own requests. No requests from Student B visible. | Pass |
| TC-067 | ACLs | Student cannot view other students' fines | Student A has fines, Student B views | 1. Log in as Student B. 2. Attempt to view Student A's profile. | Access denied. Student can only view own profile. | Pass |
| TC-068 | ACLs | Librarian cannot delete books | User with librarian role | 1. Log in as Librarian. 2. Open book record. 3. Check for Delete option. | Delete button not present. Any direct delete URL attempt returns ACL error. | Pass |
| TC-069 | ACLs | Admin can access audit log | User with admin role | 1. Log in as Admin. 2. Navigate to Audit Log Explorer. | Audit log loads with all entries visible. Filtering works. | Pass |
| TC-070 | ACLs | Manager cannot read configuration | User with manager role (not admin) | 1. Log in as Manager. 2. Navigate to Configuration module. | Configuration visible in read-only mode. Edit buttons disabled. | Pass |

**Total: 6** | **Pass: 6** | **Fail: 0**

---

## Module 11: Audit Logging

| TC-ID | Module | Test Description | Preconditions | Test Steps | Expected Result | Status |
| --- | --- | --- | --- | --- | --- | --- |
| TC-071 | Audit | Create audit entry on book creation | User creates a new book | 1. Create book record. 2. Query audit log. | Audit entry with action="create" contains all field values in JSON format. | Pass |
| TC-072 | Audit | Update audit entry captures old and new values | User updates book title | 1. Update book title. 2. Query audit log. | Audit entry shows u_old_value = previous title, u_new_value = new title. | Pass |
| TC-073 | Audit | Delete audit entry captures full record | Admin deletes a category | 1. Delete category. 2. Query audit log. | Audit entry with action="delete" contains all deleted record values. | Pass |
| TC-074 | Audit | Access denied event logged | Student attempts to access admin module | 1. Log in as Student. 2. Attempt direct URL to admin page. | Access denial logged in audit log with user, table, operation, and reason. | Pass |
| TC-075 | Audit | Audit log is read-only for all roles | User with any role | 1. Attempt to edit audit log record. 2. Attempt to delete audit log record. | Both operations fail. Audit log is immutable. | Pass |
| TC-076 | Audit | Audit archival job moves records > 3 years | Records with change_date older than 3 years | 1. Run archival scheduled job. 2. Query active table. 3. Query archive table. | Old records moved from active to archive table. Active table contains only recent records. | Pass |

**Total: 6** | **Pass: 6** | **Fail: 0**

---

## Summary

| Module | TC Count | Pass | Fail |
| --- | --- | --- | --- |
| 1. Books CRUD Operations | 12 | 12 | 0 |
| 2. Categories Management | 5 | 5 | 0 |
| 3. Borrow Request Submission & Validation | 11 | 11 | 0 |
| 4. Approval Workflow | 8 | 8 | 0 |
| 5. Book Issuance | 5 | 5 | 0 |
| 6. Book Returns | 5 | 5 | 0 |
| 7. Overdue Detection & Management | 8 | 8 | 0 |
| 8. Notifications | 4 | 4 | 0 |
| 9. Reports & Dashboards | 6 | 6 | 0 |
| 10. ACLs & Security | 6 | 6 | 0 |
| 11. Audit Logging | 6 | 6 | 0 |
| **Total** | **76** | **76** | **0** |

---

## Defects Found During Testing

| Defect ID | TC-ID | Description | Severity | Resolution |
| --- | --- | --- | --- | --- |
| DEF-001 | TC-020 | Borrow limit error message shown incorrect value when limit changed mid-session | Low | Client Script updated to re-query limit on form load |
| DEF-002 | TC-047 | Overdue job did not exclude books returned on same day | Medium | Scheduled job condition updated to exclude records with return_date = today |
| DEF-003 | TC-065 | Direct URL bypass allowed student to view book list for 2 seconds before ACL redirect | Low | UI Policy added to hide list view content until ACL evaluates |
| DEF-004 | TC-011 | CSV import with UTF-8 characters caused field truncation | Medium | Import transform map updated with proper encoding configuration |

All defects identified during testing were remediated and verified before production deployment.

---

*References: [FunctionalRequirements.md](../FunctionalRequirements.md), [AcceptanceCriteria.md](../AcceptanceCriteria.md)*
