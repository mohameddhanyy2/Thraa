# Messaging and Real-Time Communication

## Chosen approach

Thraa uses three complementary communication mechanisms:

- **REST/HTTP** for normal request/response operations.
- **RabbitMQ** for asynchronous events between backend services.
- **WebSockets with Socket.IO** for server-pushed updates to connected frontend clients.

These mechanisms serve different purposes and should not be treated as interchangeable.

## REST/HTTP

The frontend calls the API Gateway. The gateway routes requests to the owning service. Backend services may call another service's internal REST API when an immediate response is required. Use request timeouts, bounded retries where safe, and idempotency for operations that can be retried.

## RabbitMQ events

Use RabbitMQ for events that other services can process asynchronously. Start with a small, explicit event set:

- `OrderExecuted`
- `OrderRejected`
- `WalletBalanceChanged` (only if another service needs this event)
- `PortfolioUpdated` (only if another service needs this event)

The Trading Service owns order state and publishes order outcome events. The Notification Service consumes order events, stores an in-app notification, and then asks the real-time layer to push the notification to the relevant user. Portfolio and wallet event subscriptions should be added only where needed by the agreed trade workflow; avoid publishing redundant events.

Implementation requirements:

- Define versioned event payloads and stable event IDs.
- Consumers must be idempotent because delivery may be repeated.
- Acknowledge messages only after successful processing; configure retries and a dead-letter queue.
- Do not assume publishing an event and committing a database transaction are automatically atomic. For stronger reliability, use a transactional outbox when justified by the course requirements and implementation scope.
- Never put passwords, JWTs, or other secrets in event payloads.

## WebSockets / Socket.IO

The API Gateway hosts the Socket.IO gateway for the initial implementation. Clients authenticate the socket connection with a verified JWT and join only their own user room, such as `user:<userId>`. Never trust a user ID supplied by the client without verifying it against the authenticated identity.

Use WebSockets for:

- live quote updates, where the quote provider and project scope support them;
- order status updates;
- in-app notification delivery;
- optional portfolio refresh events.

Persist important notifications and order state before pushing an update. WebSocket delivery is not durable: users may be offline, so clients must fetch missed notifications and current state through REST when reconnecting.

For local development with one gateway instance, an in-memory Socket.IO setup is sufficient. If multiple gateway instances are introduced, use a shared Socket.IO adapter (for example, a Redis adapter) or another supported fan-out mechanism so events can reach clients connected to different instances. Redis is not required solely for the single-instance MVP.

## Example order event flow

1. Frontend submits a buy/sell order through REST to the API Gateway.
2. Trading Service validates the request and coordinates the simulated trade with Wallet Service using the documented order workflow.
3. After the outcome is recorded, Trading Service publishes `OrderExecuted` or `OrderRejected` to RabbitMQ.
4. Notification Service consumes the event idempotently and persists an in-app notification.
5. The real-time layer emits the notification to the authenticated user's Socket.IO room.
6. If the user is disconnected, the notification remains available through the notifications REST endpoint.

## Ownership boundaries

- RabbitMQ carries backend domain events; it is not a database and does not replace service-owned persistence.
- Socket.IO pushes updates to clients; it is not the source of truth for orders, balances, positions, or notifications.
- The API Gateway must not implement trading or wallet business logic.
- Do not broadcast private user data to public rooms or quote channels.
