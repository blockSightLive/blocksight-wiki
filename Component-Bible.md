# Component Bible

Every UI component mapped to its specification, state machine diagram, system diagram, and source files. BlockSight has **90 components** across the public explorer, customer portal, and admin dashboard.

**Last refreshed**: 2026-04-20 (DELTA wiki-freshness session — added §Runtime Invariants (M-120) matrix + §WebSocket Event Registry authoring surface (G_B-114); prior 2026-04-18 refresh covered M-110-A Auth Subsystem, M-58 MobileInfoCard, BlockHistory rolling window, G_A-3/4 Legal tabs, CTO/CRM dashboards, Calculator, Mempool Projection split).

---

## Dashboard Gauges

### BitcoinPrice

| Artifact | Description |
|----------|-------------|
| MVP | MVP-04 — Real-time Bitcoin price display |
| State Machine | Price WebSocket Manager — polling, caching, broadcast lifecycle |
| System Diagram | Provider Architecture — external API integration, rate limiting, failover |
| CEO Spec | Price + Search consolidated spec |
| Variants | `full` (desktop), `compact` (mobile/tablet) |
| Data | WebSocket `price_update` every 2s |

### CongestionGauge

| Artifact | Description |
|----------|-------------|
| MVP | MVP-03 — Network congestion visualization |
| State Machine | Congestion Gauge Calculation — 5-component weighted algorithm |
| System Diagram | Transformation Pipeline — staggered 30s interval, 4-layer DDD |
| CEO Spec | Congestion Gauge consolidated spec + algorithm definition |
| Variants | `full` (desktop), `compact` (mobile/tablet) |
| Data | WebSocket `congestion_update` every 30s |
| CEO Decisions | 5-component formula confirmed. Linear delay (not exponential). Bytes + TX in tooltip. |

### FeeGauge

| Artifact | Description |
|----------|-------------|
| MVP | MVP-02 — Network fee estimation |
| State Machine | Fee Gauge Calculation + Fee Estimate Provider |
| System Diagram | Transformation Pipeline — staggered 30s interval |
| CEO Spec | Fee Gauge consolidated spec + algorithm definition |
| Variants | `full` (desktop), `compact` (mobile/tablet) |
| Data | WebSocket `fee_update` every 30s |
| CEO Decisions | Dynamic color zones: green (<5), yellow (5-20), orange (20-50), red (>50 sat/vB) |

---

## Block Display

### BlockCard (5 types)

| Artifact | Description |
|----------|-------------|
| MVP | MVP-01 — Blockchain visualization |
| State Machine | Blockchain State Service — polling, caching, broadcasting |
| System Diagram | ZMQ Data Flow + Entity Data Flow |
| CEO Spec | Block Timers consolidated spec |
| Card Types | `nextBlock`, `lastBlock`, `builtEdge`, `mempoolEdge`, `heightOnly` |
| Data | WebSocket `dashboard.update` + `new_block` |

### Countdown / Elapsed Timer

| Artifact | Description |
|----------|-------------|
| MVP | MVP-01 |
| State Machine | Timer Hooks — countdown/elapsed/centisecond states |
| CEO Spec | Block Timers consolidated spec |
| Format | MM:SS:cc (CEO-confirmed) |

### BlockchainVisualizer

| Artifact | Description |
|----------|-------------|
| MVP | MVP-01 — Thin wrapper orchestrator |
| Pattern | Delegates rendering to TwoDBlockchain; acts as a public mount point |
| Tests | 26 total (12 unit + 14 integration) |

### TwoDBlockchain

| Artifact | Description |
|----------|-------------|
| MVP | MVP-01 — 3-section orchestrator |
| Tests | 81 total (main + branches + mempool-integration) |
| Layout | Mempool (top) + Center (NextBlock+LastBlock) + Built (bottom) |

### VirtualizedBlockList

| Artifact | Description |
|----------|-------------|
| MVP | MVP-01 |
| State Machine | VBL + Viewport Block Loader + Block Shift Manager |
| System Diagram | Frontend Visualization Entity Mapping |
| Tests | 232 total (162 unit + 29 responsive + 41 hook) — highest FE test count |
| Modes | Standard (vertical) + Hybrid (vertical+horizontal snake) |

### BlockHistoryRow

| Artifact | Description |
|----------|-------------|
| MVP | MVP-01 — horizontal snake rows |
| Tests | 25 unit tests |
| Pattern | Alternating LTR/RTL flex-direction, up to 7 blocks per row |

### BlockChainLink

| Artifact | Description |
|----------|-------------|
| MVP | MVP-01 — block connectors |
| Tests | 30 unit tests (4 SVG directions, opacity boundary, path data-truth) |
| Pattern | Bezier curve connectors: vertical, horizontal, corner-down-left, corner-down-right |

---

## BlockHistory System (desktop rolling window)

Infinite virtual scroll over the entire 900K+ block-height range with **O(1) data cost**. The Built section renders a rolling window of ~6–8 block rows at a time; placeholders are lazily materialized for every height scrolled past, and **real block data is fetched only when a user clicks a placeholder**. Users can travel tip→genesis without paying network or memory cost for the heights in between.

### Design Contract (CEO)

| # | Rule |
|---|------|
| 1 | **Desktop only.** Mobile uses a direct render path over real blocks (no virtualization). |
| 2 | **Placeholders render ONLY the block height.** No hash, no fee, no tx count, no timer, no shimmer, no color coding. |
| 3 | **Real block data fetch trigger = click only.** No viewport prefetch, no debounced batch WebSocket, no scroll-driven header requests. Click opens InfoPanel; InfoPanel owns the fetch. |
| 4 | **Rolling window** covers ~6–8 rendered rows at a time over a 900K-block virtual scroll range, via overscan + virtual scrolling. |

### Architecture

| Piece | Role |
|-------|------|
| Resolver hook | Lazy `createPlaceholderBlock(height)` for any index not in the real-block map |
| Virtual scroll hook | Overscan + visible windowing, ResizeObserver-measured container, stick-to-bottom auto-scroll |
| Placeholder sentinel | `BlockSummary` with `hash: 'placeholder-{height}'` and zero values for fee/tx/time fields (never displayed) |
| Desktop-only enforcement | Single boolean guard on the orchestrator; removing it would silently activate the rolling window on mobile |
| Click fetch pipeline | `BlockCard onClick` → VBL → block selection → InfoPanel → DataStore fetch → real `BlockSummary` flows back into the resolver |

### Cross References

- SMD: VBL state machine + Block Resolver + Block Shift Manager + Scroll Viewport Management
- RTM: block-history flow row

---

## Explorer Detail Panels

### BlockDetails

| Artifact | Description |
|----------|-------------|
| MVP | MVP-13 — Block search and details |
| State Machine | Explorer Query Flow |
| System Diagram | Domain Entity Relationships |
| Data | REST API block endpoint + WebSocket enrichment |

### TransactionDetails

| Artifact | Description |
|----------|-------------|
| MVP | MVP-14 — Transaction lookup |
| State Machine | Explorer Query Flow |
| CEO Spec | Transaction Heuristics consolidated spec |
| Data | REST API transaction endpoint |

### AddressDetails

| Artifact | Description |
|----------|-------------|
| MVP | MVP-15 — Address lookup |
| State Machine | Explorer Query Flow |
| Data | REST API address endpoint + Electrum queries |

---

## Search + Navigation

### SearchBar + SearchResults

| Artifact | Description |
|----------|-------------|
| MVP | MVP-13 |
| State Machine | Search Flow — input parsing, type detection, result rendering |
| CEO Spec | Price + Search consolidated spec |
| Pattern | Inline panel (not modal — CEO decision) |

### Header + HealthChip

| Artifact | Description |
|----------|-------------|
| State Machine | WebSocket Connection (HealthChip state reflects connection health) |
| Components | Desktop `Header`, phone `MobileHeader` |

### HomeBeacon

| Artifact | Description |
|----------|-------------|
| MVP | MVP-01 |
| State Machine | HomeBeacon State — 8 activation conditions, pure memoized hook |
| Tests | 47 (27 unit + 20 hook) |
| Status | Production Ready, Protected File |

### ToolBar (5 tools)

| Artifact | Description |
|----------|-------------|
| Tools | Supply, General Metrics, Halving, Monica Metrics, Calculator |
| Tests | 32 (18 unit + 6 context + 8 integration) |
| Pattern | Expandable panel, desktop/tablet only |

### CopyableEntityLink

| Artifact | Description |
|----------|-------------|
| MVP | Cross-cutting UI primitive across all explorer detail panels |
| Tests | 22 unit tests (no-nav path, nav click, 4 special labels + memory-pool-N regex + empty → em dash, truncation, aria-label, keyboard Enter) |
| Consumers | TransactionDetails (sender/receiver/txHash), BlockDetails (hash row), AddressDetails (address + tx history), MobileInfoCard — 13 call sites total |
| Special labels | Coinbase, Coinbase (Block Reward), OP_RETURN, Unknown, Non-standard, empty/null + regex `memory-pool-N` → italic muted span with no copy/nav |
| Props | `{ value, type: block\|tx\|address, onNavigate?, truncate?, testId?, className? }` |
| i18n keys | 3 aria labels (`copyableLink.ariaLabel.{block\|tx\|address}`) |

---

## Mobile Components

### MobileDashboard

| Artifact | Description |
|----------|-------------|
| Pattern | Phone layout shell — orchestrates MobileBlockchainView + widgets |
| Tests | 20+ unit tests |
| CEO Session | Footer repositioned to `position: fixed` bottom-right. HealthChip toggles DashboardData visibility. Clipboard-first search replaces full-screen overlay. |
| Z-stack | MobileHeader (top) > DashboardData > MobileInfoCard > Footer > MobileBlockchainView (full-viewport canvas) |

### MobileBottomSheet

| Artifact | Description |
|----------|-------------|
| State Machine | Inherits from InfoPanel View Coordinator |
| Pattern | Swipe to dismiss (>60px threshold), 4 content types, 3 dismiss methods |
| Tests | 29 unit tests |
| Status | **Tablet-only since M-58** — phone path replaced by MobileInfoCard |

### MobileInfoCard

| Artifact | Description |
|----------|-------------|
| MVP | MVP-12/13/14 — phone variant of explorer detail panels |
| Sub-components | MobileBlockCard, MobileTransactionCard, MobileAddressCard (data parity per entity type) |
| Tests | ~27 unit (block + tx + address parity, hash truncation, mode swap, drag) + 10 useDraggable hook tests (touch+mouse, topBound, button guard, rAF batching) |
| Pattern | Floating draggable card with drag-jitter prevention (topBound 52), replaces MobileBottomSheet on phone |
| Data parity | 12 block fields + 17 tx fields + 12 address fields match desktop detail panels |

### MobileBlockchainView

| Artifact | Description |
|----------|-------------|
| Pattern | 3-section scroll (mempool / center / built), phone-only |
| Tests | 13 unit tests |
| CEO Decision | Height-only on distant blocks. NextBlock collision guard. |

### MobileBlockCard

| Artifact | Description |
|----------|-------------|
| Pattern | Compact block display, CEO pixel spec (170x109 to 51x31 progressive shrink) |
| Tests | 13 unit tests |
| CEO Decision | Height-only on distant blocks (index 3+) |

---

## Revenue + Platform

### Subscription Funnel

| Artifact | Description |
|----------|-------------|
| MVP | MVP-20 — Billing and subscription |
| State Machines | Subscription Lifecycle + Invoice Lifecycle + Payment Detection |
| CEO Spec | Billing + API Platform consolidated specs |
| Pattern | Annual-only, Bitcoin push payments, no auto-renewal |

### Auth Subsystem — Per-Device Attestation (v1.0 iOS)

| Artifact | Description |
|----------|-------------|
| Scope | iOS App Attest attestation → per-device EdDSA (Ed25519) JWT → 1-hour TTL |
| ADR | ADR-052 Per-device JWT Auth (Status: Proposed) |
| State Machine | Auth Attest Lifecycle — challenge / device / JWT sub-state machines |
| Key rotation | Dual-kid window (CURRENT + PREVIOUS, 2h overlap) |
| Discovery | Public JWKS endpoint at `/.well-known/jwks.json` |
| Deprecation | 60-day `x-api-key` deprecation window with HTTP 410 Gone + RFC 8594 Sunset header post-cutover |
| Architecture | Challenge-response → attestation verify → JWT signer → bearer middleware composed dual-mode with existing API-key middleware |
| Dependencies | `node-app-attest` (pure JS, bundled Apple root CA), `jose`, Redis (challenge store, 5-min TTL), PostgreSQL (`attested_devices` table) |
| Observability | 10 Prometheus metrics (challenge issued, attest success/fail by reason, JWT sign/verify, key rotation, deprecation warnings) |
| v1.1 roadmap | Android Play Integrity + OAuth2 Client Credentials (B2B server-to-server). WebAuthn deferred v1.2+. |

### API Key Management

| Artifact | Description |
|----------|-------------|
| MVP | MVP-19 |
| State Machine | API Key Lifecycle — create, rotate, revoke |
| CEO Spec | API Platform consolidated spec |

### WebSocket Subscriptions

| Artifact | Description |
|----------|-------------|
| MVP | MVP-22 |
| State Machines | WebSocket Hub + Event Lifecycle |
| Pattern | 38 typed events, tier-based rate limiting, per-key authentication (+ Bearer JWT under Auth Subsystem) |

### Customer Portal

| Artifact | Description |
|----------|-------------|
| MVP | MVP-30, MVP-33 — Self-service portal |
| State Machine | Portal Lifecycle |
| CEO Spec | Billing consolidated spec |
| OAuth | Google + GitHub social login |

### CEO Dashboard

| Artifact | Description |
|----------|-------------|
| MVP | MVP-23 |
| CEO Spec | Admin Dashboard consolidated spec |
| Tests | ~277 total (pages 146 + bridge 13 + hooks 76 + country data 4 + Playwright 6) |
| OAuth | Google + GitHub social login (admin variant) |
| 2FA | TOTP + backup codes, ProtectedRoute enforces 2FA |

### CTO Dashboard

| Artifact | Description |
|----------|-------------|
| MVP | MVP-27 — internal engineering dashboard |

### CRM Dashboard

| Artifact | Description |
|----------|-------------|
| MVP | MVP-28 — customer relationship + support dashboard |
| Tests | ~299 total (207 unit + 81 hook/service + 11 Playwright) |

### Webhooks

| Artifact | Description |
|----------|-------------|
| MVP | MVP-29 |
| State Machine | Webhook Delivery Retry — exponential backoff, HMAC-SHA256 verification |
| CEO Spec | API Platform consolidated spec |
| Tests | 31 total (page 9 + card 13 + hook 9) |
| i18n | Wired via `useTranslation()` (only portal page with full i18n) |

---

## Document Viewer

### LegalContent (4 tabs)

| Artifact | Description |
|----------|-------------|
| MVP | MVP-31 — legal documents |
| Tabs | Terms, Privacy, SLA, Accessibility (4 tabs × 31 locales) |
| Accessibility tab | 7-section WCAG 2.2 AA statement, 24 CEO-provided translations |
| Routes | `/accessibility` (canonical SEO shell) + `/legal/accessibility` (alt redirect) |

### LegalLinksFooter

| Artifact | Description |
|----------|-------------|
| MVP | MVP-31 — legal nav mirror of FooterCopyright |
| Tests | 10 unit tests (3-link render, hrefs, 4 non-idle mode hides, RTL wrapper) |
| i18n | 3 keys × 31 locales (`toolbar.footerLinks.{privacy\|terms\|accessibility}`) |
| Design | Fixed bottom-left, row flex, separator via `::before` pseudo, idle-mode only, hidden on phone |
| RTL | RTL-safe: logical properties (`inset-inline-start`, `padding-inline-end`), flexbox auto-flips |

### Calculator

| Artifact | Description |
|----------|-------------|
| MVP | MVP-04 — BTC⇄fiat conversion |
| CEO Spec | Price + Search consolidated spec |
| Tests | 40 total (tool 12 + content 28) |
| Features | Conversion, secondary fiat, copy-to-clipboard with timer cleanup |

---

## Visual Layers

### Cosmic Background

Three z-indexed layers: gradient fallback (-10000), cosmic image (-9999), starfield canvas (-9998). Responsive orientation: horizontal (desktop) vs vertical (phone).

### Block Found Animation

Triggered by ZMQ `new_block` events. Orchestrated animation sequence with configurable duration and effects.

---

## Mempool Projection

### Ancestor-Score (Dashboard)

| Artifact | Description |
|----------|-------------|
| MVP | MVP-05 — Mempool block visualization (real-time projections) |
| State Machine | Mempool Projection Service — incremental + full projection modes |
| CEO Spec | Mempool Projection consolidated spec |
| Algorithm | CEO ancestor score: `min(AF_tx, f_tx)`, CPFP-aware, accuracy tracking |
| System Diagrams | Domain Entity Relationships + Entity Data Flow |
| Consumers | Dashboard-stream WebSocket broadcasting |

### Explorer Simple

| Artifact | Description |
|----------|-------------|
| MVP | MVP-05 — Lightweight projection for explorer endpoint |
| Algorithm | Electrum histogram greedy fill |
| Consumers | Explorer REST API (`/api/v1/blockchain/mempool-projection`) |

---

## Coverage Summary

| Category | Components | Notes |
|----------|-----------|-------|
| Dashboard Gauges | 3 | 3 consolidated CEO specs |
| Block Display | 7 | +BlockchainVisualizer wrapper |
| BlockHistory System | Architecture | Desktop-only rolling window contract |
| Explorer Panels | 3 | 1 consolidated spec + Explorer Query Flow |
| Search + Navigation | 6 | +CopyableEntityLink cross-cutting primitive |
| Mobile | 6 | +MobileInfoCard (phone-only) with 3 entity sub-cards |
| Revenue + Platform | 11 | +Auth Subsystem + CTO + CRM dashboards |
| Document Viewer | 3 | LegalContent (4 tabs) + LegalLinksFooter + Calculator |
| Visual Layers | 5 | Background + block-found animation |
| Mempool Projection | 2 | Ancestor-score (dashboard) + Explorer simple |
| **Total (UI-facing)** | **~47 core** | Plus ~43 supporting components (contexts, providers, utilities, admin) across the full 90-component architecture |

---

## Runtime Invariants (M-120 Proactive Quality Track)

BlockSight instruments data-truth contracts as runtime invariants. Each invariant is defined in a co-maintained catalog and enforced at the source boundary: when a contract drifts, a Prometheus counter increments and a critical Grafana alert fires within 5 minutes. This is the fourth DATA TRUTH RULE check (joining unit safety, cross-field assertions, and cross-path consistency), established by the M-120 proactive-quality track.

**Shared counter**: `blocksight_invariant_violations_total` with labels `{pipeline, check}`. One counter, many contracts — the label pair identifies the specific invariant.

**Critical alert**: `InvariantViolationDetected` — fires on any increase in the counter over a 5-minute window, pages the operator.

### Wired invariants (M-120 Phase 3)

| Pipeline | Check | Contract | Component |
|----------|-------|----------|-----------|
| `auth` | `sub-sha256-deviceId` | JWT `sub` claim must equal `deviceId` (both are sha256 of the signing key id). | Auth Subsystem |
| `mcp` | `init-concurrent-distinct-sid` | Every fresh MCP `initialize` returns a distinct session id (never collides with a prior session). | MCP Server |
| `ws` | `ws-jwt-revocation-30s-sla` | Within 30 seconds of a device being revoked, every open WebSocket connection authenticated with that device must close. | WebSocket Subscriptions |
| `fees` | `fee-unit-safety` | Fee-rate percentiles (p10/p50/p90) must lie within a plausible sat/vB ceiling (rejects satoshis-as-sat/vB misrouting). | FeeGauge |
| `tx-explorer` | `confirmed-tx-requires-blockheight` | Every transaction written to the explorer cache with `status=confirmed` must have a non-null `blockHeight`. | TransactionDetails |
| `block-metadata` | `miner-label-nonempty` | Every block metadata insert must carry a non-empty `miner_name` (pool name or `Unknown`, never empty string). | BlockCard |
| `price` | `price_positive` | BTC/USD price must be strictly positive. | BitcoinPrice |
| `price` | `price_range` | BTC/USD price must lie in the open range (1000, 1_000_000) USD — the plausible 2025-2027 market band. | BitcoinPrice |
| `price` | `flash_crash` | 24-hour price change magnitude must be less than 50%. | BitcoinPrice |

Nine distinct `{pipeline, check}` label pairs across seven pipelines. Every pair is both (a) emitted by source code and (b) observed by Grafana — contract drift surfaces within 5 minutes under production load instead of during a retrospective audit.

**Catalog**: The machine-readable catalog of invariants is the source of truth for this matrix. Every row carries a canonical sample of the contract satisfied and a concrete example of a contract violation so that engineers writing new tests know exactly what a production-shape fixture looks like.

---

## WebSocket Event Registry

BlockSight's WebSocket contract carries 27 subscribable event types maintained as a single readonly array in a shared schemas package. Four derived surfaces — the backend subscribable-events set, the backend contract test, the frontend consumer handler record, and the backend emit-side compile-time guard — all import from the one authoritative file. It is structurally impossible to hand-edit any derived surface without the others noticing.

**Drift class closed** (five incidents eliminated): prior to the consolidation, four incidents were possible between parallel hand-maintained lists — three backend-side plus one implicit frontend-subscriber drift risk. A first round of consolidation closed the backend authoring surface; a follow-on extended the type-system boundary to the frontend, making the subscriber record compile-time-checked instead of runtime-registered. A third pass narrowed the emit-side event-type field from a loose string to the registry union, closing a fifth, previously-hidden drift class where five transformers were emitting colon-style event literals that the registry quietly rejected. The offending emitters were deleted, and the type narrowing prevents the same shape of drift from returning.

**Authoring sequence** for adding a new subscribable event:

1. Append the event name to the canonical registry array, under the matching domain-group comment.
2. Emit the event via the hub broadcast path from the producing transformer.
3. Add a handler entry to the frontend event-handlers Record — TypeScript compile fails if the Record misses a registry entry or carries one not in the registry.

Contract tests enforce five invariants on the registry (non-empty, case-correctness, no-duplicates, grouped-by-domain, no-dead-entries via backend + frontend source scan). Contract drift fails at CI in ~30 minutes, not at a cycle 2-4 days later.

**Canonical delivery paths for transformer data**: five transformer pipelines — congestion, fee, price, time-series, milestone — no longer emit via transformer-direct hub broadcasts. Fee, congestion, and price reach the frontend via their dashboard-stream underscore broadcasters which publish `fee_update` / `congestion_update` / `price_update`. Time-series and milestone reach the frontend exclusively via the visualization-data-transformer aggregator payload (fields `t` and `m` embedded inside `visualization:update`). Pre-broadcast invariant emissions — fee unit-safety and the three price invariants (positive / range / flash-crash) — were preserved byte-identical through the deletion because they run on the return-to-engine path, not the broadcast path.

---

**See also**: [[Component Tree]] | [[Data Flow]] | [[Desktop Layout]]
