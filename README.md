# Thraa — University Project

Thraa is a microservices-based **paper-trading stock simulation platform**. Users register, view stock quotes, place simulated buy/sell orders with virtual money, track holdings, and receive order notifications.

> **Scope:** This is a university simulation project. It does not handle real-money deposits or withdrawals, custody real assets, or execute real brokerage trades.

## Repository organization

The repository is divided into three main areas:

```text
thraa/
├── frontend/       # Web client
├── api-gateway/    # Single public backend entry point
├── microservices/  # NestJS backend services
├── docs/
├── infra/
└── scripts/
```

The full suggested folder layout is in `docs/REPOSITORY_STRUCTURE.md`.

## Proposed technology stack

- **Backend and API Gateway:** NestJS + TypeScript
- **Primary database:** PostgreSQL
- **Database toolkit:** Prisma (proposed)
- **Cache:** Redis (optional, mainly for quote caching)
- **Service communication:** REST/HTTP for synchronous calls; RabbitMQ for asynchronous events
- **Real-time updates:** Socket.IO over WebSockets
- **Authentication:** JWT
- **API documentation:** Swagger / OpenAPI
- **Testing:** Jest and integration tests
- **Local environment:** Docker Compose
- **Frontend framework:** to be confirmed by the team/course

## Microservices

- Identity Service
- Market Data Service
- Trading Service
- Wallet Service
- Portfolio Service
- Notification Service

## Documentation map

- `docs/PROJECT_CONTEXT.md` — purpose, users, assumptions
- `docs/MVP_SCOPE.md` — in-scope and out-of-scope features
- `docs/ARCHITECTURE.md` — frontend, gateway, services, and overall design
- `docs/MESSAGING.md` — REST, RabbitMQ, WebSockets, and event flow
- `docs/DOMAIN_MODEL.md` — core entities and invariants
- `docs/API_CONTRACTS.md` — initial endpoint proposal
- `docs/TRADE_WORKFLOW.md` — simulated order processing
- `docs/IMPLEMENTATION_PLAN.md` — suggested implementation sequence
- `docs/SECURITY_AND_RELIABILITY.md` — security and consistency rules
- `docs/REPOSITORY_STRUCTURE.md` — requested repo organization

## First steps

1. Review the course rubric and confirm mandatory technologies/patterns.
2. Agree on frontend framework, service boundaries, database ownership, and team responsibilities.
3. Create the repo with `frontend/`, `api-gateway/`, and `microservices/` as top-level areas.
4. Implement a small end-to-end vertical slice before expanding every service.
