# The Vibe Coding Story

## One Student, Zero Code Experience, One Year

Gabriel is a university student who had never written a line of code. No computer science degree. No bootcamp. No prior programming experience of any kind.

In early 2025, he started building a Bitcoin blockchain explorer using [Claude Code](https://claude.ai/claude-code), Anthropic's AI coding assistant. Every instruction was given in plain English. Every line of code was written by the AI. Gabriel's role was to define what he wanted, evaluate the results, and make decisions.

One year later, the project has 799,027 lines of code, 27,441 tests, 90 UI components, and 25 language translations.

This is the story of how that happened.

---

## The Beginning

The first version was simple: a dashboard that showed the current Bitcoin price. One component, one API call, one number on screen.

From there, each feature was described in English: "Add a fee gauge that shows the estimated transaction fee." "Show the last 10 blocks with their height and transaction count." "Make the blocks animate when a new one is mined."

Claude Code translated these descriptions into React components, Node.js services, PostgreSQL queries, and WebSocket events. Gabriel didn't need to understand the code. He needed to understand the product.

---

## The Complexity Ramp

What started as a simple price display grew into a production system:

- A full Bitcoin Core RPC integration with ZMQ real-time events
- Seven data transformation pipelines processing raw blockchain data
- A multi-tier caching system (in-memory LRU + Redis + PostgreSQL)
- A WebSocket server with 38 event types and tier-based authentication
- A customer portal with API key management, webhooks, and billing
- An admin dashboard for the CEO

Each feature was described in English and built by Claude Code. But as the codebase grew, so did the coordination challenge. You can't tell an AI "build the whole thing" when the thing is 800,000 lines of interconnected systems.

---

## The Agent System

The solution was a multi-agent orchestration system — seven specialized AI agents, each with a defined role:

- **MEGA** thinks and plans. It never writes code.
- **OMEGA** investigates. When something is broken, OMEGA finds the root cause.
- **GAMMA** executes. It is the only agent that writes source code.
- **ALPHA** evaluates. After each deployment, ALPHA tests everything and reports back.
- **BETA** audits. Systematic quality sweeps across the entire codebase.
- **DELTA** maintains. Documentation freshness, system health, purity checks.
- **LIMA** translates. One language per session, 25 languages total.

Each agent reads from shared state files and communicates through a folder-based task queue. MEGA queues work, GAMMA builds it, ALPHA validates it, Gabriel approves it.

---

## The Engineering Discipline

Here is the part that surprises people: AI-assisted development requires **more** engineering discipline, not less.

When a human writes code, they build intuition about what they've changed and what might break. When an AI writes code from English descriptions, that intuition doesn't exist. The AI might produce code that passes type checks and looks reasonable but has subtle logic errors that only surface in production.

BlockSight's response to this challenge:

- **94 state machine diagrams** documenting every component's states, transitions, and edge cases
- **42 CEO specification documents** defining exactly what each feature should do
- **Acceptance Test-Driven Development (ATDD)** where the CEO's acceptance criteria are the test specifications
- **Domain purity enforcement** — the functional core has zero impurities (no I/O, no side effects)
- **Circuit breakers** on every external connection
- **Chaos engineering** — deliberately kill Redis, kill PostgreSQL, partition the network, verify the system recovers

Every one of these practices was adopted because the AI made a mistake that it didn't. The 94 state machine diagrams exist because a component's behavior was wrong and nobody noticed until the CEO tested it manually. The chaos engineering exists because the system crashed in production and the recovery path hadn't been tested.

---

## The Honest Numbers

27,441 tests. 97.4% E2E pass rate. 10/10 chaos scenarios passing.

These are real numbers, but they come with an honest caveat: the CEO has found bugs that 27,441 tests missed. Five minutes of manual testing uncovered issues that automated tests had covered with assertions that always pass.

This led to a systematic audit of test quality. Not "do we have tests?" but "do these tests actually catch real bugs?" The answer, in some cases, was no — tests that checked if a component rendered, but not if it rendered the correct data.

The project's test quality improved dramatically after this discovery. But it's an important lesson: test count is not test quality. Coverage percentage is not correctness.

---

## What It Proved

BlockSight proves that AI-assisted development can produce a system of significant scale and complexity. 799,027 lines. 22 backend domains. Three separate web applications. Real-time data from a Bitcoin full node. 25 languages. Production deployment.

It also proves that AI-assisted development is not a shortcut. The agent system, the state machine diagrams, the acceptance criteria, the chaos testing, the domain purity enforcement — all of this is engineering discipline. The AI writes the code, but the discipline makes it work.

The thesis is not "AI replaces programmers." The thesis is: AI changes **who** can build software, but not **what** it takes to build software well.

---

## The Numbers at a Glance

| What | How Much |
|------|----------|
| Lines of code | 799,027 |
| Files | 3,171 |
| Test cases | 27,441 |
| UI components | 90 |
| Backend domains | 22 |
| State machine diagrams | 94 |
| CEO specs | 42 |
| Languages | 25 |
| E2E pass rate | 97.4% |
| Development time | ~1 year |
| Human programming experience | Zero |
| Lines of code written by hand | Zero |
