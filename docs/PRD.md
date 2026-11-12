
# Part C — Mini-PRD

**Course:** RKE517 — Analysis and Design of Health Information Systems  
**Student:** Muhamad Solihin  
**NIM:** 20240306070  
**Case code:** KJ101-01  
**Client:** Ibu Endang Susilowati, Senior Medical Records Clerk  
**Organization:** RSU Permata Bekasi, Cikarang  
**Product:** Medical Record File Loan Tracking App  
**Version:** 1.1  
**Status:** Updated based on Change Request CR-1

> This Mini-PRD follows the six-part structure from the Week 6 template. Details not stated by the client are labelled as proposed assumptions or open questions.

## 1. Problem and goal

RSU Permata Bekasi currently records physical medical-record file loans in a handwritten book and uses tracer cards, but staff report that two or three folders per week become difficult to locate, overdue returns may only be noticed when a file is needed, and handwritten entries can be unclear or damaged. The client wants a way to see the status of each file without repeatedly asking staff, while keeping a workable process for people who prefer screens and those who still rely on the book.

**Goal:** provide a small, auditable application that records file loans and returns, displays the current borrower/unit and due time, applies the 48-hour working return rule, and enforces Head of Medical Records approval only when a folder leaves the building.

**CR-1 addition:** the application must also provide a weekly overdue-file report grouped by borrowing unit. The filing clerk can prepare each unit's overdue list and send it to the respective unit head.

## 2. Users and scope

### Users

| User (role) | What they do in the app |
|---|---|
| Filing clerk | Searches the file register, enters the loan, records the borrower/unit and loan time, records the return, monitors active/overdue loans, and generates the weekly overdue-file report. |
| Head of Medical Records | Reviews and records approval/rejection for loans that leave the building; views loan-monitoring information. |
| Authorized borrowing-unit staff (proposed, read-only) | Checks the status and due time of loans belonging to their own unit. This access is an assumption pending confirmation of roles and permissions. |
| Unit head (report recipient) | Receives the overdue-file list prepared and sent by the filing clerk for their respective borrowing unit. CR-1 does not require a separate application account for the unit head. |

### IN — the app will

1. Register and track physical file loans and returns, linking each loan to one file, borrowing unit, and accountable borrower.
2. Display file/loan status and due date/time, calculate the working 48-hour limit, and identify overdue files.
3. Capture the off-site flag and require a recorded Head of Medical Records approval before release when the folder leaves the building.
4. Generate a weekly overdue-file report grouped by borrowing unit so the filing clerk can prepare and send each unit's list to its respective unit head.

### OUT — the app will not

1. Store, edit, or display the clinical contents of the medical record (diagnoses, treatment notes, laboratory results, etc.).
2. Scan/digitize folders or replace the hospital's full RME/SIMRS.
3. Manage billing, clinical care, or unrelated hospital workflows.
4. Automatically send reports to unit heads. The filing clerk prepares and sends the lists.

## 3. User stories

| ID | As a… | I want… | So that… |
|---|---|---|---|
| US-01 | Filing clerk | I want to record a loan using the medical record number, unit, accountable borrower, and loan time. | The location and responsibility for each physical folder can be traced. |
| US-02 | Authorized borrowing-unit staff member | I want to view the status and due time of my unit's borrowed files. | My unit can return files on time without repeatedly calling the records room. |
| US-03 | Head of Medical Records | I want to approve or reject a loan flagged as leaving the building. | Files are not released outside the building without the required approval. |
| US-04 | Filing clerk | I want to generate a weekly list of overdue files grouped by borrowing unit. | I can prepare and send the relevant overdue-file list to each unit head for follow-up. |

## 4. Checks — how we will know it works

| ID | Situation (Given) | Action (When) | Result (Then) |
|---|---|---|---|
| C-1 Normal in-building loan | A fictional file `MR-DEMO-001` is available; Unit `UNIT-DEMO-01` requests it; borrower `STAFF-DEMO-02` is recorded; the folder will stay inside the building; loan time is 10 Nov 2026 at 09:00. | The filing clerk saves the loan. | The system creates one active loan, calculates the due time as 12 Nov 2026 at 09:00 (48 hours later), shows the borrowing unit and borrower, and does not ask for Head approval because the file stays inside the building. |
| C-2 Off-site approval failure | A fictional available file `MR-DEMO-002` is requested to leave the building, but no approved decision from the Head of Medical Records is recorded. | The filing clerk attempts to release/activate the loan. | The system blocks release, keeps the transaction pending approval (or not released), records the missing approval condition, and does not mark the file as successfully loaned out. |
| C-3 Weekly overdue report (CR-1) | The system contains loan records from different borrowing units. Some loans have passed their due date/time and have not been recorded as returned. Other loans are still within the loan period or have already been returned. | The filing clerk generates the weekly overdue-file report. | The report includes only loans whose due date/time has passed and whose return has not been recorded. The results are grouped by borrowing unit and display relevant loan details, due date/time, and return status. The filing clerk can prepare each unit's list for its respective unit head. |

## 5. Data list

The data groups below follow the Week 6 template's compact data-list format. Related subfields are grouped to cover the fields visible on RM-47 and the additional contact, approval, and reporting needs.

| Field name / data group | Meaning | Synthetic example | Required? | Rule |
|---|---|---|---|---|
| File identity | Medical record number and patient name printed on the paper form. | `MR-DEMO-001`, `Patient Demo A` | Yes | MR number identifies the physical folder; patient name is a display/cross-check field, not a clinical data field. |
| Borrowing unit | Service unit receiving the folder. | `UNIT-DEMO-01 — Poli Anak` | Yes | Must refer to a registered hospital unit. |
| Accountable borrower and contact | Name/staff identifier of the borrower and contact extension when available. | `STAFF-DEMO-02`, extension `1234` | Borrower: Yes; extension: not yet confirmed | The extension was requested in staff chat; confirm whether it belongs to the borrower or unit and whether it is mandatory. |
| Loan timestamp and due timestamp | Date/time the loan is registered and calculated due date/time. | `10 Nov 2026 09:00` → `12 Nov 2026 09:00` | Yes | Due timestamp is loan timestamp + 48 elapsed hours under the working rule. Confirm the clock-start event and weekend treatment. |
| Return and acknowledgement | Actual return date/time and evidence corresponding to the Signature/Returned fields on RM-47. | `12 Nov 2026 08:30`, acknowledgement recorded | Return time: required when returned; signature method: open | Return is recorded by the filing clerk. Confirm whether signature is paper, electronic, or another acknowledgement. |
| Building-exit approval | Whether the folder leaves the building and, if yes, approval decision, approver, and decision time. | `leaves_building = true`, `Approved` | Conditional | Head approval is required only for a loan that leaves the building; keep approval evidence linked to the loan. |
| Weekly overdue report (CR-1) | Reporting period and list of overdue loans grouped by borrowing unit, with relevant loan details. | `UNIT-DEMO-01`, `MR-DEMO-003`, due `10 Nov 2026 09:00`, status `Overdue` | Required for report generation | Include only loans whose due date/time has passed and that have not been recorded as returned. Group results by borrowing unit. Include only information necessary for follow-up and authorized recipients. |

### Proposed status meaning (design definition)

| Display status | Meaning |
|---|---|
| Available | No active loan is open for the file. |
| Pending Approval | A loan that will leave the building is waiting for the required Head decision; the file has not been released. |
| On Loan | The file has been released and its 48-hour due time has not passed. |
| Overdue | The file has not been returned and the 48-hour due time has passed. |
| Returned | The latest loan transaction has a recorded return time; the file is available for another loan. |

### Weekly overdue report rules (CR-1)

1. The report is generated by an authorized filing clerk.
2. A loan is classified as overdue when its due date/time has passed and no return has been recorded.
3. The report groups overdue loans by borrowing unit.
4. Each unit's list is prepared by the filing clerk for its respective unit head.
5. The report contains only information needed to identify and follow up the overdue loan.
6. The exact weekly reporting cut-off and the approved sending method remain open for client confirmation.

## 6. Open questions for the client

| # | Question | Why it matters |
|---|---|---|
| 1 | The interview says three days, while RM-47 and the later voice note say 2 × 24 hours. Does the 48-hour period start when the clerk records the loan or when the folder physically leaves the room, and do weekends/holidays count? | The app must calculate the due time consistently and identify overdue files correctly. |
| 2 | Should borrowing-unit staff have read-only status access, and should the paper book continue in parallel or be used only during app/network downtime? | This determines role permissions, the authoritative record, and how duplicate/conflicting entries are prevented. |
| 3 | Which day or reporting cut-off should define the weekly overdue report period? | The client requested a weekly report but did not specify the exact reporting day or cut-off. |
| 4 | Which loan details should appear in the list sent to each unit head, and what is the approved method for sending the report? | This ensures the report contains sufficient information for follow-up while limiting access to patient-related information. |

**Other open details remain listed in Part A**, including the client-name spelling difference, signature method, borrower-versus-carrier accountability, contact-extension ownership, and off-site approval evidence.

## Design assumptions (not yet confirmed client rules)

- The filing clerk remains responsible for entering telephone requests into the application.
- The Head of Medical Records can review off-site approval requests in the application.
- Borrowing-unit access, if approved, is read-only and restricted to that unit's own loans.
- For the working design, due time is calculated from the saved loan date/time by adding 48 elapsed hours. This must be confirmed before implementation.
- The app tracks folder movement only; it does not store clinical content.
- The filing clerk prepares and sends the weekly overdue lists. Automatic delivery and a specific report file format are not included in CR-1.
