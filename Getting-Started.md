# Getting Started with BlockSight API

## Quick Start (5 minutes)

### 1. Get an API Key

Visit [blocksight.live/api](https://blocksight.live/api) and subscribe to a tier. Your API key is generated immediately after Bitcoin payment confirmation (3 confirmations, ~30 minutes).

### 2. Make Your First Request

```bash
curl "https://api.blocksight.live/api/v1/data/price?api_key=YOUR_KEY"
```

Response:
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

### 3. Stream Real-Time Data

```javascript
const ws = new WebSocket('wss://api.blocksight.live/ws?api_key=YOUR_KEY');

ws.onopen = () => {
  ws.send(JSON.stringify({
    type: 'subscribe',
    payload: { eventTypes: ['price_update', 'block_added'] }
  }));
};

ws.onmessage = (event) => {
  const msg = JSON.parse(event.data);
  if (msg.type === 'price_update') {
    console.log(`BTC: $${msg.data.usd}`);
  }
  if (msg.type === 'block_added') {
    console.log(`New block #${msg.data.height}`);
  }
};
```

## Code Examples

### Node.js

```javascript
const fetch = require('node-fetch');

async function getBitcoinPrice() {
  const res = await fetch('https://api.blocksight.live/api/v1/data/price', {
    headers: { 'x-api-key': process.env.BLOCKSIGHT_KEY }
  });
  const { data } = await res.json();
  return data.usd;
}
```

### Python

```python
import requests

def get_bitcoin_price():
    r = requests.get(
        'https://api.blocksight.live/api/v1/data/price',
        headers={'x-api-key': os.environ['BLOCKSIGHT_KEY']}
    )
    return r.json()['data']['usd']
```

### cURL

```bash
# Current price
curl -H "x-api-key: $KEY" https://api.blocksight.live/api/v1/data/price

# Fee estimates
curl -H "x-api-key: $KEY" https://api.blocksight.live/api/v1/data/fees

# Block by height
curl -H "x-api-key: $KEY" https://api.blocksight.live/api/v1/data/block/944099

# Transaction
curl -H "x-api-key: $KEY" https://api.blocksight.live/api/v1/data/transaction/abc123...

# Address balance
curl -H "x-api-key: $KEY" https://api.blocksight.live/api/v1/data/address/bc1q...
```

## What's Available Per Tier

| Feature | Basic | Advanced | Premium+ |
|---------|-------|----------|----------|
| REST endpoints | 8 | 12 | All |
| WebSocket events | 5 | 15 | All 42 |
| Webhooks | No | 10 URLs | Unlimited |
| Rate limit | 100/min | 500/min | 2,000/min |
| Support | Community | Email | Priority |

## Next Steps

- [REST API Reference](REST-API-Reference) — All endpoints with examples
- [WebSocket Events](WebSocket-Events) — 42 real-time event types
- [Webhooks Guide](Webhooks-Guide) — Push notifications
- [Architecture Overview](Component-Bible) — How BlockSight is built
