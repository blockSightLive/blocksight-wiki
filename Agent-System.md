# Agent System

BlockSight is built by a team of specialized AI agents working in parallel, each with a distinct role, folder-based task routing, and strict boundaries. This is not prompt engineering — it's a production orchestration system with state management, dependency tracking, and quality gates.

## The Hierarchy

```
MEGA (Strategic Command Center — with CEO/CTO)
  |
  |-- OMEGA (Deep Investigator — produces root cause reports)
  |
  |-- ALPHA (Production Evaluator — SSH to production, runs tests)
  |     \-- Spins primary/secondary subagents as needed
  |
  |-- BETA (Quality Auditor — 59-component audit system)
  |
  |-- GAMMA A/B/C (3 Parallel Code Executors — TDD, max 2 tasks/session)
  |
  |-- DELTA (Maintenance — docs, health, purity, peer metrics)
  |
  |-- SIGMA (Readiness Assessor — 7-dimension component evaluation)
  |
  \-- LIMA (i18n Translator — 1 language per session, 25 languages)
```

## Agent Roles

### MEGA — The Architect

MEGA never writes code. It reads code, queries production via MCP (Bitcoin Core, PostgreSQL, Fulcrum), and makes strategic decisions. Every `/mega` cycle follows 8 phases:

**Orient → Assess → Compress → Delegate → Monitor → Archive → Plan → Report**

The core metric is **compression**: is the project shrinking toward done? If the roadmap grew, something went wrong.

### OMEGA — The Investigator

When something is broken and the root cause is unclear, OMEGA investigates. It reads source code, checks production logs via SSH, queries databases, and produces detailed reports with exact file:line references.

Example: "PaymentWatcher never detects payments" → OMEGA found 3 root causes in 2 hours, produced a fix spec that GAMMA implemented in 30 minutes.

### ALPHA — The Production Evaluator

ALPHA SSHs into the production server and runs the evaluation suite: E2E tests, k6 performance, Playwright visual tests, chaos testing, coverage measurement. It grades each cycle (A through F) and produces fix plans.

27 production cycles completed. Best: Grade A (97.4% E2E, 24/24 k6, 10/10 chaos).

### BETA — The Quality Auditor

Systematic 59-component audit: spec creation, code audit, test audit, fix generation. Each component is audited against its state machine diagram and CEO specification.

### GAMMA A/B/C — The Code Executors

Three parallel GAMMA agents execute from work queues (pool-a, pool-b, pool-c). Each follows strict TDD: read spec, write failing test, implement fix, verify, commit message.

Rules: max 2 tasks per session, single commit per task, no scope creep.

### DELTA — The Maintenance Agent

Runs 8 health tools: doc freshness, catalog refresh, metrics, purity regression, architecture violations, system health, peer metrics, compliance scoring.

### SIGMA — The Readiness Assessor

Evaluates components across 7 dimensions: spec accuracy, data truth (via MCP queries), data completeness, visual quality, test quality, code quality, decision status. 25/25 components assessed.

### LIMA — The i18n Translator

One language per session. Translates all keys (1,700+), validates placeholders, registers in config. 25 languages targeted. Strict guardrails: never modify English, never delete keys, mark uncertain translations with [REVIEW].

## Task Routing

Tasks flow through folder-based queues, not conversations:

```
MEGA writes task → pool-a/WQ-741.json (status: "queued")
GAMMA A reads pool-a/ → claims WQ-741 (status: "claimed")
GAMMA A completes work → (status: "completed", result: "...")
MEGA reads result → queues next task or closes workstream
```

No agent-to-agent communication except via task files and a notification board.

## The Four Pillars

Every agent, every task, every session follows these non-negotiable principles:

1. **Depth over breadth**: One thing done excellently beats five things done adequately
2. **Quality over quantity**: A test that catches real bugs beats ten tests that always pass
3. **Architecture over shortcuts**: Fix the root cause, never apply band-aids
4. **Structure over half-measures**: Do it now, not later. Every TODO is a structural gap

## What Makes This Unique

Most "AI-assisted" projects use AI as a code completion tool. BlockSight uses AI as a **development team** with:

- **925+ completed tasks** across all agents
- **Persistent state** across sessions (tracks.json, task files, state machines)
- **Separation of concerns**: MEGA never writes code, GAMMA never investigates, OMEGA never deploys
- **Quality gates**: Every fix is TDD, every deploy is evaluated, every component is audited
- **Evidence-first protocol**: Agents check sources and cite file:line references, never guess

The result: a 799K-line codebase with 27,000+ tests, 92%+ coverage, and zero unplanned technical debt.
