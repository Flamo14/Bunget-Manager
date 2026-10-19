# Use Cases

## 1. Purpose

This document describes the user goals and system behavior currently implied by the Budget Manager product vision, requirements, and scenarios. It is a product-level description, not an implementation design. It does not select technologies or resolve the open product decisions listed in [requirements.md](requirements.md).

## 2. Actors

- **Visitor**: a person who has not authenticated.
- **Registered user**: a user who can authenticate and manage their own financial data.
- **Family owner**: the user who creates a family group and manages its membership.
- **Family member**: a registered user who has accepted an invitation to join a family group.
- **System**: Budget Manager, which validates requests, enforces access rules, and stores or presents data.

The exact difference between owner and member permissions remains to be decided.

## 3. Use Case Index

| ID | Use case | Primary actor |
| --- | --- | --- |
| UC-01 | Create an account | Visitor |
| UC-02 | Sign in and sign out | Registered user |
| UC-03 | Import transactions from CSV | Registered user |
| UC-04 | Create a transaction manually | Registered user |
| UC-05 | View, edit, or remove a transaction | Registered user |
| UC-06 | Create and manage categories | Registered user |
| UC-07 | Categorise a transaction | Registered user |
| UC-08 | View income and expense analysis | Registered user |
| UC-09 | Create and review a budget | Registered user |
| UC-10 | Create a family group | Registered user |
| UC-11 | Invite a user to a family group | Family owner |
| UC-12 | Accept a family invitation | Invited registered user |
| UC-13 | Manage family membership and sharing | Family owner |
| UC-14 | View personal or family dashboard and reports | Registered user / family member |

## 4. Use Case Details

### UC-01: Create an account

**Primary actor:** Visitor  
**Precondition:** The person does not have an account using the chosen unique identifier.  
**Trigger:** The visitor chooses to create an account.

**Main flow:**

1. The visitor provides the information required for registration.
2. The system validates the information and checks identifier uniqueness.
3. The system creates the account.
4. The system confirms that the account was created.

**Alternative flows:**

- If required information is invalid or incomplete, the system explains what needs correction and does not create the account.
- If the identifier is already in use, the system reports the conflict and does not create a second account.

**Postcondition:** The account exists. Whether the user is signed in immediately or must verify an email is undecided.

**Related requirements:** FR-01.

### UC-02: Sign in and sign out

**Primary actor:** Registered user  
**Precondition:** The user has an account.  
**Trigger:** The user chooses to sign in or sign out.

**Main flow:**

1. To sign in, the user provides the required authentication information.
2. The system verifies it and establishes an authenticated session.
3. The user accesses features allowed to that account.
4. To sign out, the user requests sign-out and the system ends the session.

**Alternative flows:**

- If authentication fails, the system denies access and provides an appropriate error.
- If the account cannot authenticate because of an unresolved verification or recovery policy, the system follows that policy once defined.

**Postcondition:** The user is authenticated after successful sign-in and unauthenticated after sign-out.

**Related requirements:** FR-02, FR-03.

### UC-03: Import transactions from CSV

**Primary actor:** Registered user  
**Precondition:** The user is authenticated.  
**Trigger:** The user starts a CSV import.

**Main flow:**

1. The user selects a CSV file.
2. The system validates the file type, encoding, structure, headers, and transaction data.
3. The system detects or proposes a mapping for transaction fields.
4. The system identifies invalid rows and possible duplicates, and presents a preview and summary.
5. The user reviews the preview and confirms the import.
6. The system saves the accepted transactions as one import operation.
7. The system reports the outcome and the imported transactions appear in the user's transaction list.

**Alternative flows:**

- If the file structure is unsupported or invalid, the system explains the issue and does not create transactions.
- If rows are invalid, the system identifies them and explains the reasons. Whether valid rows can still be imported alongside rejected rows is unresolved.
- If all rows are invalid, the system reports that no transactions can be imported.
- If duplicate rows are found, the system identifies them and does not create duplicate transactions; the exact duplicate identity rule needs confirmation.
- If the user cancels before confirmation, the system discards the pending import and leaves the transaction list unchanged.
- If persistence fails during confirmation, the system must not leave a partially committed import; recovery and retry behavior need definition.

**Postcondition:** After successful confirmation, the accepted transactions are stored and visible to the user. Before confirmation or after cancellation, no import changes the user's transaction records.

**Related requirements:** FR-05 through FR-19; BR-04, BR-10 through BR-12; Scenario 1.

### UC-04: Create a transaction manually

**Primary actor:** Registered user  
**Precondition:** The user is authenticated.  
**Trigger:** The user chooses to create a transaction.

**Main flow:**

1. The user enters transaction information.
2. The system validates required fields and financial values.
3. The system saves the transaction under the user's ownership.
4. The system confirms creation and makes the transaction available in the transaction list.

**Alternative flow:** If information is invalid or incomplete, the system explains the problem and does not save the transaction.

**Postcondition:** A valid transaction is associated with the user.

**Related requirements:** Manual transaction management FRs (numbering to be corrected in requirements.md), NFR-04 through NFR-06.

### UC-05: View, edit, or remove a transaction

**Primary actor:** Registered user  
**Precondition:** The user is authenticated and the transaction is within the user's permitted data scope.  
**Trigger:** The user selects a transaction to view or manage.

**Main flow:**

1. The system displays the transaction details.
2. The user edits one or more supported fields or requests removal.
3. The system validates the requested change and checks authorization.
4. The system saves the change or removes/archives the transaction, then confirms the outcome.

**Alternative flows:**

- If the user lacks permission or the transaction is outside their scope, the system denies the operation.
- If edited information is invalid, the system explains the issue and keeps the existing transaction unchanged.
- Whether removal means permanent deletion or archival, and whether edits are versioned, remain undecided.

**Postcondition:** The transaction reflects the accepted change, or remains unchanged if the operation is rejected or cancelled.

**Related requirements:** Manual transaction management FRs (numbering to be corrected in requirements.md); BR-01; NFR-04 through NFR-06.

### UC-06: Create and manage categories

**Primary actor:** Registered user  
**Precondition:** The user is authenticated.  
**Trigger:** The user chooses to create, update, or remove a category.

**Main flow:**

1. The user provides category information or selects a category to manage.
2. The system validates the request and the user's authority over the category.
3. The system saves the change and confirms the outcome.

**Alternative flows:**

- If category information is invalid or the user lacks authority, the system rejects the change and explains why.
- If a category is already assigned to transactions, the behavior when updating or removing it is undecided.
- Whether categories are personal, global, or family-shared remains undecided.

**Postcondition:** The category change is saved only within the permitted ownership scope.

**Related requirements:** FR-16, FR-19; BR-02.

### UC-07: Categorise a transaction

**Primary actor:** Registered user  
**Precondition:** The user is authenticated, can edit the transaction, and the selected category exists.  
**Trigger:** The user assigns or changes a transaction's category.

**Main flow:**

1. The user opens a transaction and selects a category.
2. The system checks the user's permission to edit the transaction.
3. The system checks that the category is available within the applicable ownership or family scope.
4. The system saves the category assignment and confirms the change.

**Alternative flow:** If the category is unavailable or the user lacks permission, the system rejects the assignment and leaves the transaction unchanged.

**Postcondition:** The transaction is associated with the selected permitted category.

**Related requirements:** FR-17, FR-18, BR-02; Scenario 2.

### UC-08: View income and expense analysis

**Primary actor:** Registered user  
**Precondition:** The user is authenticated and has transaction data in the requested scope.  
**Trigger:** The user requests analysis for a period or category.

**Main flow:**

1. The user selects a supported reporting period and view.
2. The system retrieves transactions within the user's permitted scope.
3. The system presents income and expense summaries by the supported date range and category.

**Alternative flows:**

- If there is no data for the selected period, the system presents an empty state rather than implying a zero-data report has been imported.
- If the requested period or grouping is unsupported, the system indicates which report options are available.
- Supported reporting granularity and whether reports use persisted transactions or imported monthly report records require clarification.

**Postcondition:** The user sees an analysis scoped to data they are authorized to access.

**Related requirements:** FR-21, FR-22, FR-25 through FR-30; BR-05, BR-09, BR-10.

### UC-09: Create and review a budget

**Primary actor:** Registered user  
**Precondition:** The user is authenticated.  
**Trigger:** The user chooses to create or review a budget.

**Main flow:**

1. The user specifies a budget target and period.
2. The system validates the target and period, then saves the budget.
3. The user requests budget progress.
4. The system compares actual spending with the budget and presents the result.

**Alternative flow:** If the budget details are invalid, the system explains the issue and does not save the budget. Budget period options and personal-versus-family ownership remain undecided.

**Postcondition:** A valid budget is stored and can be compared with spending in its defined scope.

**Related requirements:** FR-23, FR-24; open decisions 6 and 8 in requirements.md.

### UC-10: Create a family group

**Primary actor:** Registered user  
**Precondition:** The user is authenticated.  
**Trigger:** The user chooses to create a family group.

**Main flow:**

1. The user provides the required group information.
2. The system validates the request and creates the group.
3. The system assigns the creator as owner and confirms creation.

**Alternative flow:** If the user cannot create a group under the rules eventually defined, the system rejects the request and explains why.

**Postcondition:** The group exists and its creator is recorded as owner.

**Related requirements:** FR-26, FR-27 (family requirement numbering to be corrected in requirements.md); Scenario 3.

### UC-11: Invite a user to a family group

**Primary actor:** Family owner  
**Precondition:** The owner is authenticated and has authority to manage the group.  
**Trigger:** The owner invites another registered user.

**Main flow:**

1. The owner identifies the person to invite.
2. The system validates the invite and records a pending invitation.
3. The system makes the invitation available to the invited user and confirms that it was sent.

**Alternative flows:**

- If the target user cannot be identified or is not eligible, the system explains the issue and does not create an active membership.
- Invitation expiry, revocation, duplicate invitations, and delivery channel are undecided.

**Postcondition:** An invitation is pending; the invitee is not an active member until acceptance.

**Related requirements:** FR-28, FR-29 (family requirement numbering to be corrected in requirements.md).

### UC-12: Accept a family invitation

**Primary actor:** Invited registered user  
**Precondition:** The user is authenticated and has a valid pending invitation.  
**Trigger:** The user accepts the invitation.

**Main flow:**

1. The system presents the invitation and the group identity.
2. The user accepts.
3. The system records family membership and applies the permissions defined for that membership.
4. The system confirms that membership is active.

**Alternative flows:**

- If the invitation is invalid, expired, revoked, or already handled, the system explains that it cannot be accepted.
- If the user declines, membership is not created; whether the invitation record is retained is undecided.

**Postcondition:** Membership becomes active only after acceptance.

**Related requirements:** FR-29, FR-30 (family requirement numbering to be corrected in requirements.md); Scenario 3.

### UC-13: Manage family membership and sharing

**Primary actor:** Family owner  
**Precondition:** The owner is authenticated and has authority over the family group.  
**Trigger:** The owner changes membership or sharing settings.

**Main flow:**

1. The owner selects a member or sharing setting to manage.
2. The system checks the owner's authority and validates the requested change.
3. The system saves the change and confirms which access is affected.

**Alternative flows:**

- If the owner lacks authority or the request is invalid, the system rejects it and leaves membership and access unchanged.
- The requirements do not yet define whether sharing is selected per transaction, per data type, or for all member data, nor whether members can change their own sharing settings.
- What happens to existing access when a member leaves or is removed remains undecided.

**Postcondition:** Membership and access reflect the accepted change, according to the family permission rules once defined.

**Related requirements:** FR-30, FR-31 (family requirement numbering to be corrected in requirements.md); BR-03, BR-06 through BR-08; open decisions 5 and 9 in requirements.md.

### UC-14: View personal or family dashboard and reports

**Primary actor:** Registered user; family member for family views  
**Precondition:** The user is authenticated. Family membership is required for family views.  
**Trigger:** The user requests a personal or family dashboard/report.

**Main flow:**

1. The user selects a personal or family view and a supported reporting period.
2. The system checks membership and access permissions for the requested scope.
3. The system gathers the applicable transaction or monthly report data.
4. The system presents the report and makes its personal or family scope clear.

**Alternative flows:**

- If the user is not a member or lacks access to the family view, the system denies access.
- If no report data exists for the requested period, the system presents an appropriate empty state.
- The requirements conflict on whether family reports include all members' transactions automatically or only explicitly shared financial data. This must be resolved before the family report's data scope can be specified.
- The requirements also conflict or remain unclear about whether monthly reports are generated from imported CSV data or from persisted transactions, and how annual aggregation relates to those monthly reports.

**Postcondition:** The user sees only data permitted by the final personal/family access rules.

**Related requirements:** FR-25 through FR-30, FR-32 (family requirement numbering to be corrected in requirements.md); BR-05, BR-07 through BR-10; Scenario 3.

## 5. Decisions Needed Before Detailed Design

These use cases intentionally preserve unresolved product decisions. The following are especially important because they change user-visible behavior or data access:

- **CSV partial failure:** FR-11 and FR-19 describe import without partial persistence, while FR-13 describes importing valid rows and rejecting invalid ones. Decide whether confirmation commits all valid rows or whether any invalid row blocks the whole file.
- **Family report scope:** the vision says selected data can be shared, while BR-07 and BR-08 describe personal and family reports that automatically include all members' transactions. Define whether members opt in and what data is included.
- **Reporting source:** FR-28 and BR-10 say monthly reports come from CSV uploads, while BR-05 says reports use the same underlying transactions as the transaction list. Define whether CSV imports create transactions first and reports aggregate them, or whether monthly reports have a separate source of record.
- **Permissions:** define which roles can view, edit, share, and remove personal and family transactions, categories, budgets, and reports.
- **Currencies and amount rules:** define supported currencies, precision, sign conventions, and treatment of transfers/refunds before specifying validation and analysis behavior.
- **Data lifecycle:** define transaction deletion versus archival, edit history, invitation expiry/revocation, and behavior when family membership ends.
- **Requirement traceability:** FR identifiers are reused across sections, and some identifiers cited in the source do not correspond consistently to the stated behavior. Renumber requirements before using IDs as stable traceability references.