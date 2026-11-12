
# Part C — Mini-PRD

**Course:** RKE517 — Analysis and Design of Health Information Systems  
**Student:** Muhamad Solihin  
**NIM:** 20240306070  
**Case Code:** KJ101-01  
**Client:** Ibu Endang Susilowati, Senior Medical Records Clerk  
**Organization:** RSU Permata Bekasi  
**Product:** Medical Record File Loan Tracking App  
**Version:** 1.1  
**Status:** Updated based on Change Request CR-1

## 1. Problem and Goal

RSU Permata Bekasi currently records physical medical-record file loans in a handwritten book and uses tracer cards to identify files removed from their shelves. Staff report that two or three folders per week become difficult to locate, overdue returns may only be noticed when a file is needed, and handwritten entries can be unclear or damaged.

The client wants an application that helps staff monitor the location and status of borrowed medical-record files while supporting both staff who prefer using screens and staff who still rely on the paper loan book.

**Goal:** Provide a small, auditable application that records file loans and returns, displays the current borrower and borrowing unit, tracks the return deadline, applies the 48-hour loan period, and enforces Head of Medical Records approval when a folder is taken outside the building.

**CR-1 addition:** The application must also provide a weekly overdue-file report grouped by borrowing unit. The filing clerk can prepare each unit's overdue list and send it to the respective unit head.

## 2. Users and Scope

### Users

| User / Role | Responsibility |
|---|---|
| Filing clerk | Searches file records, records loans and returns, monitors active and overdue loans, and generates the weekly overdue-file report. |
| Head of Medical Records | Reviews and records approval or rejection for loans involving files taken outside the building. |
| Borrowing-unit staff | Requests and borrows medical-record files and follows the return deadline. |
| Unit head | Receives the overdue-file list prepared and sent by the filing clerk for their respective borrowing unit. |

### IN — The Application Will

1. Record physical medical-record file loans and returns.
2. Link each loan to the medical record number, borrowing unit, borrower, and loan timestamp.
3. Display file status, borrower information, and return deadline.
4. Apply the 2 × 24-hour (48-hour) loan period.
5. Record Head of Medical Records approval when a file is taken outside the building.
6. Identify files that have passed their return deadline and have not been recorded as returned.
7. Generate a weekly overdue-file report grouped by borrowing unit.
8. Allow the filing clerk to prepare each unit's overdue list for its respective unit head.

### OUT — The Application Will Not

1. Store or manage clinical information such as diagnoses, treatment notes, or laboratory results.
2. Replace the hospital's entire SIMRS or Electronic Medical Record system.
3. Manage billing or clinical care activities.
4. Automatically send weekly overdue reports to unit heads.
5. Change the existing medical-record loan and return approval procedures.

## 3. User Stories

| ID | As a... | I want... | So that... |
|---|---|---|---|
| US-01 | Filing clerk | To record a medical-record file loan. | The location and responsibility for each file can be traced. |
| US-02 | Borrowing-unit staff | To know the status and due date of borrowed files. | My unit can return files on time. |
| US-03 | Head of Medical Records | To approve or reject loans involving files taken outside the building. | Files are not taken outside without the required approval. |
| US-04 | Filing clerk | To generate a weekly overdue-file list grouped by borrowing unit. | I can prepare and send each unit's overdue list to its unit head. |

## 4. Checks — How We Will Know It Works

| ID | Situation (Given) | Action (When) | Result (Then) |
|---|---|---|---|
| C-01 | A fictional medical-record file is available, the borrowing unit and borrower are identified, and the file will remain inside the building. | The filing clerk records the loan. | The application records the loan, borrower, unit, loan time, and 48-hour return deadline. No outside-building approval is required. |
| C-02 | A medical-record file is requested to leave the building, but approval from the Head of Medical Records has not been recorded. | The filing clerk attempts to release the file. | The application prevents release until the required approval is recorded. |
| C-03 (CR-1) | Several loans exist from different borrowing units. Some loans have passed their due date and have not been returned, while others are still within the loan period or have already been returned. | The filing clerk generates the weekly overdue-file report. | The application displays only loans whose due date has passed and whose return has not been recorded. The report is grouped by borrowing unit and shows relevant loan details, due date, and return status. |

## 5. Data List

| Data Group | Description | Example | Required? | Rule |
|---|---|---|---|---|
| File identity | Medical record number and patient name. | MR-DEMO-001, Patient Demo A | Yes | Used to identify the physical medical-record file. |
| Borrowing unit | Unit borrowing the medical-record file. | UNIT-DEMO-01 — Poli Anak | Yes | Must identify the borrowing unit. |
| Borrower information | Name or staff identifier of the responsible borrower. | STAFF-DEMO-02 | Yes | Identifies the person responsible for the borrowed file. |
| Loan timestamp | Date and time when the loan is recorded. | 10 Nov 2026, 09:00 | Yes | Used to calculate the return deadline. |
| Due date and time | Deadline for returning the borrowed file. | 12 Nov 2026, 09:00 | Yes | Calculated using the 48-hour loan period. |
| Return information | Actual return date and time and acknowledgement. | 12 Nov 2026, 08:30 | Required when returned | The filing clerk records the return. |
| Building-exit approval | Approval decision, approver, and decision time when a file leaves the building. | Approved by Head of Medical Records | Conditional | Required only when the file is taken outside the building. |
| Weekly overdue report (CR-1) | Reporting period and overdue loans grouped by borrowing unit. | UNIT-DEMO-01, MR-DEMO-003, Overdue | Required for report generation | Includes only loans past their due date that have not been recorded as returned. |

### Proposed Status Definitions

| Status | Meaning |
|---|---|
| Available | The file has no active loan. |
| Pending Approval | A loan involving a file taken outside the building is awaiting approval. |
| On Loan | The file has been borrowed and its return deadline has not passed. |
| Overdue | The return deadline has passed and the file has not been recorded as returned. |
| Returned | The file has been recorded as returned. |

### Weekly Overdue Report Rules (CR-1)

1. The report is generated by an authorized filing clerk.
2. A loan is overdue when its due date and time have passed and no return has been recorded.
3. The report groups overdue loans by borrowing unit.
4. Each unit's overdue list is prepared by the filing clerk for its respective unit head.
5. The report contains only information necessary to identify and follow up overdue loans.
6. The exact weekly reporting day and reporting cut-off remain subject to client confirmation.
7. The filing clerk is responsible for sending the report; automatic delivery is not included in CR-1.

## 6. Open Questions

| No. | Question | Reason |
|---|---|---|
| 1 | Does the 48-hour loan period start when the clerk records the loan or when the file physically leaves the records room? Do weekends and holidays count? | To ensure consistent calculation of the return deadline. |
| 2 | Should borrowing-unit staff have read-only access to loan status? | To determine application access permissions. |
| 3 | Which day and cut-off time should define the weekly overdue report period? | The client requested weekly reporting but did not specify the exact reporting schedule. |
| 4 | Which loan details should appear in the list sent to each unit head, and what is the approved sending method? | To ensure appropriate information is shared with authorized recipients. |

## Design Assumptions

1. The filing clerk is responsible for entering loan and return transactions.
2. The Head of Medical Records approves loans involving files taken outside the building.
3. The application tracks physical file movement and does not store clinical record contents.
4. The loan period remains 2 × 24 hours (48 hours).
5. The filing clerk prepares and sends the weekly overdue lists to the respective unit heads.
6. Automatic report delivery and a specific PDF or Excel format are not included because they were not specified in CR-1.

## Change Request and Version History

| Version | Description |
|---|---|
| 1.0 | Initial design for medical-record file loan and return tracking. |
| 1.1 | Added weekly overdue-file reporting grouped by borrowing unit in response to CR-1. Existing loan period, outside-building approval rule, and basic loan/return process remain unchanged. |
