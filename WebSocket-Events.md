# WebSocket Events — Real-Time Streaming

## Connection

```javascript
const ws = new WebSocket('wss://api.blocksight.live/ws?api_key=YOUR_KEY');

ws.onopen = () => {
  // Subscribe to event types
  ws.send(JSON.stringify({
    type: 'subscribe',
    payload: {
      eventTypes: ['price_update', 'block_added', 'fee_update']
    }
  }));
};

ws.onmessage = (event) => {
  const data = JSON.parse(event.data);
  console.log(data.type, data.data);
};
```

## Event Envelope

Every WebSocket event follows this structure:

```json
{
  "type": "price_update",
  "data": { ... },
  "timestamp": 1712534400000,
  "meta": {
    "producedInMs": 2
  }
}
```

## Dashboard Events (High Frequency)

### price_update (every 2s)

```json
{
  "type": "price_update",
  "data": {
    "usd": 69310.80,
    "change24h": -659.20,
    "changePercent24h": -0.94,
    "source": "kraken",
    "lastUpdated": 1712534400000
  }
}
```

### fee_update (every 30s)

```json
{
  "type": "fee_update",
  "data": {
    "nextBlockFee": 1.1,
    "sixHourFee": 1.0,
    "isReliable": true
  }
}
```

### congestion_update (every 30s)

```json
{
  "type": "congestion_update",
  "data": {
    "congestionScore": 12,
    "congestionLevel": "low",
    "mempool": {
      "txCount": 3514,
      "vsizeBytes": 4500000,
      "minFeeRate": 1.0
    },
    "fees": {
      "fastest": 1.1,
      "halfHour": 1.0,
      "hour": 1.0,
      "economy": 1.0
    },
    "delayScore": 0,
    "activityLoad": 8.2,
    "timestamp": 1712534400000
  }
}
```

## Block Events

### block_added

Fired when a new block is mined and confirmed.

```json
{
  "type": "block_added",
  "data": {
    "height": 944100,
    "hash": "00000000000000000001...",
    "timestamp": 1712534400,
    "txCount": 3200,
    "size": 1500000,
    "weight": 3990000
  }
}
```

### blocks_history

Sent on initial connection — recent block summaries for display.

```json
{
  "type": "blocks_history",
  "data": {
    "blocks": [
      { "height": 944100, "hash": "...", "txCount": 3200, "minFeeRate": 1.0, "maxFeeRate": 150.0, "minedAtMs": 1712534400000 },
      { "height": 944099, "hash": "...", "txCount": 5442 }
    ]
  }
}
```

## Mempool Events

### mempool_update (every 5s)

```json
{
  "type": "mempool_update",
  "data": {
    "txCount": 3514,
    "size": 4500000,
    "bytes": 4500000,
    "minfee": 0.00000001
  }
}
```

### mempool_blocks (every 30s)

Projected mempool blocks for fee estimation.

```json
{
  "type": "mempool_blocks",
  "data": {
    "blocks": [
      { "position": 1, "txCount": 2800, "minFeeRate": 1.0, "maxFeeRate": 150.0 }
    ]
  }
}
```

## System Events

### system:status (on connect)

```json
{
  "type": "system:status",
  "data": {
    "uptime": 345600,
    "bootstrapComplete": true,
    "circuitBreakers": {
      "allClosed": true
    }
  }
}
```

## All 42 Event Types

| Category | Events | Frequency |
|----------|--------|-----------|
| Dashboard | price_update, fee_update, congestion_update, dashboard.update, ath_update, supply_update, fx.rates, exchange.rates | 2-30s |
| Block | block_added, blocks_history, tip.height, blockchain.info, network.mining, chain.reorg | On event |
| Mempool | mempool_update, mempool_blocks, congestiongauge.metrics, network.fees, network.mempool | 5-30s |
| System | health_update, system:status, electrum.chain-tip, visualization:update, milestone:update | On event |

## Reconnection Strategy

```javascript
let reconnectDelay = 1000;

ws.onclose = (event) => {
  if (event.code === 4002) {
    // Authentication failed — don't reconnect
    console.error('Invalid API key');
    return;
  }
  // Exponential backoff with jitter
  setTimeout(() => {
    reconnectDelay = Math.min(reconnectDelay * 2, 30000);
    connect();
  }, reconnectDelay + Math.random() * 1000);
};
```

## Close Codes

| Code | Meaning |
|------|---------|
| 1000 | Normal closure |
| 4002 | Authentication failed (invalid API key) |
| 4003 | Rate limit exceeded |
| 4004 | Subscription tier does not include this event |
