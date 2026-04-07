# Credits

## The Humans

**Gabriel Osemberg** -- CEO, Product Owner, and the person who directed every aspect of BlockSight's development through English-language prompts. University student with no prior programming experience. Defined the product vision, wrote the CEO specifications, validated every feature, and found the bugs that 27,441 tests missed.

**Daniel** -- CEO (business side). Designed the core algorithms: congestion scoring, fee estimation heuristics, mempool projection, block time prediction. Wrote the detailed business specifications that guided the technical implementation. Partner pricing, tier structure, and billing architecture.

---

## The AI

**Claude Code** by [Anthropic](https://anthropic.com) -- The AI coding assistant that wrote every line of BlockSight's source code. Claude Code operates as a CLI tool that reads project context, understands the codebase, and generates code from natural language instructions.

The project evolved a multi-agent system where different instances of Claude Code take on specialized roles:

| Agent | Role |
|-------|------|
| **MEGA** | Strategic architect. Plans work, delegates tasks, monitors progress. Never writes code. |
| **OMEGA** | Deep investigator. Root cause analysis, architecture design, task specification. |
| **ALPHA** | Deployment evaluator. Deploys to staging/production, runs tests, reports results. |
| **BETA** | Quality auditor. Systematic spec compliance and code quality audits. |
| **GAMMA** | Code executor. The only agent that modifies source files. Works from detailed task specs. |
| **DELTA** | Maintenance agent. Documentation freshness, system health, purity regression checks. |
| **LIMA** | Translation agent. Processes one language per session with quality guardrails. |

---

## Open Source Foundation

BlockSight is built on the work of many open source projects and communities:

- **Bitcoin Core** -- The reference implementation of the Bitcoin protocol
- **Fulcrum** -- High-performance Electrum protocol server
- **React** -- UI framework by Meta
- **Node.js** -- Server-side JavaScript runtime
- **TypeScript** -- Type-safe JavaScript by Microsoft
- **PostgreSQL** -- Relational database
- **Redis** -- In-memory data store
- **Vite** -- Build tool by Evan You
- **Jest** -- Testing framework by Meta
- **Playwright** -- Browser testing by Microsoft
- **Hono** -- Lightweight web framework
- **i18next** -- Internationalization framework
- **Cloudflare** -- CDN and security
- **Vercel** -- Frontend deployment platform

And many more dependencies that make modern web development possible.
