# Security and Reliability

## Authentication and authorization
- Use a maintained password-hashing algorithm/library; never store plaintext passwords.
- Sign and verify JWTs securely and keep secrets outside source control.
- Derive user identity from a verified token.
- Enforce resource ownership on orders, trades, wallet, positions, and notifications.
- Protect internal service endpoints with an agreed service-to-service mechanism; do not assume public endpoints are safe because they are “internal” by convention.

## Validation
- Validate all request DTOs in NestJS.
- Normalize and validate stock symbols.
- Reject zero or negative quantities.
- Restrict accepted order sides and state transitions.
- Enforce maximum input lengths and sensible pagination limits.

## Financial-data correctness
- Use PostgreSQL NUMERIC and decimal-safe application handling for money.
- Do not calculate cash balances with JavaScript `number` floating-point arithmetic.
- Apply balance changes atomically with conditional updates or transactions inside Wallet Service.
- Use unique references and idempotency keys to prevent duplicate ledger effects.
- Prevent negative balances and negative positions.
- Document the rounding and currency policy.

## Distributed failure
- Calls across services are not a single atomic transaction.
- Set timeouts and bounded retries.
- Never retry a debit/credit with a new business reference if it is a retry of the same operation.
- Track intermediate order states and provide a recovery path for incomplete workflows.
- If asynchronous events are introduced, consider outbox/inbox or deduplication patterns.

## Market data
- Keep provider API keys in environment variables or a secrets manager.
- Apply timeouts and handle provider rate limits and outages.
- Include source timestamps and define a stale-quote threshold.
- Use mocked providers for automated tests.
- Do not claim quote data is real-time unless the provider/feed actually guarantees it.

## Operational basics
- Do not commit `.env` files or secrets.
- Use structured logs and request/correlation IDs where practical.
- Avoid logging passwords, tokens, or unnecessary personal data.
- Add health checks and actionable error messages without exposing stack traces.
