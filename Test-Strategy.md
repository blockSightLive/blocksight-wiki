# Test Strategy

BlockSight's testing philosophy: **test on real data, not mocks**. E2E tests run against a live Bitcoin Core node with real blockchain data. When we assert that block 943,000 has the correct miner, we check against the actual Bitcoin network.

## Production Metrics (PL-C30)

| Metric | Result | Notes |
|--------|--------|-------|
| **E2E** | **98.1%** (566/577) | Live Bitcoin Core + Fulcrum + PostgreSQL |
| **Playwright** | **72.5%** (211/291) | 3 viewports, 41 specs |
| **k6 smoke** | **24/24** | 11th consecutive perfect score |
| **Chaos** | **10/10** | All phases pass |
| **Soak** | **0.00%** errors | 60-minute sustained load |
| **Coverage (BE)** | **92.54%** lines | 22 backend domains |
| **Coverage (FE)** | **93.47%** lines | 3 frontend domains |
| **Total tests** | **28,857+** | Across 1,425+ suites |

## The Testing Pyramid

```mermaid
graph TB
    A["Unit + Integration<br/>27,441 tests / 1,198 suites"] --> B["E2E (Live Blockchain)<br/>573 tests / 61 suites"]
    B --> C["Playwright Visual<br/>257 tests / 29 specs"]
    C --> D["k6 Performance<br/>24 checks (smoke/load/soak)"]
    D --> E["Chaos Engineering<br/>10 phases / 10 services"]

    style A fill:#1a365d,color:#fff
    style B fill:#2a4365,color:#fff
    style C fill:#2c5282,color:#fff
    style D fill:#2b6cb0,color:#fff
    style E fill:#3182ce,color:#fff
```

## Test Runners

| Runner | Purpose | Scale |
|--------|---------|-------|
| Jest (unit) | Domain logic, transformers, hooks, services | 28,857+ cases across 1,425+ suites |
| Jest (E2E) | Data accuracy, API contracts, WebSocket events | 577 tests against live infrastructure |
| Playwright | Visual regression, responsive layouts, user journeys | 291 tests across 41 specs |
| k6 | Load, soak, and performance baselines | 24 checks (smoke/load/soak profiles) |
| Chaos | Infrastructure resilience testing | 10 phases covering all dependencies |

## Why Real Data, Not Mocks

Mock-based testing hides real bugs. We learned this when mocked tests passed but production showed wrong fee estimates because the transformer silently dropped a field.

- **Data accuracy tests** compare API responses against Bitcoin Core RPC directly
- **Fee estimation** is validated against real mempool state
- **Congestion scores** run through the CEO's algorithm with live network data
- **Block enrichment** verifies miner labels, transaction counts, and fee totals against the chain

The E2E suite connects to the actual Bitcoin node, queries the real mempool, and verifies against the live Electrum server. No test doubles. No fixtures. Real blockchain data.

## Coverage by Domain

Coverage is measured per-domain, not in aggregate:

| Domain Category | Backend Lines | Frontend Lines |
|-----------------|---------------|----------------|
| Core business (billing, subscription, auth) | 90%+ | N/A |
| Blockchain (bitcoin-core, electrum, explorer) | 90%+ | 90%+ |
| Data processing (transformers, projections) | 92%+ | 92%+ |
| Platform (webhook, fx, usage) | 88%+ | N/A |

Branch coverage (~82-84%) is the active gap being closed.

## Chaos Testing: 10 Phases

Production resilience testing that intentionally breaks infrastructure:

| Phase | Service | Disruption | What We Verify |
|-------|---------|-----------|----------------|
| 1 | Redis | Service stopped 30s | Cache circuit breaker activates, services degrade |
| 2 | PostgreSQL | Service stopped 30s | Database circuit breaker, read fallbacks |
| 3 | Bitcoin Core RPC | Port blocked 30s | Blockchain data serves from cache |
| 4 | Fulcrum | Port blocked 30s | Address lookups degrade, search still works |
| 5 | ZMQ publisher | Ports blocked 30s | Block notifications stop, polling fallback |
| 6 | Kraken WebSocket | Outbound blocked | Price feed loss, cached prices served |
| 7 | REST APIs | Endpoints blocked | CoinGecko/CoinCap circuit breakers fire |
| 8 | Multi-service | Redis + BTC Core simultaneously | Cascade degradation, no crashes |
| 9 | WebSocket surge | 100 simultaneous connections | Memory stable, connections managed |
| 10 | DNS failure | UDP+TCP 53 blocked | External API isolation verified |

Each phase: disrupt for 30s, verify graceful degradation (no crashes), restore, verify full recovery.

## k6 Performance Profiles

| Profile | VUs | Duration | Latest Result |
|---------|-----|----------|---------------|
| Smoke | 1 | 10s | 24/24 checks, p95 < 50ms |
| Load | 50 | 2 min | 0.00% error rate |
| Soak | 10 | 60 min | 0.00% errors |

The smoke test has achieved 24/24 for 11 consecutive production cycles.

## Playwright Visual Testing

29 specs covering the full user experience across 3 viewports:

- **Explorer**: dashboard widgets, block history, search, address/transaction details
- **Responsive**: layout verification at desktop (1440px), tablet (900px), phone (375px)
- **Portal**: customer journey (register, dashboard, API keys, billing, settings)
- **Admin**: CEO dashboard, CTO dashboard
- **Cross-cutting**: RTL (Hebrew/Arabic), deep linking, error states, toolbar, calculator

Screenshots are captured at 3 viewports and compared daily using pixelmatch for visual regression detection.

## Zod Runtime Validation

TypeScript types disappear at runtime. Backend sends `{error: {code, message}}` but the frontend type says `error: string` — React renders `[object Object]` and crashes. Zod schemas catch this at the API boundary, before React ever sees it.

- **79 safeParse calls** across 4 bridges (portal, CEO, CTO, CRM)
- **25 schema files** in `@blocksight/shared-schemas` package
- **42 WebSocket event schemas** for real-time data validation
- **Fail-open design**: invalid data logs a warning but never throws — the app stays up

## Historical Progression

| Cycle | E2E | k6 | Chaos | Coverage |
|-------|-----|-----|-------|----------|
| PL-C24 | 81.2% | 24/24 | 4/4 | BE 90.8% / FE 91.0% |
| PL-C25 | 95.1% | 24/24 | 4/4 | BE 91.4% / FE 91.9% |
| PL-C26 | 97.4% | 24/24 | 10/10 | BE 91.8% / FE 92.3% |
| PL-C28 | 97.7% | 24/24 | 10/10 | BE 91.8% / FE 92.3% |
| PL-C30 | **98.1%** | **24/24** | **10/10** | **BE 92.5% / FE 93.5%** |

Key improvements: try/catch removal exposed 22 hidden failures (PL-C24). Chaos expanded 4→10 phases. Zod runtime validation added (PL-C29). Coverage sprint +1,280 tests (PL-C30).
