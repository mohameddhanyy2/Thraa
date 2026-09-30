# MVP Scope

## Included

### Accounts
- Registration and login
- JWT-protected endpoints
- Current-user endpoint

### Market data
- Retrieve quote by supported symbol
- Normalize provider responses into an internal format
- Cache quotes optionally
- Expose quote timestamp and freshness information

### Virtual wallet
- Create or initialize a virtual cash account for a user
- Read available virtual cash and ledger history
- Record every balance movement with a unique reference

### Simulated trading
- Submit market-style buy/sell requests
- Validate symbol, quantity, quote availability, cash, and holdings
- Track order status and execution details
- Support idempotent order submission

### Portfolio
- List positions and quantities
- Estimate market value using available quotes
- Show basic cost basis and unrealized profit/loss where supported by the agreed calculation policy

### Notifications
- In-app notifications for order outcomes
- List notifications and mark them as read

## Excluded from initial MVP

- Real-money deposits, withdrawals, payment processing, or customer funds
- Actual brokerage execution or custody of assets
- KYC and regulatory onboarding
- Paid plans, subscriptions, and billing
- Investment advice or personalized buy/sell recommendations
- Margin, short selling, options, futures, and other derivatives
- Complex order types such as stop-loss and limit orders unless required by the rubric
- Multi-currency conversion
- Fractional shares unless explicitly agreed
- Analytics dashboards beyond basic portfolio information
- Kafka/RabbitMQ or event-driven infrastructure unless the course requires it

## MVP acceptance checklist

- [ ] A user can register and log in.
- [ ] Protected routes reject missing or invalid tokens.
- [ ] A user can retrieve a quote for a supported symbol.
- [ ] A user can view their virtual balance.
- [ ] A buy order validates funds and records a consistent result.
- [ ] A sell order validates owned quantity and records a consistent result.
- [ ] Repeating an idempotent request does not double-debit or double-credit.
- [ ] Users cannot read or change another user's private records.
- [ ] Portfolio, wallet, and order history have automated tests.
