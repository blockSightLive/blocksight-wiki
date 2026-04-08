# Technology Stack

BlockSight is a full-stack TypeScript application connecting directly to Bitcoin's peer-to-peer network.

---

## Backend

| Layer | Technology | Purpose |
|-------|-----------|---------|
| Runtime | Node.js | Server runtime |
| Language | TypeScript (strict) | Type safety across 1,024 source files |
| HTTP Framework | Hono + Express bridge | API routing with Express middleware compatibility |
| Database | PostgreSQL 16 | Persistent storage, migrations, full-text search |
| Cache | Redis 7 | L2 cache, session storage, pub/sub |
| Bitcoin RPC | Bitcoin Core | Direct full node communication (getblock, getmempool, estimatefee) |
| Real-time events | ZMQ (hashblock, rawtx) | Zero-latency block and transaction notifications |
| Electrum Protocol | Fulcrum server | Address balance, history, UTXO lookups |
| WebSocket | ws library | Real-time client updates (38 event types) |
| Authentication | JWT + Redis sessions | Admin (bcrypt), Customer (session-based), API keys |
| Email | Resend API | Transactional email (fail-open, never blocks operations) |
| Payments | Bitcoin on-chain | Invoice generation, payment watching, address monitoring |

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
| Language | TypeScript (strict) | Type safety across 474 source files |
| Build tool | Vite | Fast HMR, production bundling, dynamic imports |
| Styling | CSS Modules | Scoped styles, 400+ design tokens |
| State | `useSyncExternalStore` + DataStore | Centralized reactive state (no Redux) |
| i18n | i18next | 25 languages with lazy loading |
| Routing | React Router | Client-side navigation |
| Charts | Custom SVG | No charting library dependency |

### Frontend Architecture

- **3 apps**: Public explorer (port 3000), Admin dashboard (port 3002), Customer portal (port 3003)
- **90 UI components** with cosmic glassmorphism theme
- **Responsive**: Desktop (triple-column), tablet (dual-column), phone (MobileDashboard)
- **Real-time**: WebSocket bridge updates DataStore, components re-render selectively

---

## Testing

| Tool | Purpose | Count |
|------|---------|-------|
| Jest | Unit + integration tests | 22,195 cases across 1,015 suites |
| Playwright | Visual regression + E2E browser tests | 257 scenarios |
| k6 | Load testing | 24 endpoint scenarios |
| Custom chaos | Resilience testing (kill Redis, kill PG, network partition) | 10 scenarios |
| E2E (backend) | Full API endpoint testing with real database | 573 scenarios (97.4% pass) |

---

## Infrastructure

| Component | Provider | Purpose |
|-----------|----------|---------|
| Chain server | Hetzner dedicated | Bitcoin Core + Fulcrum + production backend + PostgreSQL + Redis |
| Frontend hosting | Vercel | Static deployment, edge CDN |
| DNS + CDN | Cloudflare | DNS, DDoS protection, tunnel to backend |
| CI/CD | GitHub Actions | Automated testing on push |
| Monitoring | UptimeRobot | 5-endpoint availability monitoring — [System Status](https://stats.uptimerobot.com/0stUweBL91) |
| Search indexing | Google Search Console | SEO visibility |

---

## Observability

- **50 Prometheus metrics** covering RPC latency, cache hit rates, WebSocket connections, circuit breaker states
- **Structured logging** via Winston with category-based filtering
- **Health endpoints**: `/health`, `/health/services`, `/healthz` (Kubernetes-compatible)
- **Custom system health scanner**: knip (dead code), jscpd (duplication), dependency-cruiser (architecture rules)
