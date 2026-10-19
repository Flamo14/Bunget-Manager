# Budget Manager Architecture

## Status and Scope

This document proposes an initial architecture for the Budget Manager MVP before implementation. It compares two application shapes and records the recommended direction without specifying a complete system design where product decisions are still open.

The MVP is an individual user's workflow: import and review CSV transactions, manage and categorise transactions, and view monthly summaries. Family sharing, budgets, and annual reports are deferred. See [mvp.md](mvp.md) for scope and [requirements.md](requirements.md) for the wider requirements and open decisions.

## Options Compared

### Option A: Django Server-Rendered Monolith

- Python and Django provide the backend and server-side web framework.
- Django Templates render HTML pages. Small amounts of browser-side JavaScript may be used where an interaction requires it, but a separate frontend application is not part of this option.
- PostgreSQL stores persistent application data.
- Django ORM and migrations manage database access and schema changes.
- The application and database run locally. Docker Compose is an optional way to provide a repeatable local database and application environment, not a prerequisite for choosing Django.

### Option B: Separate Frontend and Backend API

- A distinct frontend application renders the user experience and communicates with a backend through an API.
- The backend owns validation, authorization, business rules, and persistence; PostgreSQL and Django ORM/migrations could still be used on the backend.
- The frontend framework and API style are intentionally unspecified. Selecting this option would require choosing them and defining the API and authentication boundary.

## Comparison

| Criterion | Django server-rendered monolith | Separate frontend and backend API |
| --- | --- | --- |
| Complexity for a beginner | Lower. One primary application, one language for server-side work, and Django's integrated routing, forms, templates, authentication primitives, ORM, and migrations. HTML, CSS, and basic JavaScript remain necessary web skills. | Higher. The developer must build and debug two applications and their integration, define API contracts, coordinate validation and errors across the boundary, and handle browser/server authentication and cross-origin concerns where applicable. |
| Maintainability and testing | Clear module boundaries can be kept inside one Django project. Django's test tools support request, form, model, and integration tests without a separately deployed client. Server-rendered pages make end-to-end workflows straightforward to exercise. Avoid putting all business rules in views or templates. | The frontend and backend can be independently structured and tested, and the API can serve multiple clients. However, changes cross a contract boundary; API contract, integration, frontend, and end-to-end tests become important. This separation helps when independent release or client needs justify its cost. |
| MVP fit | Strong. The current MVP is a browser-based workflow and does not require multiple clients or a rich client-side application. Django provides a short path from authenticated form submission to validated persisted data and rendered results. | Weaker for the stated MVP. It introduces an API and client application before there is a demonstrated need for them. It may be justified if a mobile client, public API, or highly interactive client becomes a confirmed requirement. |
| Further development | A modular monolith can add features while retaining a single deployable application. A deliberate API can be introduced later if another client or integration requires one; it is not necessary to build the API pre-emptively. | Supports multiple independently developed clients and separate deployment/scaling of client and server. It does not by itself solve domain modularity or authorization, and those capabilities require additional design and operational work. |
| Additional technologies and concepts | Python, Django, HTML, CSS, basic JavaScript as needed, SQL/PostgreSQL fundamentals, and optionally Docker/Compose. | All technologies needed by the Django backend, plus a frontend framework and its language/tooling, API design and serialization, API authentication, CORS/deployment configuration where applicable, and cross-application contract/integration testing. Exact choices cannot be assessed until a frontend and API style are selected. |
| Main trade-off | Less infrastructure and fewer moving parts, in exchange for a closer coupling between page rendering and the Django application. | Stronger client/server separation and client choice, in exchange for more code, integration surfaces, and concepts to learn and maintain. |

## Recommendation

Use Option A for the MVP: a Django server-rendered monolith with Django Templates, PostgreSQL, Django ORM, and migrations. Keep application responsibilities separated in Django modules, but deploy and test the MVP as one application. Run the app and database locally; consider Docker Compose for reproducible local setup after deciding which contributors and environments the project must support.

This recommendation matches the current product: the MVP is a web application with form-based import, transaction management, and monthly reports. The existing requirements do not specify a mobile application, a public API, multiple independent clients, or a need to deploy the frontend separately. Option A minimizes the number of technologies to learn while still providing a realistic, testable architecture suitable for a portfolio project.

This is not a claim that a separate frontend/API is inherently less maintainable. It is a scope decision: accept that the presentation layer is served by Django now, and reconsider a client/API split only when a concrete product or operational need justifies its additional boundary.

## Proposed Logical Shape

Keep these responsibilities distinct within the Django application; this is a logical outline, not a final package or database schema:

- **Presentation:** Django views, forms, templates, and limited browser-side behavior.
- **Application/domain behavior:** transaction validation, CSV preview and confirmation workflow, categorisation rules, authorization checks, and report aggregation.
- **Persistence:** Django ORM models and migrations backed by PostgreSQL.
- **Tests:** unit tests for domain rules and focused integration/request tests for persistence, permissions, CSV import confirmation, and monthly summaries.

The import confirmation flow must persist the accepted rows as one operation. Monthly summaries use canonical saved transaction records rather than a separate report data source, as selected in the MVP document. Do not infer a final transaction schema until currency, amount semantics, duplicate identity, and category ownership are decided.

## Decisions to Record

### Record in this document

- The MVP boundary and deferred capabilities, kept aligned with `mvp.md`.
- The selected application shape and which product needs would justify revisiting it.
- The intended local development workflow, including whether Docker Compose is required, optional, or out of scope.
- High-level Django responsibility boundaries and testing expectations; update these only as implementation details are decided.
- Deployment target and operational assumptions once the user decides whether this remains a local learning application or is hosted for real users.
- Security and privacy expectations for authentication, session handling, secrets, backups, and access to financial data, without treating framework defaults as a complete security policy.

### Record as ADRs

ADRs capture decisions with meaningful alternatives and consequences. Proposed ADRs for this project:

1. **ADR-0001: Server-Rendered Django Monolith** — choose Django Templates and one deployable application for the MVP rather than a separate frontend and API.
2. **ADR-0002: PostgreSQL for Persistent Storage** — choose a relational database and Django ORM/migrations for the system of record, including the rationale and local-development implications.
3. **Future ADR, if needed: Local Development Environment** — decide whether to run services directly, use Docker Compose for the database, or containerize both application and database. Do not add Docker solely for appearance; weigh repeatability against learning and setup cost.

ADRs should remain short and record context, the decision, status, alternatives considered, and consequences. Keep a decision marked proposed until the project owner accepts it.

## Decisions Still Needed Before Implementation

These affect the architecture, data model, or safe operation and are not settled by this recommendation:

- **Deployment and users:** Is the first version local-only or internet-hosted, and is it for one developer/test user or multiple real users? This determines operational security, backup, availability, and deployment needs.
- **Authentication policy:** Decide email verification, password recovery, and session expectations. Django provides primitives, but the required product policy is still open.
- **CSV support:** Choose the first format, encoding, required fields, and fixed versus configurable mapping.
- **Financial data semantics:** Decide supported currencies, decimal precision, amount/sign convention, and treatment of transfers/refunds before settling field constraints and report calculations.
- **Duplicate identity:** Choose the duplicate comparison rule and how legitimate transactions that otherwise look identical are handled.
- **Category lifecycle:** Confirm personal category ownership for the MVP and define the effect of renaming or removing a category already assigned to transactions.
- **Family extension:** Before implementing family features, settle membership and permission rules, whether sharing is automatic or selective, and ownership of shared categories and budgets. Do not introduce family abstractions into the MVP schema without a justified requirement.
- **Test and quality bar:** Select the required automated test layers and any coverage, linting, or type-checking expectations for the portfolio project.

## Revisit Triggers

Reconsider a separate frontend/API if the product gains a confirmed mobile or additional independent client, a public or partner API, a frontend team that must release independently, or client-side interaction needs that server-rendered pages cannot meet economically. Revisit the persistence choice only if measured requirements or new domain constraints show PostgreSQL is unsuitable; do not change it based on speculation.