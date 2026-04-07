# BlockSight API — Developer Overview

BlockSight provides a real-time Bitcoin data API with REST endpoints, WebSocket streaming, and webhook notifications.

## Base URL

```
https://api.blocksight.live
```

## Authentication

All paid endpoints require an API key passed as a query parameter or header:

```bash
# Query parameter
curl "https://api.blocksight.live/api/v1/data/price?api_key=YOUR_KEY"

# Header
curl -H "x-api-key: YOUR_KEY" "https://api.blocksight.live/api/v1/data/price"
```

## Subscription Tiers

| Tier | Price | Rate Limit | WebSocket | Webhooks | REST Endpoints |
|------|-------|-----------|-----------|----------|---------------|
| **Basic** | Annual BTC payment | 100 req/min | 5 event types | No | 8 endpoints |
| **Advanced** | Annual BTC payment | 500 req/min | 15 event types | 10 webhook URLs | 12 endpoints |
| **Premium+** | Annual BTC payment | 2,000 req/min | All 42 events | Unlimited webhooks | All endpoints |

## Free Endpoints (No API Key Required)

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/healthz` | GET | Service health check |
| `/api/v1/blockchain/tip-height` | GET | Current blockchain height |
| `/api/v1/blockchain/price` | GET | BTC/USD price |
| `/api/v1/blockchain/recommended-fees` | GET | Fee estimates (next block, 6 hour) |
| `/api/v1/blockchain/congestion-load` | GET | Network congestion score (0-100) |

## Response Format

All responses follow the same envelope:

```json
{
  "success": true,
  "data": { ... },
  "meta": {
    "requestId": "req_abc123",
    "timestamp": 1712534400000
  }
}
```

Error responses:

```json
{
  "success": false,
  "error": {
    "code": "RATE_LIMITED",
    "message": "Rate limit exceeded. Retry after 60 seconds."
  }
}
```

## Next Steps

- [REST API Reference](REST-API-Reference) — All endpoints with request/response examples
- [WebSocket Events](WebSocket-Events) — Real-time streaming with 42 event types
- [Webhooks](Webhooks-Guide) — Push notifications for blockchain events
- [Getting Started](Getting-Started) — Quick start guide with code examples
