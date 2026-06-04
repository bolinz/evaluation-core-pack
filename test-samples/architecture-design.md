# Microservice Architecture Design: Payment Platform

## Overview

A new payment processing platform with 4 microservices.

## Architecture

```
User → API Gateway → Auth Service → Payment Service → Ledger Service
                                   ↘                    ↗
                                Notification Service
```

### Services

1. **Auth Service** — Handles user authentication, API keys, rate limiting
2. **Payment Service** — Processes payments, manages transactions, retry logic
3. **Ledger Service** — Records all transactions, generates reports
4. **Notification Service** — Sends emails, webhooks, push notifications

### Data Flow

1. User authenticates via Auth Service → receives JWT
2. User initiates payment → Payment Service validates → charges payment provider
3. Payment Service publishes event → consumed by Ledger (record) + Notification (email)
4. Ledger Service stores immutable transaction log

### Key Decisions

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Communication | Async via message queue | Decoupling, resilience |
| Database | PostgreSQL per service | Data isolation, no shared DB |
| Payment provider | Stripe | Proven, good API, global coverage |
| Deployment | Kubernetes | Scalability, self-healing |

### Concerns

- Auth Service is a single point of failure — consider HA setup
- No circuit breaker pattern between services
- Payment retry logic is in the Payment Service — what if Payment Service is down?
- No distributed tracing implemented yet
