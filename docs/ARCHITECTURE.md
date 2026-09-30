# Architecture

## High-level structure

Thraa is organized as one repository with three top-level application areas:

- `frontend/`: the web client.
- `api-gateway/`: the public backend entry point.
- `microservices/`: backend services implemented with NestJS + TypeScript.

## Request flow

```text
User
 |
 v
Frontend
 |
 v
API Gateway
 |
 +--> Identity Service
 +--> Market Data Service
 +--> Trading Service
 +--> Wallet Service
 +--> Portfolio Service
 +--> Notification Service
```

The frontend calls the API Gateway rather than calling each microservice directly. The gateway routes requests and handles cross-cutting concerns, but domain logic remains inside the owning microservice.

## Proposed microservices

| Service | Responsibilities | Data ownership |
|---|---|---|
| Identity Service | Registration, login, identity, token-related operations | User and authentication data |
| Market Data Service | Provider adapter, normalized quotes, freshness, optional cache | Optional quote snapshots |
| Trading Service | Order validation/orchestration, order state, simulated execution, trade history | Orders and trade records |
| Wallet Service | Virtual cash accounts, ledger entries, atomic balance movements | Wallet accounts and ledger |
| Portfolio Service | Positions, cost basis, valuation summaries | Position records/snapshots |
| Notification Service | In-app notifications and read state | Notifications |

## API Gateway responsibilities
- Expose the public API to the frontend.
- Route requests to the relevant service.
- Apply shared request handling, request IDs, and consistent error mapping.
- Optionally apply rate limiting and authentication checks.
- Never become the owner of domain business logic.
- Never directly read or write microservice databases.

Microservices must still validate service requests, enforce their own business rules, and protect user-owned resources. Gateway authentication alone is not a substitute for service-level authorization.

## Service communication
- **REST/HTTP** handles synchronous request/response calls.
- **RabbitMQ** carries asynchronous backend domain events such as `OrderExecuted` and `OrderRejected`.
- **Socket.IO over WebSockets** pushes quotes, order status, and in-app notifications to connected frontend users through the API Gateway.

See `docs/MESSAGING.md` for event ownership, delivery rules, authentication, and the order notification flow. Do not assume a series of calls across services is one atomic transaction.

## Data ownership
Each service owns its data and migrations. Other services use APIs instead of querying another service's tables. For local development, one PostgreSQL server with separate logical databases may be acceptable if the course rubric permits it.

## Technology proposal
- Backend services and API Gateway: NestJS + TypeScript
- Primary database: PostgreSQL
- Database toolkit: Prisma (proposed)
- Message broker: RabbitMQ for asynchronous service events
- Real-time client updates: Socket.IO over WebSockets
- Cache / multi-instance socket adapter: Redis only if needed
- Authentication: JWT
- API documentation: Swagger / OpenAPI
- Testing: Jest and integration tests
- Local environment: Docker Compose
- Frontend framework: confirm with the team/course

## Distributed workflow warning
A trading workflow spanning Trading, Wallet, and Portfolio is not a single database transaction. Use explicit order states, idempotency keys, unique ledger references, and a documented recovery/compensation strategy. Add an outbox/event pattern only if required or justified.
