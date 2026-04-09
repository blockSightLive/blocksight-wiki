# Statistics

Every number on this page is sourced from the project's internal metrics system and verified against the actual codebase. Last verified: April 2026.

---

## Codebase Scale

| Layer | Files | Lines | Language |
|-------|-------|-------|----------|
| Backend source | 1,041 | 183,217 | TypeScript |
| Frontend explorer source | 449 | 64,717 | TypeScript + TSX |
| Frontend admin source | 95 | 12,897 | TypeScript + TSX |
| Frontend portal source | 40 | 4,981 | TypeScript + TSX |
| Shared schemas (Zod) | 47 | 4,307 | TypeScript |
| Backend tests | 1,024 | 386,516 | TypeScript |
| Frontend tests | 602 | 173,188 | TypeScript + TSX |
| Shared schemas tests | 15 | 4,051 | TypeScript |
| **Total** | **3,313** | **833,874** | |

Language composition: 100% TypeScript (backend) and TypeScript + TSX (frontend). Zero JavaScript source files.

---

## Component Architecture

| Metric | Count |
|--------|-------|
| UI components | 90 |
| Component variants (full/compact/mobile) | 15+ |
| State machine diagrams | 99 |
| System architecture diagrams | 21 |
| States documented across all SMDs | 410+ |
| Transitions documented | 580+ |
| Lines of architecture documentation | ~42,800 |

---

## Test Infrastructure

| Metric | Count |
|--------|-------|
| **Total test cases** | **28,857+** |
| Total test suites (executable) | 1,476+ |
| Backend unit + integration suites | 608 |
| Backend unit + integration cases | 14,423 |
| Frontend unit + integration suites | 407 |
| Frontend unit + integration cases | 7,772 |
| E2E test scenarios | 573 |
| Playwright visual test scenarios | 329+ |
| k6 load test endpoints | 24 |
| Chaos engineering scenarios | 10 |

### Test Runners

| Runner | Purpose | Scope |
|--------|---------|-------|
| Jest | Unit, integration, E2E | Backend + frontend |
| Playwright | Visual regression, browser testing | Frontend (3 viewports) |
| k6 | Load testing, smoke testing | All API endpoints |

---

## Production Quality (PL-C30 — Current Best)

| Metric | Value | Notes |
|--------|-------|-------|
| E2E pass rate | **98.1%** (566/577) | All-time best |
| Playwright pass rate | **72.5%** (211/291) | +53% from PL-C29, 0 DNS errors |
| k6 smoke tests | **24/24 (100%)** | 11th consecutive perfect score |
| Chaos engineering | **10/10 PASS** | All failure scenarios survived |
| Soak test error rate | **0.00%** | Zero errors over 60 minutes |
| Backend line coverage | **92.54%** | +0.98% from PL-C29 |
| Frontend line coverage | **93.47%** | +1.64%, branches 85.07% |
| Domain purity (backend) | **100%** | 0 impurities in 526 domain files |
| Architecture violations | **0** | dependency-cruiser enforced |
| Startup errors | **0** | 0 errors, 0 warnings |

See [[Production Metrics]] for the full progression across 27 production cycles.

---

## Domain Architecture

### Backend (22 domains)

| Domain | Files | Purpose |
|--------|-------|---------|
| bitcoin-core | 233 | RPC service, ZMQ handlers, block data loader |
| electrum | 93 | Protocol integration, connection pooling |
| auth | 23 | JWT, sessions, authentication |
| crm | 18 | Customer relationship management |
| subscription | 16 | Subscription lifecycle management |
| explorer | 16 | Block, transaction, address lookup |
| price | 15 | Price tracking (multi-provider) |
| transformation | 15 | 7 data pipeline transformers |
| fx | 12 | Exchange rates (26 currencies) |
| usage | 11 | API call metering, site visits |
| webhook | 10 | Delivery, retry logic, HMAC signing |
| billing | 9 | Invoices, Bitcoin payments |
| analytics | 9 | Congestion + fee gauge services |
| ceo-dashboard | 9 | CEO metrics dashboard |
| apikey | 7 | Key generation, validation, rotation |
| mempool-projection | 7 | Ancestor-score block projection |
| ratelimit | 7 | Token bucket, sliding window |
| maintenance | 5 | System maintenance mode |
| shared | 4 | Cross-domain interfaces |
| prediction-observatory | 3 | Prediction accuracy measurement |
| blockchain | 1 | Core models and repositories |
| verification | 0 | Email/identity (stub) |

### Frontend (3-App Model)

| App | Files | Auth | MVPs Served |
|-----|-------|------|-------------|
| Public Explorer | ~491 | None (public) | MVP-01 to 05, 13-17, 24-25, 31 |
| Admin Dashboard | ~85 | Session (bcrypt) | MVP-23, 27, 28, 32 |
| Customer Portal | ~36 | Session (customer) | MVP-19, 20, 30, 33 |

---

## API Surface

| Metric | Count |
|--------|-------|
| REST API endpoints | ~195 |
| WebSocket event types | 38 |
| API route files | 41 |
| Health endpoints | 3 |

---

## CEO Specification Library

| Metric | Count |
|--------|-------|
| CEO specification documents | 42 |
| Consolidated specs | 10 |
| WE_EXPECT acceptance criteria files | 25 |
| Requirements traceability matrices | 12 |
| ATDD architectural diagrams | 7 |
| MVPs defined and implemented | 35/35 |
| CEO coverage: STRONG | 14 MVPs |
| CEO coverage: MODERATE | 8 MVPs |

---

## Internationalization

| Metric | Value |
|--------|-------|
| Supported languages | 25 |
| Translation keys per language | ~1,730 |
| Namespaces | 3 (common, documentation, portal) |
| RTL languages | 3 (Hebrew, Arabic, Farsi) |
| Fiat currencies mapped | 25 (language-aware) |

---

## Design System

| Metric | Value |
|--------|-------|
| CSS custom properties (design tokens) | 400+ |
| Design token files | 12 |
| Design token lines of CSS | ~4,300 |
| Breakpoints | 3 (phone <=768px, tablet 769-1100px, desktop >1100px) |
| Component variant system | full / compact / mobile |

---

## Infrastructure

| Metric | Value |
|--------|-------|
| Infrastructure components | 22 |
| Prometheus metrics exported | 81 |
| Circuit breakers | 4 (PostgreSQL, Redis, Bitcoin Core, Fulcrum) |
| Data transformation pipelines | 7 |
| Cache layers | 2 (L1 in-memory LRU + L2 Redis) |

---

## Agent System

| Metric | Value |
|--------|-------|
| **Tasks completed** | **925+** |
| Specialized agents | 7 (MEGA, OMEGA, ALPHA, BETA, GAMMA, DELTA, LIMA) |
| Production evaluation cycles | 27 |
| Maintenance cycle tools | 13 |
| Languages translated by LIMA | 25 |
| Agent task queues | 8 folders |

---

**See also**: [[Production Metrics]] | [[The Vibe Coding Story]] | [[Technology Stack]]
