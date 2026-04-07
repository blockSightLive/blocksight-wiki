# BlockSight.Live — Developer Documentation

Real-time Bitcoin blockchain explorer. API, WebSocket streaming, and webhook notifications.

**[blocksight.live](https://blocksight.live)** | **[API Docs](API-Overview.md)** | **[WebSocket Events](WebSocket-Events.md)** | **[Getting Started](Getting-Started.md)**

---

## For Developers

Connect your application to live Bitcoin data:

```bash
# Get current BTC price
curl "https://api.blocksight.live/api/v1/data/price?api_key=YOUR_KEY"

# Stream real-time events
wscat -c "wss://api.blocksight.live/ws?api_key=YOUR_KEY"
```

| Resource | Description |
|----------|-------------|
| [Getting Started](Getting-Started.md) | API key setup + first request in 5 minutes |
| [REST API Reference](REST-API-Reference.md) | All endpoints with request/response examples |
| [WebSocket Events](WebSocket-Events.md) | 42 real-time event types with payloads |
| [Webhooks Guide](Webhooks-Guide.md) | Push notifications with HMAC-SHA256 verification |

## For Engineers

How BlockSight was built — architecture, testing, and methodology:

| Resource | Description |
|----------|-------------|
| [Component Bible](Component-Bible.md) | 90 components mapped to specs, diagrams, and source |
| [Agent System](Agent-System.md) | 7 AI agents orchestrated via Claude Code |
| [Test Strategy](Test-Strategy.md) | 9-layer test pyramid: 27K tests, chaos, soak, visual regression |
| [ATDD Methodology](ATDD-Methodology.md) | Acceptance-test-driven development with CEO specifications |
| [The Vibe Coding Story](The-Vibe-Coding-Story.md) | One student, zero experience, pure English, one year |

## The Numbers

```
3,171 files  ·  799K lines  ·  27,441 tests  ·  90 components
25 languages  ·  42 WS events  ·  97.4% E2E  ·  0.00% soak errors
```

Built entirely with [Claude Code](https://claude.ai/claude-code) by one university student using English prompts.

---

*BlockSight OÜ · Registry 17474529 · Tallinn, Estonia*
