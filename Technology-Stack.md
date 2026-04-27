# Technology Stack

BlockSight is a full-stack TypeScript application connecting directly to Bitcoin's peer-to-peer network.

**Last refreshed**: 2026-04-19 (DELTA maintenance — post M-118 per-session MCP transport + M-110-A Auth Attest + LIMA-M-119 locale propagation).

---

## Backend

| Layer | Technology | Purpose |
|-------|-----------|---------|
| Runtime | Node.js | Server runtime |
| Language | TypeScript (strict) | Type safety across 1,065 source files (187K lines) |
| HTTP Framework | Hono + Express bridge | API routing with Express middleware compatibility |
| Database | PostgreSQL 16 | Persistent storage, migrations, full-text search |
| Cache | Redis 7 | L2 cache, session storage, pub/sub |
| Bitcoin RPC | Bitcoin Core | Direct full node communication (getblock, getmempool, estimatefee) |
| Real-time events | ZMQ (hashblock, rawtx) | Zero-latency block and transaction notifications |
| Electrum Protocol | Fulcrum server | Address balance, history, UTXO lookups |
| WebSocket | ws library | Real-time client updates (38 event types) |
| Authentication | JWT + Redis sessions + iOS App Attest | Admin (bcrypt), Customer (session-based), B2B (API keys + per-device JWT with 30s live revocation) |
| Email | Resend API | Transactional email (fail-open, never blocks operations) |
| Payments | Bitcoin on-chain | Invoice generation, payment watching, address monitoring |
| MCP Server | Custom streamable HTTP transport | Per-session Model Context Protocol endpoint for Claude integrations (7 read-only tools) |

### Backend Architecture

- **22 domains** following Domain-Driven Design (DDD)
- **Functional Core / Imperative Shell** pattern — 100% domain purity
- **Dependency injection** via service container
- **Circuit breakers** for all external connections (PostgreSQL, Redis, Bitcoin Core, Fulcrum)
- **7 data transformation pipelines** (price, fee, congestion, mempool, block, transaction, address)

---

## Frontend

| Layer | Technology | Purpose |
|-------|-----------|---------|
| Framework | React 19 | UI rendering |
| Language | TypeScript (strict) | Type safety across 594 source files (84K lines) in 3 apps |
| Build tool | Vite | Fast HMR, production bundling, dynamic imports |
| Styling | CSS Modules | Scoped styles, 400+ design tokens |
| State | `useSyncExternalStore` + DataStore | Centralized reactive state (no Redux) |
| i18n | i18next | 31 languages with lazy loading (4 RTL: Arabic, Hebrew, Persian, Urdu) |
| Routing | React Router | Client-side navigation |
| Charts | Custom SVG | No charting library dependency |
| Observability | Grafana Faro + OpenTelemetry | Real-user monitoring, distributed tracing |

### Frontend Architecture

- **3 apps**: Public explorer (457 files), Admin dashboard (96 files), Customer portal (41 files)
- **90 UI components** with cosmic glassmorphism theme
- **Responsive**: Desktop (triple-column), tablet (dual-column), phone (MobileDashboard)
- **Real-time**: WebSocket bridge updates DataStore, components re-render selectively

---

## Shared

| Layer | Technology | Purpose |
|-------|-----------|---------|
| Schemas | Zod | 47 files (4K lines) — runtime-validated contracts shared across backend, frontend, and admin |

---

## Testing

| Tool | Purpose | Scale |
|------|---------|-------|
| Jest | Unit + integration tests | 22,195 cases across 1,015 suites (608 BE + 407 FE) |
| Jest E2E | Full API/workflow tests | 82 suites (80 BE + 2 FE) — recent PL-C42 PRIMARY: 699/706 pass (98.98%) |
| Playwright | Visual regression + cross-app E2E | 43 spec files across explorer, admin, portal, cross-cutting, capture |
| k6 | Load testing | 24 endpoint scenarios (15-minute soak profile default) |
| Chaos (custom) | Resilience testing | Kill-Redis / kill-PG / network-partition scenarios |
| MCP smoke | Claude MCP handshake regression | Session-scoped AC-1/AC-2/AC-3 assertions |

**Data truth layer**: Separate ALPHA phase (data-truth-sweep engine) exercises every external field against its Bitcoin Core source of truth — invariant assertions on fees, block rewards, transaction counts.

---

## Infrastructure

| Component | Provider | Purpose |
|-----------|----------|---------|
| Chain server | Hetzner dedicated | Bitcoin Core + Fulcrum + production backend + PostgreSQL + Redis |
| Frontend hosting | Vercel | Static deployment, edge CDN for all 3 apps |
| DNS + CDN | Cloudflare | DNS, DDoS protection, tunnel to backend |
| CI/CD | GitHub Actions | Automated testing on push + auto-deploy to Hetzner via ssh-action |
| Monitoring | UptimeRobot | 5-endpoint availability monitoring — [System Status](https://stats.uptimerobot.com/0stUweBL91) |
| Search indexing | Google Search Console | SEO visibility |

---

## Observability

- **81 Prometheus metrics** across 18 categories (HTTP/API, WebSocket, Cache, PostgreSQL, RPC, Circuit Breaker, Rate Limiting, Billing, Prediction Observatory, Frontend, MCP sessions, Auth, and more)
- **Structured logging** via Winston with category-based filtering
- **Health endpoints**: `/health`, `/health/services`, `/healthz` (Kubernetes-compatible), `/mcp/health`
- **Grafana dashboards**: CTO operations, CEO business metrics, MCP session activity
- **Custom health scanner**: knip (dead code), jscpd (duplication), dependency-cruiser (architecture rules)
- **Alert rules**: 16 production alerts covering handshake regression, circuit-breaker flips, mempool starvation, peer count drops
