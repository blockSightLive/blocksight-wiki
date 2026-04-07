# REST API Reference

## Paid Endpoints (API Key Required)

### Price

```
GET /api/v1/data/price
```

```json
{
  "success": true,
  "data": {
    "usd": 69310.80,
    "lastUpdated": 1712534400000,
    "source": "kraken"
  }
}
```

### Fee Estimates

```
GET /api/v1/data/fees
```

```json
{
  "success": true,
  "data": {
    "nextBlockFee": 1.1,
    "sixHourFee": 1.0,
    "unit": "sat/vB",
    "isReliable": true
  }
}
```

### Congestion

```
GET /api/v1/data/congestion
```

```json
{
  "success": true,
  "data": {
    "congestionScore": 12,
    "congestionLevel": "low",
    "mempoolTxCount": 3514,
    "activityLoad": 8.2,
    "delayLoad": 0
  }
}
```

### Block by Height

```
GET /api/v1/data/block/:height
```

```json
{
  "success": true,
  "data": {
    "height": 944099,
    "hash": "00000000000000000002a8f...",
    "timestamp": 1712534400,
    "txCount": 5442,
    "size": 1623456,
    "weight": 3993824,
    "minFeeRate": 0.1,
    "maxFeeRate": 263.2,
    "medianFeeRate": 1.5,
    "totalFees": 12345678,
    "subsidy": 312500000,
    "miner": "Foundry USA"
  }
}
```

### Transaction by TXID

```
GET /api/v1/data/transaction/:txid
```

```json
{
  "success": true,
  "data": {
    "txid": "abc123...",
    "blockHeight": 944099,
    "confirmations": 6,
    "fee": 1234,
    "feeRate": 5.2,
    "size": 225,
    "weight": 573,
    "inputs": 1,
    "outputs": 2,
    "value": 50000000
  }
}
```

### Address Balance

```
GET /api/v1/data/address/:address
```

```json
{
  "success": true,
  "data": {
    "address": "bc1q...",
    "confirmed": 150000000,
    "unconfirmed": 0,
    "txCount": 42
  }
}
```

### Address Transactions

```
GET /api/v1/data/address/:address/transactions?page=1&limit=25
```

```json
{
  "success": true,
  "data": [ ... ],
  "pagination": {
    "page": 1,
    "limit": 25,
    "total": 42,
    "totalPages": 2
  }
}
```

### Mempool Blocks (Projected)

```
GET /api/v1/data/mempool-blocks
```

```json
{
  "success": true,
  "data": {
    "blocks": [
      {
        "position": 1,
        "txCount": 2800,
        "minFeeRate": 1.0,
        "maxFeeRate": 150.0,
        "totalSize": 3950000
      }
    ]
  }
}
```

## Error Codes

| Code | HTTP Status | Description |
|------|------------|-------------|
| `UNAUTHORIZED` | 401 | Missing or invalid API key |
| `RATE_LIMITED` | 429 | Rate limit exceeded for your tier |
| `NOT_FOUND` | 404 | Block, transaction, or address not found |
| `TIER_RESTRICTED` | 403 | Endpoint not available on your tier |
| `INTERNAL_ERROR` | 500 | Server error (please report) |

## Rate Limit Headers

Every response includes:

```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 97
X-RateLimit-Reset: 1712534460
```
