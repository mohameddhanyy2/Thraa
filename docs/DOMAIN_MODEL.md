# Domain Model

This is an initial model. Refine names, relationships, and fields during implementation.

## User (Identity Service)
- `id`: UUID
- `email`: unique
- `passwordHash`: stored password hash
- `createdAt`, `updatedAt`

Never store plaintext passwords. A user ID used for authorization must come from a verified identity/token context.

## Quote (Market Data Service)
- `symbol`: normalized symbol, e.g. `AAPL`
- `price`: decimal
- `currency`: string
- `asOf`: timestamp from provider
- `receivedAt`: timestamp when our service received it
- `source`: provider identifier

Quote values can become stale. Responses should expose timestamps and avoid implying that cached data is live.

## Order (Trading Service)
- `id`: UUID
- `userId`: authenticated owner identifier
- `symbol`
- `side`: `BUY` or `SELL`
- `quantity`: positive decimal or integer, depending on fractional-share decision
- `status`: proposed states include `PENDING`, `PROCESSING`, `EXECUTED`, `REJECTED`, `FAILED`
- `requestedAt`, `completedAt`
- `executionPrice`: decimal, nullable until executed
- `executedQuantity`: decimal/integer
- `rejectionReason`: nullable
- `idempotencyKey`: unique per user/request scope

Keep state transitions explicit and reject invalid transitions.

## Trade (Trading Service)
- `id`: UUID
- `orderId`: unique reference to the order that generated it
- `userId`
- `symbol`, `side`, `quantity`
- `price`: decimal
- `executedAt`

For a simple market-style simulator, an executed order may produce one trade record. If partial fills are not supported, document that assumption.

## Virtual Cash Account (Wallet Service)
- `id`: UUID
- `userId`: unique for the initial single-currency account model
- `currency`
- `availableBalance`: decimal
- `createdAt`, `updatedAt`

## Ledger Entry (Wallet Service)
- `id`: UUID
- `accountId`
- `referenceType`: e.g. `ORDER`
- `referenceId`: order ID
- `entryType`: e.g. `BUY_DEBIT`, `SELL_CREDIT`, `INITIAL_CREDIT`, `REVERSAL`
- `amount`: decimal with a documented sign convention
- `createdAt`

Use a unique constraint on the business reference/entry type where appropriate to prevent duplicate effects. Define one consistent sign convention and document it.

## Position (Portfolio Service)
- `id`: UUID
- `userId`
- `symbol`
- `quantity`
- `averageCost`: decimal
- `updatedAt`

A unique constraint on `(userId, symbol)` is appropriate for one aggregate position per symbol.

## Notification (Notification Service)
- `id`: UUID
- `userId`
- `type`
- `title`, `message`
- `relatedOrderId`: nullable
- `createdAt`
- `readAt`: nullable

## Monetary and quantity rules

- Use PostgreSQL `NUMERIC` and suitable decimal handling in application code for monetary values; do not use JavaScript floating-point arithmetic for balance calculations.
- Validate quantity is greater than zero.
- Define quantity precision only after deciding whether fractional shares are supported.
- Do not allow a wallet balance or position quantity to become negative.
- Define a consistent currency and price-rounding policy.
