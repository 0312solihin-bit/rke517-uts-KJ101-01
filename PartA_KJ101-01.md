# Part A — Understand the Client

**Course:** RKE517 — Analysis and Design of Health Information Systems  
**Student:** Muhamad Solihin  
**NIM:** 20240306070  
**Case code:** KJ101-01  
**Organization:** RSU Permata Bekasi, Cikarang  
**Client named in the Case Brief & Interview:** Ibu Endang Susilowati, Senior Medical Records Clerk  
**Proposed system:** Medical Record File Loan Tracking App  
**Status:** v1.0 working draft; operational details marked as open questions remain subject to client confirmation.

## A1. Problem and goal

RSU Permata Bekasi currently records physical medical-record file loans in a handwritten loan book and uses tracer cards to mark files removed from their shelves. The client reports that two or three folders per week become difficult to trace, late returns may only be noticed when a file is needed again, and handwritten entries can be difficult to read or become wet. **The goal is to design a small application that records each loan and return, shows the current location and responsible borrower, tracks the 48-hour return period, records approval when a file leaves the building, and supports both screen-oriented staff and staff who still rely on the loan book.**

## A2. Client and service context

The case describes RSU Permata Bekasi as a type C general hospital in Cikarang, with 150 beds, approximately 600 outpatient visits and 120 inpatients per day, and 18 medical-record staff. The client request is limited to tracking the movement of physical medical-record folders between the records room and service units. The proposed app is not intended to manage clinical documentation or change the contents of a medical record.

## A3. Current process (as described in the interview)

1. A nurse from a service unit phones the medical-records room to request a patient's file.
2. The filing clerk locates the physical folder on the shelf using its medical record number.
3. The filing clerk writes the loan in the paper loan book and places a tracer card in the empty shelf slot.
4. A porter or the nurse carries the folder to the requesting unit.
5. When the folder is returned, the filing clerk writes the return date in the loan book and removes the tracer card.

### Main problems evidenced by the case

- The client reports that two or three folders per week become difficult to trace.
- A borrowed file can remain in a drawer for a week after a doctor borrows it for a meeting.
- Staff may only notice that a file is late when the next patient needs it.
- Handwriting in the loan book can be difficult to read, and pages can become wet.
- Staff chat reports difficulty interpreting the handwriting for a medical record number and says that locating the borrower took 20 minutes; the chat asks that an extension number be written next time.

## A4. Requirements and acceptance checks

The eight requirements below are deliberately limited to the client's stated problem and the evidence in the four case sources. Where the exact operational rule is not yet stated, the requirement is marked as provisional and linked to an open question.

| ID | Requirement | Type / source | Acceptance check: Situation → Action → Result |
|---|---|---|---|
| **R-01** | The app shall allow an authorized filing clerk to register a physical file loan using the fields shown on Form RM-47: loan date, medical record number, patient name, borrowing unit, borrower, signature/evidence of acknowledgement, due-back date, and return information when available. A loan time field is proposed so that the 48-hour deadline can be calculated accurately; this field is not printed on the current paper form and must be confirmed. | Functional. Form RM-47 rev. 2; current process interview. | **Situation:** A service unit requests a file and the filing clerk has located the folder. → **Action:** The clerk enters the RM number, patient name, unit, borrower, loan date and proposed loan time, and acknowledgement details, then saves the transaction. → **Result:** One active loan record is created and the file is shown as on loan to the selected unit and borrower. |
| **R-02** | The app shall let an authorized user search by medical record number and view the file's current state and, when it is on loan, the borrowing unit, borrower, loan date, and due-back date/time. | Functional. Client's need to see status; Form RM-47 fields. | **Situation:** An authorized staff member searches for a file using a known RM number. → **Action:** The user opens the matching file record. → **Result:** The app displays whether the file is available, on loan, or overdue and shows the current unit, borrower, and due-back date/time when applicable. |
| **R-03** | The app shall calculate and display the return deadline as **2 × 24 hours (48 hours) after borrowing**, using the loan date/time recorded in the app as the provisional clock-start value. The app shall mark an unreturned loan as overdue after that deadline. | Business rule. Form RM-47 rev. 2 and the client's later voice-note transcript; interview originally says three days. Capturing loan time is a proposed design detail because the form records the date only; exact clock-start event remains open. | **Situation:** A loan is recorded at 10 November 2026, 09:30 (fictional test value). → **Action:** The app applies the 48-hour rule to the recorded loan date/time. → **Result:** The due date/time is shown as 12 November 2026, 09:30; if the file has not been returned after that point, the loan is shown as overdue. |
| **R-04** | The app shall record whether a loan is staying inside the hospital building or leaving the building. It shall require the Head of Medical Records' approval to be recorded before release only when the file leaves the building; an in-building loan shall not be blocked for lack of this approval. | Business rule. Interview and Form RM-47 rev. 2 both state approval is needed only for loans leaving the building. The in-building/out-of-building field is a proposed design detail. | **Situation:** Two loan requests are entered: one for use inside the building and one that will leave the building. → **Action:** The clerk records the destination type and attempts to release each file. → **Result:** The in-building loan can proceed without Head approval; the out-of-building loan cannot be marked released until approval is recorded. |
| **R-05** | The app shall allow an authorized filing clerk to record that a borrowed file has physically returned and close the active loan, while retaining the completed loan details as history. | Functional. Current process interview; Form RM-47 has a Returned field. Recording an exact return timestamp is a proposed detail to support the 48-hour rule and must be confirmed because the paper form currently shows a return date field only. | **Situation:** A borrowed folder is physically returned to the records room. → **Action:** The filing clerk opens its active loan and records the return date and, if confirmed, the exact return time/system timestamp. → **Result:** The loan is closed, the file's current state changes to available, and the completed loan remains in the history. |
| **R-06** | The app shall provide a searchable view of active and overdue loans so authorized staff can identify the file, borrowing unit, responsible borrower, and due-back date for follow-up. | Functional. Client's stated need for everyone to see status and the reported problem of late returns. | **Situation:** A staff member needs to find files that have not returned by their due-back time. → **Action:** The staff member filters the loan list to active or overdue loans. → **Result:** The app shows each outstanding file with its borrowing unit, borrower, and due-back date/time so the responsible staff can follow it up. |
| **R-07** | The app shall provide a contact-extension field for a borrower or borrowing unit, with the final field meaning and whether it is mandatory to be confirmed by the client. Until confirmed, the field shall be optional. | Data / functional. Staff Chat: Mbak Tika asks that an extension number be written because locating the borrower took 20 minutes. | **Situation:** A loan is registered and a valid extension number is supplied. → **Action:** The filing clerk enters the extension in the designated contact field. → **Result:** Authorized staff can use the saved extension to contact the responsible borrower or unit; a loan can still be saved when no extension has been confirmed as mandatory. |
| **R-08** | The solution shall support an agreed paper-assisted working method for staff who prefer the loan book and a documented fallback/reconciliation method for periods when the app is unavailable, so that paper and digital records do not conflict. The exact routine use of paper versus downtime-only use must be agreed with the client. | Operational / non-functional. Interview and voice-note transcript: the client says screen-oriented staff and staff who prefer the book must both be able to work, and asks what happens if the app breaks. | **Situation:** The app or network is unavailable, or a staff member needs to use the paper process. → **Action:** Staff use the approved paper fallback and later reconcile the loan/return into the app under the agreed responsibility and procedure. → **Result:** Physical file movements remain recorded and the app's status is brought back into agreement with the paper record without silently losing or duplicating a transaction. |

## A5. Open questions for the client

| ID | Question / evidence difference | Why it matters | Proposed clarification / working position |
|---|---|---|---|
| **OQ-01** | The interview says a unit may keep a borrowed file for **3 days**. Form RM-47 rev. 2 states **2 × 24 hours**, and the later voice-note transcript says **two by 24 hours** and asks the app to follow that. | The return deadline and overdue status depend on the authoritative period and the event that starts the clock. The form captures a date but not a time, so using an exact timestamp is a proposed design detail. | For the v1.0 working design, use **48 hours** because the paper form and later voice-note clarification agree. Ask the client to confirm whether the clock starts when the clerk records the loan or when the folder physically leaves the records room, whether the app must capture loan/return times in addition to dates, and whether weekends/holidays count. |
| **OQ-02** | The Case Brief names the client **Ibu Endang Susilowati**. The voice-note speaker introduces herself as **Andang Susilawati**. The staff chat refers to **Ibu Endang** but does not provide her full name. | The analyst must know whether the voice note is from the same client and record the client's name accurately. | Confirm the correct spelling of the client's full name and whether the voice-note speaker is the same person named in the Case Brief. Do not silently change either spelling. |
| **OQ-03** | The interview and Form RM-47 agree that Head of Medical Records approval is needed only when a file leaves the building, but neither defines the evidence or exact recording steps. | The app needs a reliable release control and an audit trail for off-site loans. | Confirm what counts as leaving the building and whether approval must record approver name, date/time, signature, reason, or another identifier. |
| **OQ-04** | The interview says a porter or nurse may carry the file, while Form RM-47 has a Borrower field and a separate Signature field. | The person accountable for the loan may differ from the person physically transporting the folder. | Confirm whether the app must record both the accountable borrower and the carrier/porter, and whose acknowledgement/signature is required. |
| **OQ-05** | The staff chat asks the borrower to write an extension number, but Form RM-47 has no extension field. | The system needs to store the right contact detail without collecting an unclear or unnecessary field. | Confirm whether the extension belongs to the individual borrower or the borrowing unit, its format, and whether it is required for every loan. |
| **OQ-06** | The interview describes a nurse telephoning the records room, and the client also says many forms received are handwritten by doctors. | The system must define who enters a request and how unclear or incomplete requests are verified before a physical file is released. | Confirm whether phone requests, handwritten forms, or both are accepted; identify the required request details and who verifies unclear handwriting or an uncertain RM number. |
| **OQ-07** | The client says younger staff prefer screens while she prefers the book and both must be able to work. The interview also asks what happens if the app breaks, but no fallback procedure is specified. | Routine parallel paper-and-digital entry can create duplicate or conflicting records unless one source of truth and a reconciliation process are clear. | Confirm whether the book remains in routine parallel use, is used only during downtime, or is phased out gradually. Also identify which record is authoritative and who reconciles paper entries into the app. |
| **OQ-08** | The interview identifies filing clerks, nurses/doctors who borrow files, and the Head of Medical Records, but it does not define detailed viewing, editing, approval, and return permissions. | Role permissions are necessary for accountability and to prevent unauthorized changes to loan records. | Confirm which roles may create, view, correct, cancel, approve, release, and close a loan. Also confirm whether borrowing units can see only their own unit's loans or all loan statuses. |
| **OQ-09** | The client wants to see every file's status, but the sources do not specify who follows up an overdue file or whether reminders are required. | A visible overdue list may not be enough to ensure timely return if follow-up responsibility is unclear. | Confirm who monitors overdue loans, when follow-up occurs, and whether the app should send reminders or simply display an overdue list. |

## A6. Initial scope boundary

### Candidate IN scope

- Registering a physical medical-record file loan.
- Linking a loan to the correct RM number, patient name, borrowing unit, accountable borrower, and contact extension when confirmed.
- Displaying the current file location/state and loan history.
- Calculating the 48-hour return deadline and identifying overdue loans.
- Recording Head of Medical Records approval when a file leaves the building.
- Recording file returns and closing active loans.
- Defining a paper-assisted/downtime fallback and reconciliation procedure.

### Candidate OUT of scope (unless the client changes the request)

- Writing or editing clinical notes, diagnoses, or other clinical content.
- Scanning or digitizing the physical medical-record contents.
- Replacing the hospital's full electronic medical record or hospital information system.
- Billing, clinical decision support, and patient-care workflows.
- External delivery or courier tracking beyond recording who is responsible for carrying the folder, if the client confirms that field is needed.

## A7. Source traceability

| Source | Evidence used in this Part A |
|---|---|
| **01 — Case Brief & Interview** | Hospital/client context; client request; current five-step loan/return process; reported 2–3 hard-to-trace folders per week; late returns; handwriting and wet-page problems; interview's three-day statement; approval only for files leaving the building; phone request process; porter/nurse carrying files; client concern about app downtime and mixed staff preferences; success criterion that status should be visible without asking someone. |
| **02 — Client Voice Note (transcript supplied by the student)** | Speaker says the loan period is “two by 24 hours”, asks the app to follow that rule, emphasizes that nothing should be lost and everybody should see status, and says both screen-oriented staff and staff who prefer the book must be able to work. Speaker's stated name is captured as “Andang Susilawati” and is not silently normalized. |
| **03 — Current Paper Form (Form RM-47 rev. 2)** | Fields: Date, RM No., Patient name, Borrowing unit, Borrower, Signature, Due back, Returned. Printed rules: return within 2 × 24 hours after borrowing; Head of Medical Records approval only for loans that leave the building. |
| **04 — Staff Chat** | Staff report a recurring problem with RM number 12-34-56; Mbak Nia says the handwriting in the book is unclear; Mbak Tika asks that an extension number be written because she spent 20 minutes looking for the borrower; Ibu Endang supports forwarding the suggestion. |

## A8. Analyst decisions for the v1.0 working draft

1. **Return period:** Use 48 hours as the working rule because Form RM-47 rev. 2 and the later voice-note transcript agree. Keep the exact clock-start event, timestamp capture, and weekend/holiday treatment open for confirmation.
2. **Client identity:** Retain “Ibu Endang Susilowati” as the name in the Case Brief, and record “Andang Susilawati” as the name stated in the voice-note transcript. Confirm whether they refer to the same person.
3. **Extension number:** Include it as an optional candidate field because it appears in the staff chat; do not make it mandatory until the client confirms whose extension it is and how it should be used.
4. **Paper and digital use:** Include a paper-assisted/downtime requirement because the client explicitly says both book-oriented and screen-oriented staff must be able to work. Do not decide yet whether paper runs routinely in parallel or only during downtime.
5. **System boundary:** Track the movement and status of physical folders only; clinical content is outside the requested system.

---

**Student review note:** Before committing this file to GitHub, read each requirement and open question, verify that it matches the case sources, and be ready to explain the provisional choices in your own words.
