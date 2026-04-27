# API Reference

BlockSight provides a comprehensive Bitcoin data API with REST endpoints, real-time WebSocket events, webhook notifications, and an MCP (Model Context Protocol) server for AI integrations.

**Base URL**: `https://api.blocksight.live`
**MCP URL**: `https://mcp.blocksight.live/mcp`
**System Status**: [stats.uptimerobot.com/0stUweBL91](https://stats.uptimerobot.com/0stUweBL91)

**Last refreshed**: 2026-04-19 (DELTA maintenance — post M-110-A Auth Attest + M-110-A-WS-EXT + M-118 per-session MCP transport).

---

## Authentication

### Per-Device JWT (v2 — recommended)

BlockSight v1.0 uses per-device JWT authentication using Apple App Attest and EdDSA (Ed25519) tokens.

**Flow**: `GET /api/v1/auth/challenge` → attestation → `POST /api/v1/auth/attest` → receive JWT → use `Authorization: Bearer <JWT>` on all requests.

**JWKS**: Public keys available at `/.well-known/jwks.json` (RFC 7517, Cache-Control 300s).

**Token lifetime**: 1 hour. Refresh via `POST /api/v1/auth/refresh` with device assertion.

**Live revocation (30s SLA)**: If a device is revoked server-side (admin action), active WebSocket connections for that device close with code `4009` within 30 seconds regardless of JWT TTL. REST requests also reject with `401 DEVICE_REVOKED` at verify time.

### API Key (v1 — deprecated)

```
Header:  x-api-key: bs_live_...
```

> **Deprecation notice**: `x-api-key` authentication enters a 60-day deprecation window starting with v1.0 deployment. After 60 days, `x-api-key` requests will receive HTTP 410 Gone. Migrate to per-device JWT authentication.

> **Removed in v1.0 (M-110-C, OWASP A01:2021):** the `api_key` query-parameter path
> was removed because query strings leak to logs, browser history, and `Referer` headers.

API keys are created in the [Customer Portal](https://portal.blocksight.live) under **API Keys**. Keys start with `bs_live_` and are shown once at creation.

### Auth Endpoints

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/api/v1/auth/challenge` | Get a single-use attestation challenge (300s TTL) |
| `POST` | `/api/v1/auth/attest` | Submit Apple attestation, receive JWT |
| `POST` | `/api/v1/auth/refresh` | Refresh JWT with device assertion |
| `GET` | `/.well-known/jwks.json` | Public JWKS endpoint (EdDSA keys) |

---

## Response Format

All REST endpoints return a consistent envelope:

**Success:**
```json
{
  "success": true,
  "data": { },
  "meta": {
    "method": "GET /api/v1/data/price",
    "timestamp": "2026-04-07T14:02:16.784Z",
    "duration_ms": 1,
    "cached": false
  }
}
```

**Error:**
```json
{
  "success": false,
  "error": {
    "code": "API_KEY_REQUIRED",
    "message": "API key is required..."
  }
}
```

### Error Codes

| Code | HTTP | When |
|------|------|------|
| `API_KEY_REQUIRED` | 401 | No API key provided |
| `INVALID_API_KEY` | 401 | Key doesn't exist or revoked |
| `DEVICE_REVOKED` | 401 | Per-device JWT device has been revoked |
| `DEVICE_NOT_ATTESTED` | 401 | Per-device JWT device not found in attested registry |
| `JWT_EXPIRED` | 401 | Bearer token past expiry |
| `JWT_KID_UNKNOWN` | 401 | Bearer token signed with key rotated out of JWKS |
| `JWT_SIGNATURE_INVALID` | 401 | Bearer token signature verification failed |
| `SUBSCRIPTION_EXPIRED` | 403 | Subscription ended |
| `TIER_RESTRICTED` | 403 | Endpoint not available for your tier |
| `RATE_LIMIT_EXCEEDED` | 429 | Per-second or per-day limit hit |

### Rate Limit Headers

Every response includes rate limit information:

```
X-RateLimit-Limit-Per-Second: 20
X-RateLimit-Remaining-Per-Second: 19
X-RateLimit-Limit-Per-Day: 1000000
X-RateLimit-Remaining-Per-Day: 999999
X-RateLimit-Reset: 1775616000
```

---

## REST Endpoints

### Data Endpoints (authentication required)

| Endpoint | Tiers | Description |
|----------|-------|-------------|
| `GET /api/v1/data/price` | All | Current BTC price (multi-fiat) |
| `GET /api/v1/data/fees` | All | Current fee estimates |
| `GET /api/v1/data/congestion` | All | Network congestion metrics |
| `GET /api/v1/data/block/:id` | Advanced+ | Block details by height or hash |
| `GET /api/v1/data/transaction/:txid` | Advanced+ | Transaction details |
| `GET /api/v1/data/address/:address` | Advanced+ | Address summary |
| `GET /api/v1/data/address/:address/transactions` | Advanced+ | Address transactions (paginated) |
| `GET /api/v1/data/mempool-blocks` | Premium+ | Projected mempool blocks |

### External Public API (`/v1/` — no auth, for ChatGPT + MCP)

A separate read-only API for AI integrations (ChatGPT Actions, Claude Custom Connectors). Base path: `/v1/` (no auth, rate-limited per IP).

| Endpoint | Description |
|----------|-------------|
| `GET /v1/block-height` | Current tip height |
| `GET /v1/network-status` | Tip + mempool snapshot |
| `GET /v1/fees/recommended` | Fast/medium/slow fee rates |
| `GET /v1/blocks/latest?limit=N` | Recent blocks (1-20, default 10) |
| `GET /v1/blocks/{height}` | Block summary by height |
| `GET /v1/transactions/{txid}` | Transaction by 64-hex txid |
| `GET /v1/addresses/{address}` | Address summary (mainnet only) |

---

## Customer Portal API

The portal API uses session cookies (not API keys). Manage your account, keys, billing, and webhooks programmatically.

**Base path**: `/api/v1/customer/*`

### Auth

| Method | Path | Description |
|--------|------|-------------|
| `POST` | `/auth/register` | Create account. Body: `{email, password, companyName?}` |
| `POST` | `/auth/login` | Login. Body: `{email, password}`. Returns user profile. |
| `GET` | `/auth/session` | Check session status |
| `PATCH` | `/auth/language` | Set preferred language. Body: `{language}` |
| `POST` | `/auth/logout` | End session |

### API Keys

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/keys` | List your API keys (paginated) |
| `POST` | `/keys` | Create key. Returns full key ONCE. Body: `{name?}` |
| `DELETE` | `/keys/:keyId` | Revoke a key |
| `POST` | `/keys/:keyId/rotate` | Rotate: revoke old + create new |

### Billing

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/billing/subscription` | Current subscription (tier, status, features) |
| `POST` | `/billing/subscription/change` | Change tier. Returns BTC payment address. |
| `GET` | `/billing/invoices` | List invoices (paginated) |
| `GET` | `/billing/invoices/:id` | Invoice details |
| `GET` | `/billing/invoices/:id/status` | Payment status (confirmations, amount received) |
| `POST` | `/billing/invoices/renew` | Create renewal invoice |
| `GET` | `/billing/credits` | Credit balance and history |
| `GET` | `/billing/invoices/:id/document` | Download PDF (proforma/invoice/receipt) |

### Usage

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/usage/current` | Current period usage (daily/hourly counts, limits) |
| `GET` | `/usage/history` | Historical usage (paginated, up to 90 days) |
| `GET` | `/usage/limits` | Your tier's rate limits and features |
| `GET` | `/usage/export` | Download usage CSV |

### Organization

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/org` | Organization details |
| `PATCH` | `/org` | Update org info (legalName, billingEmail, etc.) |
| `POST` | `/org/invite` | Invite team member. Body: `{email, role?}` |
| `GET` | `/org/users` | List team members (paginated) |
| `DELETE` | `/org/users/:userId` | Remove team member |

### Webhooks

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/webhooks` | List available event types |
| `POST` | `/webhooks/endpoints` | Create endpoint. Returns signing secret ONCE. |
| `GET` | `/webhooks/endpoints` | List your endpoints |
| `GET` | `/webhooks/endpoints/:id` | Endpoint details |
| `PATCH` | `/webhooks/endpoints/:id` | Update endpoint (url, events, status) |
| `DELETE` | `/webhooks/endpoints/:id` | Delete endpoint |
| `POST` | `/webhooks/endpoints/:id/rotate-secret` | Rotate signing secret |
| `POST` | `/webhooks/endpoints/:id/test` | Send test event |
| `GET` | `/webhooks/endpoints/:id/deliveries` | Delivery history |

### Documents

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/docs/onboarding` | Download tier-specific onboarding guide (Markdown) |
| `GET` | `/docs/invoice/:id/:type` | Download invoice document (PDF) |

### Request Logs

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/logs` | API request history (paginated, filterable) |

---

## WebSocket

### Connection

```
Anonymous:       wss://api.blocksight.live/ws
Authenticated:   wss://api.blocksight.live/ws
                 Authorization: Bearer <JWT>           (v2 — per-device JWT)
                 Authorization: Bearer bs_live_...     (v1 — legacy API key)
```

The upgrade handler routes by token shape. A 3-segment `aaa.bbb.ccc` bearer value is verified as a per-device EdDSA JWT (same signer/JWKS as REST); a `bs_live_…` value takes the API key path. Browser clients that cannot set headers may use the `Sec-WebSocket-Protocol: api_key, bs_live_...` subprotocol form for the v1 path. The legacy `?api_key=<key>` query-parameter path was removed in v1.0 (M-110-C, OWASP A01:2021). Authenticated connections get higher rate limits and access to premium events.

### Close Codes

| Code | Error | Meaning |
|------|-------|---------|
| `4002` | Invalid API key | v1 path — key doesn't exist or revoked |
| `4006` | `JWT_EXPIRED` | v2 path — bearer token past expiry |
| `4007` | `JWT_INVALID` | v2 path — signature or format invalid |
| `4008` | `JWT_KID_UNKNOWN` | v2 path — signing key rotated out |
| `4009` | `DEVICE_REVOKED` | v2 path — device revoked (verify-time OR live termination within 30s SLA) |
| `4010` | `DEVICE_NOT_ATTESTED` | v2 path — device not found in attested registry |

### Subscribe / Unsubscribe

```json
{ "type": "subscribe", "payload": { "eventTypes": ["price_update", "block_added"] } }
{ "type": "unsubscribe", "payload": { "eventTypes": ["price_update"] } }
```

### Event Catalog

| Event | Frequency | Description |
|-------|-----------|-------------|
| `price_update` | ~2s | BTC price across exchanges |
| `congestion_update` | ~30s | Network congestion metrics |
| `fee_update` | ~30s | Fee rate estimates |
| `block_added` | ~10min | New block mined |
| `mempool_blocks` | ~30s | Projected mempool blocks |
| `dashboard.update` | ~2s | Aggregated dashboard data |
| `health_update` | ~60s | System health status |
| `blocks_history` | on connect | Recent block history |
| `ath_update` | on new ATH | All-time high notification |

### Reconnection Strategy

```
On disconnect:
  Wait 1s, then reconnect
  On failure: exponential backoff (2s, 4s, 8s, 16s, 30s cap)
  On reconnect: re-send subscribe message
  blocks_history re-fires automatically on new connection
```

---

## MCP Server (AI Integrations)

BlockSight exposes an MCP (Model Context Protocol) server for use with Claude Custom Connectors, Claude Code CLI, and any MCP-compatible LLM client.

> **Install steps for end users**: see the [README — MCP — Use BlockSight inside Claude](README.md#mcp--use-blocksight-inside-claude) section, or the canonical install page at [blocksight.live/claude](https://blocksight.live/claude). The reference below documents the wire-level protocol surface (session lifecycle, tool catalog, transport semantics) — not the install flow.

**URL**: `https://mcp.blocksight.live/mcp`
**Health**: `https://mcp.blocksight.live/mcp/health` (and `/health` for backward compatibility)
**Transport**: Streamable HTTP, **stateful per-session** (MCP spec 2025-06-18). Every `POST /mcp initialize` mints a fresh session id returned in the `Mcp-Session-Id` response header; subsequent calls echo the header back to route to the correct per-session transport.

### Session Lifecycle

```
initialize (no header)
    ↓
200 OK + Mcp-Session-Id: <uuid>
    ↓
[client echoes Mcp-Session-Id on every tools/list, tools/call, resources/list]
    ↓
[idle > 30 min (default) → session swept, subsequent calls return 404 -32001]
    ↓
client re-initializes → fresh session id
```

Idle timeout defaults to 30 minutes; sweep interval defaults to 60 seconds. Both tunable via MCP server env vars.

### Tools (7 read-only)

| Tool | Wraps | Input |
|------|-------|-------|
| `get_current_block_height` | `GET /v1/block-height` | None |
| `get_network_status` | `GET /v1/network-status` | None |
| `get_recommended_fees` | `GET /v1/fees/recommended` | None |
| `get_latest_blocks` | `GET /v1/blocks/latest?limit=N` | `{limit?: int, 1-20, default 10}` |
| `get_block_summary_by_height` | `GET /v1/blocks/{height}` | `{height: int, min 0}` |
| `get_transaction_by_txid` | `GET /v1/transactions/{txid}` | `{txid: 64-hex string}` |
| `get_address_summary` | `GET /v1/addresses/{address}` | `{address: string, mainnet only}` |

Each tool validates input (Zod), calls the corresponding `/v1/` REST endpoint internally, and returns JSON text content or `isError: true` on failure.

### Error Codes (JSON-RPC)

| Code | Meaning |
|------|---------|
| `-32000` | Missing or invalid `Mcp-Session-Id` header |
| `-32001` | Session id not found (expired or never existed) |
| `-32600` | Invalid request (re-initialize on a known session) |
| `-32603` | Internal server error |

---

## Webhook Events

### Delivery Format

```
POST to your endpoint URL
Headers:
  Content-Type: application/json
  X-BlockSight-Event-Id: evt_...
  X-BlockSight-Event-Type: payment.confirmed
  X-BlockSight-Signature: sha256=...
  X-BlockSight-Timestamp: 1712342400
```

### Event Types

| Category | Events |
|----------|--------|
| Subscription | `subscription.created`, `.activated`, `.renewed`, `.tier_changed`, `.cancelled`, `.expired`, `.expiring_30d`, `.expiring_7d` |
| Payment | `payment.confirmed`, `.failed`, `.pending` |
| Usage | `usage.alert.80`, `.alert.90`, `.alert.100`, `.report.daily`, `.report.monthly` |
| API Key | `api_key.created`, `.rotated`, `.revoked` |

### HMAC-SHA256 Verification

```javascript
const crypto = require('crypto');

const message = `${eventId}.${timestamp}.${body}`;
const expected = crypto.createHmac('sha256', secret).update(message).digest('hex');
const received = headers['x-blocksight-signature'].split('=')[1];
const valid = crypto.timingSafeEqual(Buffer.from(expected), Buffer.from(received));
```

---

## Tier Feature Matrix

| Feature | Basic | Advanced | Premium+ |
|---------|-------|----------|----------|
| REST: price, fees, congestion | Yes | Yes | Yes |
| REST: block, tx, address | No (403) | Yes | Yes |
| REST: mempool-blocks | No (403) | No (403) | Yes |
| WebSocket: public events | Yes | Yes | Yes |
| WebSocket: premium events | No | Yes | Yes |
| Webhooks | Yes | Yes | Yes |
| Rate: requests/second | 1 | 5 | 20 |
| Rate: requests/day | 25,000 | 150,000 | 1,000,000 |
| Team members | Unlimited | Unlimited | Unlimited |
| Price (annual) | 0.15 BTC | 0.30 BTC | 0.60 BTC |

---

## OAuth (Social Login)

The customer portal supports social login via Google and GitHub:

| Provider | Initiate | Flow |
|----------|----------|------|
| GitHub | `GET /api/auth/oauth/github` | Browser redirect → GitHub consent → callback → session |
| Google | `GET /api/auth/oauth/google` | Browser redirect → Google consent → callback → session |

If the OAuth email matches an existing account, the identity is linked automatically.

---

## Telemetry

BlockSight collects anonymous operational telemetry from the browser to detect translation gaps. This data is used exclusively for monitoring — it never affects rendering or user experience.

### POST /api/v1/telemetry/i18n-missing-key

Reports missing translation keys encountered in the browser. The call is **fire-and-forget**: the UI never reads the response body, never retries on failure, and never displays an error if the request fails.

**Auth**: None (public endpoint, rate-limited per IP)

**Request body**:
```json
{
  "events": [
    {
      "locale": "fr",
      "namespace": "common",
      "key": "blockchain.blockHeight",
      "ts": 1714000000000
    }
  ]
}
```

**Constraints**:
- `events`: 1–20 items per request
- `locale`: one of the 31 supported UI languages
- `key`: dot-notation i18n key (e.g. `blockchain.blockHeight`)
- `ts`: Unix timestamp in milliseconds

**Responses**:

| Status | Meaning |
|--------|---------|
| `204 No Content` | Events recorded |
| `400 Bad Request` | Body validation failed (missing fields, wrong types, >20 events) |
| `429 Too Many Requests` | IP rate limit exceeded |

**What happens with the data**: each event increments a Prometheus counter by `{locale, namespace, key}`, visible in the Grafana i18n monitoring panel. No user identifiers are stored. No event payload is written to a database.

---

*Derived from internal API contract registry. Sections on admin, CTO/CRM dashboards, and partner billing are intentionally omitted — they are internal and not customer-facing.*
