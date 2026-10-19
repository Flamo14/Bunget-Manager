# Requirements Specification

## 1. Purpose

This document defines the requirements for the Budget Manager application described in the product vision and user scenarios. It is intentionally written as a business and product requirements document, not as an implementation specification. It captures what the system must do, what is out of scope, and which decisions still need validation before architecture and technology choices are made.

The requirements below are aligned with the project constraints:

- This is a learning project intended to demonstrate real software engineering practice.
- The repository should be suitable for a developer portfolio.
- Documentation is a core part of the project.
- Requirements should be explicit about assumptions and missing decisions.
- No implementation work should begin before these requirements are understood.

## 2. Product Summary

Budget Manager is a web application for individuals and family groups to import, review, categorise, and analyse financial transactions. Users need a consistent way to manage data exported from multiple banking systems, reduce manual work, and understand spending and income patterns over time.

## 3. Scope

### In scope

- User account creation and authentication
- CSV transaction import
- Manual transaction creation and editing
- Transaction review and categorisation
- Category creation and management
- Spending and income analysis
- Budget creation and tracking
- Reporting and dashboard views
- Family group management and limited shared data access

### Out of scope

- Direct banking integration or account synchronization
- Payment execution or financial transfers
- Automated financial advice
- Banking or ledger-grade compliance workflows
- Investment portfolio management
- Tax preparation

## 4. Users and Roles

### 4.1 Individual user

An individual user is the primary actor in the system. They can:

- register and authenticate
- manage their own transactions and categories
- import CSV files
- create and edit budgets
- view their dashboard and reports
- invite family members to a family group
- manage their own financial data visibility

### 4.2 Family group owner

The owner is the creator of a family group. They can:

- create a family group
- invite other users
- approve or manage membership
- define which financial data is shared
- see both personal and shared dashboard/report views

### 4.3 Family group member

A family group member is a user that accepted an invitation. They can:

- access shared family data according to permissions
- view relevant transactions and reports
- contribute to shared financial views when permitted

## 5. Functional Requirements

### 5.1 Account and authentication

FR-01: The system shall allow a user to create an account with a unique identifier and a secure password or equivalent authentication mechanism.

FR-02: The system shall allow a user to log in and log out securely.

FR-03: The system shall protect user data from unauthorised access using authentication and authorization rules.

FR-04: The system shall support user profiles that identify ownership of transaction data and categories.

### 5.2 CSV import

FR-05: The system shall allow a user to upload a CSV file containing financial transaction data.

FR-06: The system shall validate the uploaded file format and structure before import, including file type, encoding, column headers, required fields, and row completeness.

FR-07: The system shall detect or infer the CSV mapping for common fields such as date, description, amount, and transaction type.

FR-08: The system shall present a preview of imported transactions before final confirmation.

FR-09: The system shall allow the user to confirm the import and persist the transactions.

FR-10: The system shall display imported transactions in the user's transaction list after successful import.

FR-11: If the CSV file contains structural or data errors, the system shall not import any rows until the user corrects the problem or cancels the operation.

FR-12: The system shall identify invalid rows with clear reasons, such as missing required fields, invalid dates, invalid amounts, malformed values, or duplicate rows.

FR-13: When a file has a mix of valid and invalid rows, the system shall keep the invalid rows out of the import and present a summary of the rejected records to the user.

FR-14: If a CSV file has no valid rows, the system shall cancel the import and show a clear error message without creating transactions.

FR-15: The system shall support re-importing a file and shall detect duplicate transactions using a consistent duplicate rule based on transaction identity, such as normalized date, amount, description, and counterparty.

FR-16: On repeated import, the system shall not create duplicate transactions for records already present; instead, it shall mark them as duplicates, skip them, and report the count of skipped duplicates to the user.

FR-17: The system shall provide a final import summary after confirmation, including counts of imported transactions, skipped duplicates, and rejected rows.

FR-18: If the user cancels the import after preview, the system shall discard the imported data and leave the user's transaction list unchanged.

FR-19: Once the user confirms the import, all valid rows in the uploaded file shall be committed as transactions in a single import operation, and the user shall receive confirmation that the import succeeded.

### 5.3 Manual transaction management

FR-11: The system shall allow a user to create a transaction manually.

FR-12: The system shall allow a user to view a transaction in detail.

FR-13: The system shall allow a user to edit transaction information such as date, description, amount, counterparty, category, and notes.

FR-14: The system shall allow a user to delete or archive a transaction when necessary.

FR-15: The system shall retain sufficient transaction metadata to support auditability and reporting.

### 5.4 Categories

FR-16: The system shall allow a user to create categories for transactions.

FR-17: The system shall allow a user to assign a category to a transaction.

FR-18: The system shall validate that a category belongs to the user or is available to the user's family group before assignment.

FR-19: The system shall allow a user to update or remove categories that are no longer needed.

FR-20: The system shall support categorisation as a core mechanism for income and expense analysis.

### 5.5 Analysis and reporting

FR-21: The system shall allow a user to analyse income and expenses over time.

FR-22: The system shall provide views that summarize transactions by category, amount, and date range.

FR-23: The system shall support creating budgets for selected time periods.

FR-24: The system shall allow comparison between actual spending and budget targets.

FR-25: The system shall provide dashboard or report views that help users understand their current financial situation.

FR-26: The system shall support monthly reporting for a selected month, including totals by category and summary of income and expenses for that period.

FR-27: The system shall support annual reporting for a selected year, based on aggregated monthly report data rather than direct raw transaction processing.

FR-28: For monthly reporting, the user shall be able to import a CSV file containing transactions for the selected month and generate the monthly report from that imported data.

FR-29: For annual reporting, the system shall calculate the yearly totals by aggregating the corresponding monthly reports for the selected year.

FR-30: The system shall make the distinction between monthly and annual report sources explicit so that annual results are derived from aggregated monthly data and not created as independent raw imports.

### 5.6 Family data sharing

FR-26: The system shall allow a user to create a family group.

FR-27: The system shall assign the creator of a family group as the family owner.

FR-28: The system shall allow the family owner to invite another registered user to the family group.

FR-29: The system shall require that an invited user accept the invitation before membership is active.

FR-30: The system shall record family membership and permissions for each member.

FR-31: The system shall restrict access to shared financial data according to explicit permissions.

FR-32: The system shall allow the user to view their personal dashboard and separate family dashboard or shared views.

## 6. Non-Functional Requirements

### 6.1 Usability

NFR-01: The application should be straightforward to use for users who are comfortable with web-based tools.

NFR-02: The core flows for import, categorisation, and reporting should be clear and efficient.

NFR-03: The UI should provide confirmation states for actions such as file import and transaction update.

### 6.2 Data integrity

NFR-04: The system shall preserve transaction data accurately during import, edit, and categorisation.

NFR-05: The system shall avoid assigning transactions to categories outside the allowed ownership or family access scope.

NFR-06: The system shall prevent inconsistent or invalid financial data from being saved without user confirmation or validation.

### 6.3 Security and privacy

NFR-07: User authentication and authorization must follow secure practices.

NFR-08: Family data must be isolated by ownership and permission boundaries.

NFR-09: Sensitive financial information must be stored and accessed in a controlled manner.

NFR-10: The system should make it clear which data is personal and which is shared with the family group.

### 6.4 Reliability and maintainability

NFR-11: The system should be built with clear separation of responsibilities to support long-term maintenance.

NFR-12: The codebase should be understandable and structured to be suitable for portfolio review and future extension.

NFR-13: The project should include documentation that explains decisions, trade-offs, and architecture choices.

### 6.5 Performance

NFR-14: Transaction import and list display should remain responsive for typical personal and family-sized datasets.

NFR-15: Reporting and dashboard generation should be efficient enough for regular use without significant delays.

## 7. Data Requirements

The system must manage several categories of data:

- user account data
- authentication/session data
- financial transactions
- category definitions
- budgets
- reports/dashboard aggregates
- family group membership and permissions
- CSV upload metadata

The exact data model is not defined yet. The project must decide the following before implementation:

- how transactions are normalized across banks and CSV variants
- whether amounts are stored as integers, decimals, or currency-specific values
- whether the application supports a single currency or multiple currencies
- whether categories are global, per-user, or shared within a family group
- how family permissions are represented and enforced

## 8. Business Rules

BR-01: A transaction belongs to a user, unless explicitly shared through family access rules.

BR-02: A category may be used only by the user who owns it or by members who are granted access through family sharing.

BR-03: A family owner retains responsibility for managing family group membership and access controls.

BR-04: Imported data must be reviewable before it becomes part of the user's financial records.

BR-05: Reports should be based on the same underlying transaction records used in the transaction list to avoid inconsistent analysis.

BR-06: All users in the same family group shall have identical permissions for shared family features, including the ability to view the family report and the ability to manage family membership according to the group rules.

BR-07: Each user shall have two separate report views: a personal report that includes only that user's transactions, and a family report that includes all transactions belonging to all members of the user's family group.

BR-08: A family report shall automatically include every transaction created by each family member without any manual selection of individual transactions.

BR-09: Reports shall be generated at two levels: monthly and yearly. Monthly reports shall reflect the transactions in the selected month, while yearly reports shall be calculated from the aggregated monthly reports of the selected year.

BR-10: The monthly report shall be generated from CSV data uploaded for that month, while the yearly report shall not require a separate raw CSV import and shall instead be produced by aggregating the monthly report results.

BR-11: The system shall not create duplicate transactions when the same transaction is imported again; repeated imports shall be checked against existing records and duplicates shall be skipped with a warning.

BR-12: The system shall treat CSV import as a confirmation-based workflow: a preview is shown before import, invalid rows are rejected, and final persistence occurs only after user confirmation.

## 9. Key Constraints

- The application is a learning project and should prioritize clarity and maintainability over complexity.
- The project must reflect professional engineering practices in structure, documentation, and decision-making.
- The solution should be suitable for a portfolio and should demonstrate an understanding of trade-offs.
- The project should not assume a technology stack before the key decisions are justified.

## 10. Open Decisions and Missing Information

The following decisions are not yet specified and must be resolved before architecture or implementation:

1. Authentication approach
   - local authentication vs third-party identity provider
   - email verification requirements

2. Deployment model
   - single-user local deployment vs internet-hosted service
   - production environment assumptions

3. Data persistence
   - relational database vs document database
   - schema design and migration strategy

4. CSV compatibility
   - which bank formats are supported initially
   - whether custom column mapping is required
   - how malformed rows are handled

5. Family sharing model
   - whether family data is fully shared or partially restricted by entity
   - how permissions are granted and revoked

6. Budget structure
   - monthly, weekly, or custom budget periods
   - whether budgets are per-user or per-family

7. Currency support
   - single currency only or multi-currency support

8. Reporting granularity
   - daily, monthly, yearly, category-level, or custom date ranges
   - whether monthly reports are built from uploaded CSV files and whether yearly reports are derived from those monthly aggregates

9. Access controls
   - whether all family members can edit shared transactions or only certain roles can

10. Audit and traceability
   - whether transaction edits should be versioned or simply overwritten

## 11. Acceptance Criteria

The project can be considered successful if:

- users can create accounts and sign in
- users can import CSV transactions and review them before accepting import
- users can create and edit transactions and categories
- users can categorise transactions and analyse spending/income
- users can create budgets and review reports
- family groups can be created and shared data can be controlled by permissions
- the implementation matches the documented requirements and constraints

## 12. Summary

This document defines the functional and non-functional requirements for the Budget Manager project. It intentionally leaves some decisions open where the product scope does not yet provide sufficient detail. This is a deliberate choice to ensure that later architecture and technology decisions are justified rather than guessed.
