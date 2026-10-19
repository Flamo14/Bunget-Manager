# Budget Manager MVP

## Purpose

This document defines a first working version of Budget Manager. The MVP focuses on an individual user's core workflow: import transactions, review and categorise them, then understand income and expenses from the saved records. It is intentionally narrower than the full product vision so the first release can validate the central problem without taking on unresolved family-sharing and budgeting rules.

The scope is based on [product_vision.md](product_vision.md), [requirements.md](requirements.md), [scenario.md](scenario.md), and [use_cases.md](use_cases.md). Requirement identifiers are accompanied by section names where possible because `requirements.md` reuses FR numbers.

## 1. Necessary for the MVP

### Individual account and data isolation

- A visitor can create an account and a registered user can sign in and out.
- A user's transactions and categories are associated with that user and inaccessible to other users without authorization.
- Related source: Account and authentication (FR-01–04); UC-01 and UC-02.

### Import transactions from CSV

- An authenticated user can upload a CSV containing transactions in a documented, initially supported format.
- The system validates file structure and transaction rows, presents a preview, and explains invalid rows.
- Invalid rows are excluded. After the user confirms the preview, all accepted, non-duplicate rows are saved together as one import operation. A structural failure or cancellation creates no transactions.
- Repeated imports skip duplicate transactions and show an outcome summary, including imported, skipped, and rejected counts.
- Related source: CSV import (FR-05–19); BR-04, BR-11, and BR-12; UC-03; Scenario 1.

### Manage and categorise transactions

- A user can create a transaction manually, view transaction details, and edit transaction information.
- A user can create and manage categories and assign an available category to a transaction.
- The system validates transaction data and verifies that the user can access the transaction and category before saving changes.
- Related source: Manual transaction management (FR-11–15 in that subsection); Categories (FR-16–20); BR-01 and BR-02; UC-04–07; Scenario 2.

### Monthly income and expense summary

- A user can view a summary for a selected month, including income and expenses and totals by category.
- The summary is calculated from the same saved transaction records shown in the transaction list; it is not a separate report dataset.
- Related source: Analysis and reporting (FR-21, FR-22, FR-25, and FR-26); BR-05; UC-08.

## 2. Useful but Deferred

These capabilities are part of the broader product vision, but are not required to validate the MVP's individual import, categorisation, and analysis workflow:

- Creating and tracking budgets (FR-23–24; UC-09).
- Family groups, invitations, membership management, shared data, and family dashboards or reports (family requirements FR-26–32; BR-06–08; UC-10–14; Scenario 3).
- Annual reports and aggregation of monthly reports into yearly reports (FR-27 and FR-29–30; BR-09–10).
- Removing or archiving transactions and maintaining edit history (Manual transaction management FR-14–15; UC-05).
- Supporting additional CSV formats or user-defined column mappings beyond the initially documented format (CSV import FR-07; open decision 4 in requirements.md).

Deferred does not mean excluded from the product direction. These features should be reconsidered after the individual workflow is usable and the related product decisions are resolved.

## 3. Architecture and Data-Model Influences

The MVP should remain simple, but these requirements affect data ownership, persistence, or future extension and should be considered before implementation:

- **User ownership and authorization:** Transactions and personal categories need an owner relationship, and access checks must enforce isolation (FR-03–04; BR-01–02).
- **Transaction representation:** Persist the information needed for the supported CSV and manual workflows, including date, description, amount, category, and applicable counterparty or notes. Amount precision, currency, and sign conventions must be decided before defining the stored representation (Data Requirements; open decisions 7 and 10).
- **Category ownership:** The MVP needs a clear personal-category ownership rule. Family-shared categories are deferred; their future ownership model is not decided (Categories FR-16–20; open decision 5).
- **Import operation and provenance:** Keep enough import metadata to associate accepted transactions with an import and report its outcome. Confirmation must not leave a partially saved set of accepted rows if persistence fails (CSV import FR-17–19; UC-03).
- **Duplicate handling:** A consistent duplicate check is required for re-imports (FR-15–16; BR-11), but its identity rule must be selected before implementation. Consider that separate legitimate transactions can share date, amount, and description.
- **Reporting source:** Monthly summaries aggregate canonical saved transactions, aligning with BR-05. This is the MVP decision; FR-28 and BR-10 describe monthly reports as generated from CSV data, so that broader report requirement should be reconciled before it is treated as a separate report workflow.
- **Future family access:** Family membership, permissions, automatic inclusion versus selected sharing, and edit rights are not part of the MVP. They will affect authorization and potentially ownership relationships, so resolve them before adding family features (family FR-26–32; BR-06–08; open decisions 5 and 9).

## 4. Decisions to Resolve Before Implementation

The MVP scope above does not silently settle details that are still open in the source documents:

1. Which CSV format and encoding are supported first, and whether the mapping is fixed or configurable.
2. The duplicate identity rule and how legitimate repeated transactions are distinguished.
3. Supported currency or currencies, decimal precision, and amount/sign conventions.
4. Whether categories are strictly personal in the MVP and what happens when an assigned category is changed or removed.
5. Deployment assumptions and the required account-verification or recovery behavior.

The import and reporting choices defined in this MVP resolve the source-document conflicts for this release: valid rows may be imported despite rejected rows, but only after confirmation and as one atomic operation; monthly summaries use saved transactions. These choices do not require changing the existing source documents.

## 5. MVP Completion Criteria

The MVP is ready for evaluation when an individual user can:

- create an account and securely access their own data;
- import a supported CSV, inspect the preview and rejected rows, confirm the import, and see an accurate result summary;
- avoid creating duplicate transactions through a repeated import;
- create, view, edit, and categorise transactions;
- view a monthly income and expense summary by category based on the transactions in their account.
