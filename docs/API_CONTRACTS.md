# Initial API Contracts

These are proposed endpoint shapes, not a finalized OpenAPI contract. All private routes require JWT authentication unless noted.

## Identity Service

| Method | Endpoint | Purpose |
|---|---|---|
| POST | `/auth/register` | Create an account |
| POST | `/auth/login` | Authenticate and issue token |
| GET | `/auth/me` | Return current authenticated user |

## Market Data Service

| Method | Endpoint | Purpose |
|---|---|---|
| GET | `/quotes/{symbol}` | Retrieve normalized quote |
| GET | `/quotes` | Optional quote lookup for supported symbols |

Quote responses should include symbol, price, currency, source, `asOf`, and freshness metadata.

## Wallet Service

| Method | Endpoint | Purpose |
|---|---|---|
| GET | `/wallet` | Current user's virtual cash account |
| GET | `/wallet/ledger` | Current user's ledger history |

Internal balance mutation endpoints, if needed between services, must be authenticated service-to-service and must enforce idempotency. Do not expose arbitrary balance mutation to normal clients.

## Trading Service

| Method | Endpoint | Purpose |
|---|---|---|
| POST | `/orders` | Submit simulated buy/sell order |
| GET | `/orders` | List current user's orders |
| GET | `/orders/{orderId}` | Read one owned order |
| GET | `/trades` | List current user's executed trades |

Example request:

```json
{
  "symbol": "AAPL",
  "side": "BUY",
  "quantity": "2",
  "idempotencyKey": "client-generated-unique-key"
}
```

Use a decimal string for quantity in the API if fractional quantities are supported. Otherwise use an integer. The server must derive `userId` from the verified JWT rather than accepting it as trusted request input.

## Portfolio Service

| Method | Endpoint | Purpose |
|---|---|---|
| GET | `/portfolio` | Summary of current user's portfolio |
| GET | `/portfolio/positions` | List positions |
| GET | `/portfolio/positions/{symbol}` | One position |

State whether valuation is estimated and expose quote timestamps or stale-data indicators.

## Notification Service

| Method | Endpoint | Purpose |
|---|---|---|
| GET | `/notifications` | List current user's notifications |
| PATCH | `/notifications/{id}/read` | Mark an owned notification as read |

## Common API behavior

- Use consistent HTTP status codes and a shared error response format.
- Validate DTOs at the NestJS boundary.
- Apply pagination to potentially large history lists.
- Verify ownership for every user-scoped resource.
- Avoid returning password hashes, secrets, or internal stack traces.
- Document internal-only endpoints separately from public client endpoints.
