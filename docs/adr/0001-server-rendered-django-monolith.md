# ADR-0001: Server-Rendered Django Monolith

- **Status:** Proposed
- **Date:** 2026-10-19
- **Decision owners:** Project owner

## Context

Budget Manager is a learning and portfolio project. Its current MVP is a browser-based workflow for one user at a time: authenticate, import and review CSV transactions, manage and categorise transactions, and view monthly summaries. The requirements do not establish a mobile client, public API, multiple independently deployed clients, or a need for separate frontend releases.

The project owner is considering Python, Django, Django Templates, PostgreSQL, Django ORM, and migrations. The alternative under consideration is a separate frontend application communicating with a backend API.

## Decision

For the MVP, use a single Django application that renders pages with Django Templates. Keep presentation, domain behavior, and persistence responsibilities distinct within the application, but do not create a separate frontend application or backend API pre-emptively.

Use PostgreSQL with Django ORM and migrations as the proposed persistent-storage direction; the database choice is described separately in ADR-0002. The local development method, including whether to use Docker Compose, remains open.

## Alternatives Considered

### Separate frontend and backend API

This would provide a clear client/server contract and support independent clients or releases. It also requires selecting and learning a frontend framework and API approach, defining API authentication and error behavior, and testing the integration between two applications. Those costs are not justified by the current MVP requirements.

### Django server-rendered monolith

This uses one primary application and Django's integrated request handling, templates, forms, authentication primitives, ORM, and migrations. It reduces the number of technologies and deployment components while remaining suitable for the current workflows. The presentation layer is more closely coupled to the Django application, and independent clients would need a deliberate API later.

## Consequences

- A beginner can focus on Python, Django, HTML, CSS, and the database before taking on a frontend framework and API integration.
- The MVP can exercise complete user flows through Django request and integration tests without maintaining a separate client build and test system.
- Django modules must preserve clear responsibility boundaries; a monolith should not become a collection of views containing all business logic.
- Server-rendered pages are the default. Add client-side JavaScript only where a concrete interaction needs it.
- If a mobile client, public API, or independently released frontend becomes a confirmed need, design an API at that point and record the change in a new ADR.
- This decision does not select deployment hosting, Docker usage, a complete module structure, or the final data schema.

## Review Conditions

Revisit this decision if requirements add another independent client, public API consumers, independent frontend release needs, or interaction requirements that cannot be met economically with server-rendered pages.