# Webhooks — Push Notifications

## Overview

BlockSight webhooks deliver real-time Bitcoin events to your endpoint via HTTPS POST. Available on **Advanced** and **Premium+** tiers.

## Setup

Configure webhooks in the [Customer Portal](https://portal.blocksight.live):

1. Navigate to Webhooks section
2. Enter your endpoint URL (must be HTTPS)
3. Select event types to receive
4. Save — a signing secret is generated

## Payload Format

```json
{
  "id": "evt_abc123",
  "type": "block.mined",
  "created": 1712534400000,
  "data": {
    "height": 944100,
    "hash": "00000000000000000001...",
    "txCount": 3200,
    "timestamp": 1712534400
  }
}
```

## Signature Verification

Every webhook includes an HMAC-SHA256 signature in the `X-BlockSight-Signature` header:

```javascript
const crypto = require('crypto');

function verifyWebhook(payload, signature, secret) {
  const expected = crypto
    .createHmac('sha256', secret)
    .update(payload)
    .digest('hex');
  return crypto.timingSafeEqual(
    Buffer.from(signature),
    Buffer.from(expected)
  );
}

// Express example
app.post('/webhook', (req, res) => {
  const signature = req.headers['x-blocksight-signature'];
  if (!verifyWebhook(JSON.stringify(req.body), signature, WEBHOOK_SECRET)) {
    return res.status(401).send('Invalid signature');
  }
  // Process event
  console.log(req.body.type, req.body.data);
  res.status(200).send('OK');
});
```

## Event Types (18)

| Event | Trigger | Data |
|-------|---------|------|
| `block.mined` | New block confirmed | height, hash, txCount, timestamp |
| `block.orphaned` | Block orphaned (chain reorg) | height, hash |
| `price.updated` | BTC/USD price change >0.5% | usd, change24h, changePercent |
| `fee.spike` | Fee rate >50 sat/vB | nextBlockFee, previousFee |
| `fee.drop` | Fee rate drops >50% | nextBlockFee, previousFee |
| `congestion.high` | Score >75 | score, level, mempoolTxCount |
| `congestion.extreme` | Score >90 | score, level, mempoolTxCount |
| `mempool.flood` | >50K unconfirmed TXs | txCount, size |
| `chain.reorg` | Chain reorganization detected | depth, oldTip, newTip |
| `subscription.activated` | Your subscription activated | tier, expiresAt |
| `subscription.expiring_30d` | 30 days until expiry | tier, expiresAt |
| `subscription.expiring_7d` | 7 days until expiry | tier, expiresAt |
| `subscription.expired` | Subscription expired | tier, expiredAt |
| `apikey.created` | New API key generated | keyPrefix |
| `apikey.revoked` | API key revoked | keyPrefix |
| `usage.80percent` | 80% of rate limit used | currentUsage, limit |
| `usage.100percent` | Rate limit reached | currentUsage, limit |
| `health.degraded` | Service degradation detected | affectedServices |

## Retry Policy

Failed deliveries are retried with exponential backoff:

| Attempt | Delay |
|---------|-------|
| 1 | Immediate |
| 2 | 1 minute |
| 3 | 5 minutes |
| 4 | 30 minutes |
| 5 | 2 hours |
| 6 (final) | 12 hours |

After 6 failed attempts, the webhook is marked as failed. You can retry manually from the portal.

## Best Practices

- Always verify the HMAC signature before processing
- Return 200 within 10 seconds (process asynchronously if needed)
- Use a queue (Redis, SQS) for heavy processing
- Monitor your endpoint's response time in the portal dashboard
