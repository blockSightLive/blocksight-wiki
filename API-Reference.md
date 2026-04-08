# API Reference

BlockSight provides a comprehensive Bitcoin data API with REST endpoints, real-time WebSocket events, and webhook notifications.

**Base URL**: `https://api.blocksight.live`
**System Status**: [stats.uptimerobot.com/0stUweBL91](https://stats.uptimerobot.com/0stUweBL91)

---

## Authentication

Three methods (any one works):

```
Header:  x-api-key: bs_live_...
Header:  Authorization: Bearer bs_live_...
Query:   ?api_key=bs_live_...
```

API keys are created in the [Customer Portal](https://portal.blocksight.live) under **API Keys**. Keys start with `bs_live_` and are shown once at creation.

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

### Data Endpoints (API key required)

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
Anonymous:      wss://api.blocksight.live/ws
Authenticated:  wss://api.blocksight.live/ws?api_key=bs_live_...
```

Authenticated connections get higher rate limits and access to premium events. Invalid keys receive close code `4002`.

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
| REST: block, tx, address | No | Yes | Yes |
| REST: mempool-blocks | No | No | Yes |
| WebSocket: public events | Yes | Yes | Yes |
| WebSocket: premium events | No | Yes | Yes |
| Webhooks | Yes | Yes | Yes |
| Rate: requests/second | 1 | 5 | 20 |
| Rate: requests/day | 25,000 | 150,000 | 1,000,000 |
| Team members | Unlimited | Unlimited | Unlimited |
| Price (annual, BTC) | 0.15 | 0.30 | 0.60 |

---

## OAuth (Social Login)

The customer portal supports social login via Google and GitHub:

| Provider | Initiate | Flow |
|----------|----------|------|
| GitHub | `GET /api/auth/oauth/github` | Browser redirect → GitHub consent → callback → session |
| Google | `GET /api/auth/oauth/google` | Browser redirect → Google consent → callback → session |

If the OAuth email matches an existing account, the identity is linked automatically.

---

*Last updated: 2026-04-09. Derived from internal API contract registry.*
