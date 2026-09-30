# Project Context

## Overview

Thraa is a web application for practicing stock trading with virtual money. It provides a realistic learning environment without placing real orders or moving real funds.

## Problem and goal

New investors and students may want to understand order flows and portfolio tracking without financial risk. Thraa simulates the core workflow: market quote lookup, buy/sell requests, virtual cash movements, holdings, and order outcomes.

## Intended users

- Registered users practicing simulated investing
- Project administrators or maintainers who configure and monitor the application
- University evaluators reviewing the microservices design and implementation

## Core user journey

1. Register and log in.
2. View a supported stock's latest available quote.
3. Review available virtual cash and current holdings.
4. Submit a simulated buy or sell order.
5. Receive an accepted/rejected/executed status.
6. Review wallet ledger, order history, and portfolio summary.

## Assumptions

- All balances and trades are simulated.
- The app is not a broker and does not place real-market orders.
- A third-party market-data provider may be used, subject to access, terms, and course constraints.
- Quote availability and freshness can vary; the UI and API must communicate stale or unavailable data clearly.
- Initial implementation should prioritize correctness and clarity over sophisticated trading features.

## Success criteria

- Users can authenticate and access only their own private data.
- A valid buy order cannot spend more virtual cash than is available.
- A valid sell order cannot sell more shares than the user owns.
- Retrying the same order request does not create duplicate financial effects.
- Portfolio and wallet views reflect completed simulated trades consistently.
- Core flows are covered by automated tests.
