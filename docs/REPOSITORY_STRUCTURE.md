# Repository Structure

## Required top-level organization

The repository is organized into three clear areas:

1. `frontend/` — the user-facing web application.
2. `api-gateway/` — the single public backend entry point.
3. `microservices/` — independently organized backend services.

The backend services use **NestJS + TypeScript**. The frontend framework is still a team decision unless already specified by the course.

```text
thraa/
├── frontend/
│   ├── src/
│   ├── public/
│   ├── tests/
│   ├── package.json
│   └── README.md
│
├── api-gateway/
│   ├── src/
│   │   ├── modules/
│   │   ├── guards/
│   │   ├── filters/
│   │   ├── interceptors/
│   │   ├── config/
│   │   └── main.ts
│   ├── test/
│   ├── package.json
│   └── README.md
│
├── microservices/
│   ├── identity-service/
│   │   ├── src/
│   │   ├── prisma/
│   │   ├── test/
│   │   ├── package.json
│   │   └── README.md
│   ├── market-data-service/
│   │   ├── src/
│   │   ├── prisma/              # Only if this service persists data
│   │   ├── test/
│   │   ├── package.json
│   │   └── README.md
│   ├── trading-service/
│   │   ├── src/
│   │   ├── prisma/
│   │   ├── test/
│   │   ├── package.json
│   │   └── README.md
│   ├── wallet-service/
│   │   ├── src/
│   │   ├── prisma/
│   │   ├── test/
│   │   ├── package.json
│   │   └── README.md
│   ├── portfolio-service/
│   │   ├── src/
│   │   ├── prisma/
│   │   ├── test/
│   │   ├── package.json
│   │   └── README.md
│   └── notification-service/
│       ├── src/
│       ├── test/
│       ├── package.json
│       └── README.md
│
├── docs/
├── infra/
│   └── docker-compose.yml  # PostgreSQL and RabbitMQ for local development
├── scripts/
├── .env.example
├── .gitignore
└── README.md
```

## Responsibilities

### `frontend/`
- Renders pages and user interactions.
- Calls the API Gateway, not individual microservices directly.
- Stores no trusted business rules or secrets.
- Handles loading, validation feedback, API errors, and authentication state.

### `api-gateway/`
- Provides the single public API entry point.
- Routes requests to the appropriate service.
- Applies shared concerns such as request IDs, basic rate limiting, and consistent error responses where appropriate.
- Validates/authenticates incoming requests as designed, while services still enforce authorization and business invariants.
- Must not own trading, wallet, or portfolio business logic.
- Must not connect directly to service databases.

### `microservices/`
Each service owns its business logic, API, and data. Other services communicate with it through documented service APIs, not by reading its database tables.

- `identity-service/`: registration, login, and user identity.
- `market-data-service/`: quote provider integration, normalization, and freshness.
- `trading-service/`: orders, simulated execution, and trade history.
- `wallet-service/`: virtual balances and ledger.
- `portfolio-service/`: holdings and valuation.
- `notification-service/`: in-app notifications.

## NestJS service layout

A typical NestJS backend application can use this structure:

```text
src/
├── modules/
│   └── feature-name/
│       ├── dto/
│       ├── feature-name.controller.ts
│       ├── feature-name.service.ts
│       └── feature-name.module.ts
├── common/
├── config/
└── main.ts
```

Adapt each service's modules to its domain. Do not copy every folder into every app if it is not needed.

## Database ownership
- PostgreSQL is the proposed primary database.
- Each data-owning microservice should own its tables and migrations.
- For local development, one PostgreSQL server with separate logical databases can be used if the rubric permits it.
- Prisma is proposed for database access; keep each service's schema/migrations with that service.
- RabbitMQ handles asynchronous backend events.
- Socket.IO runs in the API Gateway for authenticated real-time client updates.
- Redis is optional for quote caching or a shared Socket.IO adapter if multiple gateway instances are used; it is not the source of truth for wallet balances, orders, or trades.

## API flow

```text
Browser
   |
   v
frontend/
   |
   v
api-gateway/
   |
   +--> identity-service
   +--> market-data-service
   +--> trading-service
   +--> wallet-service
   +--> portfolio-service
   +--> notification-service
```

The frontend should not call a microservice directly in the normal application flow. Internal service endpoints should not be exposed as public client endpoints. The API Gateway also authenticates Socket.IO connections and emits private events only to the corresponding authenticated user room.

## Monorepo note
This is a proposed monorepo layout, not a generated code scaffold. The team can use npm workspaces, pnpm workspaces, or separate package manifests depending on course expectations. Keep the three top-level areas intact even if the package tooling changes.
