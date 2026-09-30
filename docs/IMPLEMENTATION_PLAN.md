# Implementation Plan

Implement a thin end-to-end flow first, then expand. Adapt this sequence to the deadline and course rubric.

## Phase 1 — Confirm requirements
- Read the assignment rubric.
- Confirm required service count, gateway, broker, database separation, and deployment expectations.
- Agree on frontend, supported symbols, virtual starting balance, currency, and execution policy.
- Assign owners and define API contracts.

**Deliverable:** approved scope and architecture decisions.

## Phase 2 — Repository and foundations
- Create the repository/workspace and NestJS service skeletons.
- Set TypeScript, linting, formatting, environment-variable validation, and logging conventions.
- Set up Docker Compose for PostgreSQL and any required local dependencies.
- Configure Prisma and migrations if selected.
- Add Swagger and health endpoints.

**Deliverable:** services start locally and database migrations run.

## Phase 3 — Identity and Market Data
- Implement registration/login and JWT protection.
- Add user ownership guards and DTO validation.
- Build the market-data provider adapter behind an interface.
- Add a mock provider for predictable automated tests.
- Add quote timestamps and stale-data behavior.

**Deliverable:** authenticated user can retrieve a quote.

## Phase 4 — Wallet
- Create virtual accounts and initial-balance policy.
- Implement balance and ledger read endpoints.
- Implement safe, idempotent internal debit/credit operations.
- Add concurrency and duplicate-request tests.

**Deliverable:** wallet operations preserve balance invariants.

## Phase 5 — Trading
- Implement order creation and state transitions.
- Integrate quote lookup and wallet checks.
- Record simulated executions and trade history.
- Add idempotency and recovery behavior for partial failures.

**Deliverable:** end-to-end simulated buy and sell flows.

## Phase 6 — Portfolio and Notifications
- Update positions from completed trades.
- Calculate valuation using the documented quote policy.
- Create and list in-app notifications.
- Test ownership and stale quote handling.

**Deliverable:** user can inspect portfolio and order outcome.

## Phase 7 — Integration and presentation
- Add integration tests for full workflows and failure paths.
- Verify API documentation.
- Prepare seed/demo data and a reproducible demo.
- Document service boundaries, trade flow, known limitations, and setup instructions.
- Run the project from a clean checkout using the documented commands.

**Deliverable:** demonstrable, tested university project.
