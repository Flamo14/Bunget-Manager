# ADR-0002: PostgreSQL for Persistent Storage

- **Status:** Proposed
- **Date:** 2026-10-19
- **Decision owners:** Project owner

## Context

The MVP needs persistent user, transaction, category, and CSV import outcome data. Transaction import must avoid partial persistence after confirmation, while reports aggregate the same canonical transaction records used by the transaction list. The product also requires ownership and authorization boundaries around financial data.

The project is a learning and portfolio project, and its proposed application framework is Django.

## Decision

Use PostgreSQL as the proposed persistent system of record, accessed through Django ORM. Use Django migrations to evolve the database schema. Keep the database available locally during development; whether it is run directly or through Docker Compose is a separate decision.

## Alternatives Considered

### SQLite

SQLite can reduce initial setup for a local demonstration and is integrated with Python tooling. It is a reasonable option for a strictly local prototype. PostgreSQL is preferred here because the project is intended to demonstrate a realistic persistent web application and because using the intended relational database from the outset reduces database-specific differences between development and any later hosted environment. The project has not established production deployment, so this is a learning-oriented choice rather than a scale requirement.

### PostgreSQL

PostgreSQL provides a full relational database for transaction, user, and category relationships and supports reliable transactional writes. It adds local setup and database administration concepts, which can be managed manually or with an optional Compose setup.

### Non-relational database

No current requirement calls for document-oriented storage or schema flexibility that would outweigh the relational relationships and integrity needs. Introducing a non-relational database would require additional justification and is not recommended for this MVP.

## Consequences

- Django ORM and migrations provide a consistent path for defining and evolving relational data.
- Confirmed imports can be handled within a database transaction so accepted rows are not partially persisted.
- Developers must learn PostgreSQL setup, connections, backups appropriate to the chosen deployment, and relational constraints in addition to Django.
- The project should avoid relying on behavior that differs between SQLite and PostgreSQL if a second database is later used for quick tests.
- PostgreSQL does not resolve open product decisions such as currency semantics, duplicate identity, or category ownership; those must be settled before finalizing the schema.
- This decision does not assert a production hosting provider, availability target, backup schedule, or Docker requirement.

## Review Conditions

Revisit this decision if the project is explicitly constrained to a local-only, zero-setup demonstration, or if validated requirements show PostgreSQL is unsuitable. Any change should account for migration, integrity, and test-environment consequences.