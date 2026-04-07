# BlockSight.Live

**One university student. Zero programming experience. Pure English prompts. Claude Code. One year.**

**3,171 files. 799,027 lines. 27,441 tests.**

---

## What is BlockSight?

BlockSight is a real-time Bitcoin blockchain explorer built entirely through AI-assisted development. It connects directly to a Bitcoin Core full node, processes live blockchain data through a multi-layer transformation pipeline, and presents it through a responsive dashboard with real-time WebSocket updates.

The public explorer shows live Bitcoin metrics: price, fees, congestion, mempool visualization, block history, and full transaction/address lookup. A customer portal provides API key management, webhook subscriptions, and tiered access. An admin dashboard gives the CEO operational control.

---

## The Numbers

| Metric | Value |
|--------|-------|
| Total files | 3,171 |
| Total lines of code | 799,027 |
| Test cases | 27,441 |
| Test suites | 1,395 |
| UI components | 90 |
| State machine diagrams | 94 |
| System architecture diagrams | 18 |
| CEO specification documents | 42 |
| Supported languages | 25 |
| MVPs defined and implemented | 35 |
| Backend domains (DDD) | 22 |
| REST API endpoints | ~195 |
| WebSocket event types | 38 |
| Prometheus metrics | 50 |
| E2E pass rate | 97.4% |
| Chaos engineering scenarios | 10/10 |
| Design tokens (CSS custom properties) | 400+ |

---

## How Was This Built?

BlockSight was built using [Claude Code](https://claude.ai/claude-code), Anthropic's AI coding assistant, directed entirely through English-language prompts. No code was hand-written. The human operator (Gabriel) defined product requirements, made business decisions, and validated results. Claude Code wrote every line.

To manage the complexity, the project developed a multi-agent orchestration system:

| Agent | Role |
|-------|------|
| **MEGA** | Strategic command center. Plans, delegates, archives. Never writes code. |
| **OMEGA** | Deep investigator. Root cause analysis, architecture design. |
| **ALPHA** | Staging and production evaluator. Deploys, tests, reports. |
| **BETA** | Systematic auditor. Spec compliance, code quality, test quality. |
| **GAMMA** | Code executor. The only agent that writes source code. |
| **DELTA** | Maintenance. Documentation freshness, system health, purity checks. |
| **LIMA** | i18n translator. One language per session, 25 languages total. |

Each agent has a defined role, reads from shared state files, and communicates through a folder-based task queue. MEGA orchestrates. Gabriel approves.

---

## Explore the Architecture

- [Component Bible](Component-Bible) -- Every component mapped to its spec, diagram, and source
- [Data Flow](Data-Flow) -- Bitcoin Core to browser, end to end
- [Cache Architecture](Cache-Architecture) -- L1 in-memory, L2 Redis, PostgreSQL persistence
- [WebSocket Architecture](WebSocket-Architecture) -- Real-time event system
- [Desktop Layout](Desktop-Layout) -- Triple-column dashboard
- [Tablet Layout](Tablet-Layout) -- Dual-column responsive
- [Phone Layout](Phone-Layout) -- Single-column mobile

## Engineering Practices

- [Test Strategy](Test-Strategy) -- 27K tests across unit, integration, E2E, chaos, k6
- [ATDD Methodology](ATDD-Methodology) -- Acceptance-test-driven development with CEO specs
- [Agent System](Agent-System) -- Multi-agent orchestration architecture
- [DDD Architecture](DDD-Architecture) -- Domain-driven design with 22 backend domains
- [CI/CD Pipeline](CI-CD-Pipeline) -- GitHub Actions to Vercel and Hetzner

## Design System

- [Design Tokens](Design-Tokens) -- 400+ CSS custom properties across 12 files
- [Responsive Strategy](Responsive-Strategy) -- 3 breakpoints, variant system, mobile-first
- [i18n Strategy](i18n-Strategy) -- 25 languages with lazy loading and currency mapping

## About

- [Technology Stack](Technology-Stack) -- Full stack overview
- [The Vibe Coding Story](The-Vibe-Coding-Story) -- How one student built this with AI
- [Statistics](Statistics) -- All the numbers, verified
- [Production Metrics](Production-Metrics) -- Quality progression across 27 cycles
- [Credits](Credits) -- The humans and tools behind BlockSight
