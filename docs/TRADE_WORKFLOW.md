# Simulated Trade Workflow

## Purpose

Define a simple and testable order workflow. This is a simulation, not a connection to a real broker.

## Proposed buy flow

1. Client sends `POST /orders` with symbol, side, quantity, and an idempotency key.
2. Trading Service authenticates the user and validates the request.
3. Trading Service checks whether the idempotency key has already been processed; if so, return the existing result.
4. Trading Service retrieves a sufficiently fresh quote from Market Data Service.
5. Trading Service calculates the estimated order cost using the agreed price and rounding policy.
6. Trading Service asks Wallet Service to reserve/debit the required virtual cash using a unique order reference and an atomic conditional update.
7. If the wallet operation fails, reject the order without creating a duplicate debit.
8. Trading Service records the simulated execution and trade result.
9. Portfolio Service is updated through its API or a documented event/compensation mechanism.
10. Notification Service records the outcome.
11. Client can retrieve the final order, wallet, and portfolio state.

## Proposed sell flow

1. Authenticate and validate symbol, quantity, and idempotency key.
2. Check the idempotency key and retrieve a sufficiently fresh quote.
3. Verify that the user owns enough shares to sell.
4. Record the simulated execution.
5. Credit virtual cash with a unique ledger reference.
6. Update the position and record the outcome.
7. Create an in-app notification.

## Important distributed-systems limitation

A call to Wallet Service and a separate call to Portfolio Service cannot be made atomic simply by calling them sequentially. A service may fail after another service has committed.

For the first version:
- Use explicit order states and record each step.
- Make wallet operations idempotent.
- Do not retry a financial operation without the same stable business reference.
- Define how incomplete orders are retried, rejected, or compensated.
- Add tests for failure between steps.

If the course requires event-driven communication, consider an outbox pattern and a message broker. Otherwise, keep the first implementation simpler and document the limitation.

## Quote and execution policy to decide

The team must decide:
- Which price is used for simulated execution (e.g. latest available quote or another documented convention)
- Maximum acceptable quote age
- Behavior when provider data is missing or stale
- Whether fees or slippage are simulated
- Whether market hours matter
- Whether fractional shares are supported

The UI must describe execution as simulated and must not imply an order was sent to a real exchange.
