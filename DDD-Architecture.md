# DDD Architecture

BlockSight follows Domain-Driven Design with strict boundary enforcement. Domain code has **zero infrastructure imports** — not by convention, but by automated rules that fail the build.

## Domain Structure

22 backend domains + 5 frontend domains, each following the Functional Core / Imperative Shell pattern:

### Backend Domains

| Category | Domains |
|----------|---------|
| Core Business | billing, subscription, auth, apikey |
| Blockchain | bitcoin-core, electrum, explorer |
| Data Processing | mempool-projection, transformation (7 transformers), analytics |
| Platform | webhook, fx, usage, prediction-observatory |
| Infrastructure | cache, websocket, monitoring, crm |

### Frontend Domains

| Domain | Purpose |
|--------|---------|
| search | Block/transaction/address search with type detection |
| websocket-bridge | WebSocket event handlers and state sync |
| datastore | Centralized state management with transformers |
| i18n | 25-language internationalization |
| subscription | Customer portal billing flows |

## Boundary Rules

Six DDD boundary rules enforced by `dependency-cruiser`:

| Rule | Constraint |
|------|-----------|
| R1 | Domain code must NOT import from infrastructure |
| R2 | Domain code must NOT import from application |
| R3 | Application code must NOT import from API/routes |
| R4 | Shared types are read-only from domain perspective |
| R5 | No circular dependencies between domains |
| R6 | Infrastructure adapters implement domain interfaces (ports) |

Violations are caught in CI and tracked in `STATE.md`.

## Functional Core / Imperative Shell

Each domain separates pure business logic (core) from side effects (infrastructure):

```
domain/billing/
  core/
    billing.service.ts      # Pure business logic (Functional Core)
    payment-watcher.service.ts
    invoice.repository.impl.ts  # Implements IInvoiceRepository port
  types/
    billing.types.ts        # Interfaces, types, ports (IInvoiceRepository, IBillingService)
  config/
    billing.config.ts       # Domain configuration
```

The `types/` directory defines **ports** (interfaces). The `core/` directory provides **adapters** (implementations). Infrastructure (PostgreSQL, Redis, Electrum) is injected through constructor parameters.

## Purity Tracking

Domain purity is measured and tracked:

- **Backend: 100.0% pure** (0 impure files across 501 domain files)
- **Frontend: F1-F10 phases complete** (zero runtime `console.*` in domain layers, all `Date.now()` injected)

Purity means:
- No direct `console.log` / `console.error` (use injected logger)
- No `Date.now()` or `new Date()` (use injected clock)
- No `process.env` access (use injected config)
- No `fetch()` or network calls (use injected service)

This makes every domain function **deterministic and testable** without mocks.

## The `@/domains/*` Path Alias

Frontend domains use a path alias for clean imports:

```typescript
// Instead of:
import { useSearch } from '../../../domains/search/hooks/useSearch';

// Use:
import { useSearch } from '@/domains/search';
```

New frontend domains are created when: 3+ components share logic, business rules exist, API integration is needed, and clear bounded context boundaries are identifiable.

## Dependency Injection

Services are wired through a DI container at startup (13-phase bootstrap):

1. Configuration validation
2. Database connection pool
3. Redis connection
4. Bitcoin Core RPC client
5. Electrum client
6. Cache service (L1 in-memory + L2 Redis)
7. Domain services (billing, subscription, auth)
8. Transformers (7 data pipeline stages)
9. WebSocket hub
10. Payment services (watcher, confirmation tracker)
11. Background schedulers
12. HTTP server
13. Health check readiness gate

Each service declares its dependencies as constructor parameters (ports). The bootstrap layer provides concrete implementations (adapters).
