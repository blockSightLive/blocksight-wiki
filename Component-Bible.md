# Component Bible

Every UI component mapped to its specification, state machine diagram, system diagram, and source files. BlockSight has **90 components** across the public explorer, customer portal, and admin dashboard.

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
| Lines of Code | ~804 lines (largest FE component) |
| Tests | 195 total (166 unit + 29 responsive — highest FE test count) |
| Modes | Standard (vertical) + Hybrid (horizontal snake) |

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

---

## Mobile Components

### MobileDashboard

| Artifact | Description |
|----------|-------------|
| Pattern | Phone layout shell — orchestrates MobileBlockchainView + widgets |
| Tests | 20 unit tests |

### MobileBottomSheet

| Artifact | Description |
|----------|-------------|
| State Machine | Inherits from InfoPanel View Coordinator |
| Pattern | Swipe to dismiss (>60px threshold), 4 content types, 3 dismiss methods |
| Tests | 29 unit tests |

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
| Pattern | 38 typed events, tier-based rate limiting, per-key authentication |

### Customer Portal

| Artifact | Description |
|----------|-------------|
| MVP | MVP-30, MVP-33 — Self-service portal |
| State Machine | Portal Lifecycle |
| CEO Spec | Billing consolidated spec |

### Webhooks

| Artifact | Description |
|----------|-------------|
| MVP | MVP-29 |
| State Machine | Webhook Delivery Retry — exponential backoff, HMAC-SHA256 verification |
| CEO Spec | API Platform consolidated spec |

---

## Visual Layers

### Cosmic Background

Three z-indexed layers: gradient fallback (-10000), cosmic image (-9999), starfield canvas (-9998). Responsive orientation: horizontal (desktop) vs vertical (phone).

### Block Found Animation

Triggered by ZMQ `new_block` events. Orchestrated animation sequence with configurable duration and effects.

---

## Mempool Projection

| Artifact | Description |
|----------|-------------|
| MVP | MVP-05 — Mempool block visualization |
| State Machine | Mempool Projection Service — incremental + full projection modes |
| CEO Spec | Mempool Projection consolidated spec |
| Algorithm | CEO ancestor score: `min(AF_tx, f_tx)` |
| System Diagrams | Domain Entity Relationships + Entity Data Flow |

---

## Coverage Summary

| Category | Components | SMDs | CEO Specs |
|----------|-----------|------|-----------|
| Dashboard Gauges | 3 | 4 | 3 consolidated |
| Block Display | 6 | 5 | 1 consolidated |
| Explorer Panels | 3 | 1 shared | 1 consolidated |
| Search + Navigation | 5 | 2 | 1 consolidated |
| Mobile | 5 | 1 inherited | — |
| Revenue + Platform | 8 | 7 | 3 consolidated |
| Visual Layers | 5 | 1 | — |
| Mempool | 1 | 1 | 1 consolidated |
| **Total** | **~36 core** | **22** | **10 consolidated** |

Plus ~54 supporting components (contexts, providers, utilities, admin dashboard) across the full 90-component architecture.

---

**See also**: [[Component Tree]] | [[Data Flow]] | [[Desktop Layout]]
