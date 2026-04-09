# Development Process

BlockSight follows an **Acceptance Test-Driven Development (ATDD)** pipeline. Every change — feature, bug fix, or optimization — goes through the same 7-phase process. This ensures nothing ships without acceptance criteria, tests, and validation against production.

---

## The 7-Phase Pipeline

### Phase 1: SPECIFY
Define what the change should do. Write acceptance criteria in a structured format. Map the component to its state machine diagram, system diagram, and CEO specification.

### Phase 2: TEST
Write failing tests **before** writing any code. Tests express the acceptance criteria:
- Unit tests for domain logic
- Integration tests for service-to-service wiring
- E2E tests for API contracts
- Playwright tests for browser behavior

The tests must fail — if they pass, the criteria were already met or the tests are wrong.

### Phase 3: CODE
Implement the minimum code to make the failing tests pass. No more, no less. Follow the existing architecture patterns (DDD boundaries, design tokens, shared schemas).

### Phase 4: VALIDATE
Deploy to production and evaluate. Run the full test suite against live infrastructure:
- E2E tests against Bitcoin Core + Fulcrum + PostgreSQL
- k6 performance baselines
- Chaos engineering scenarios
- Coverage verification

### Phase 5: VERIFY
Visual QA at all 3 viewports (375px phone, 768px tablet, 1440px desktop). Screenshot comparison against baseline. Real-device testing for critical flows.

### Phase 6: REFRESH
Update all documentation affected by the change:
- State machine diagrams
- Component Bible entries
- Test coverage matrix
- Requirements Traceability Matrix

### Phase 7: DONE
Archive the work. Update the changelog. The change is live and documented.

---

## The 5-Layer Test Model

Every user-facing flow is tested at 5 independent layers. Each layer catches different classes of bugs:

| Layer | Tool | What It Catches |
|-------|------|----------------|
| **Unit** | Jest | Logic errors in isolated functions |
| **Integration** | Jest + real DB/Redis | Service wiring, DB queries, cache flows |
| **E2E** | Jest + fetch | API contracts, data accuracy vs Bitcoin Core |
| **User Journey** | Playwright | Full browser: click → API → cookie → redirect → render |
| **Resilience** | k6 + chaos | Memory leaks, circuit breaker recovery, cascade failures |

**Key insight**: E2E and User Journey are different layers. E2E tests `POST /login` and checks JSON. User Journey fills the email field, clicks Sign In, waits for redirect, and verifies the dashboard rendered.

---

## Requirements Traceability Matrix (RTM)

The RTM maps 27 user-facing flows across all 5 test layers. Each cell is marked FULL, PARTIAL, or GAP.

| Category | Flows | Coverage |
|----------|-------|----------|
| Revenue-critical | 8 | 5 FULL, 3 PARTIAL |
| Explorer journeys | 8 | 8 FULL |
| WebSocket real-time | 4 | 3 FULL, 1 PARTIAL |
| Admin operations | 4 | 4 FULL |
| Responsive viewports | 3 | 3 FULL |
| **Total** | **27** | **23 FULL, 4 PARTIAL, 0 GAP** |

---

## Component Bible

Every component in the system has an entry in the Component Bible — a unified hub that links:
- **Source files** — where the code lives
- **State machine diagram** — all possible states and transitions
- **System diagram** — how it interacts with other components
- **CEO specification** — the original product intent
- **Acceptance criteria** — what "correct" means
- **Test matrix** — which layers have coverage

When you change a component, the Bible tells you exactly which documentation needs updating. This prevents orphaned changes where code diverges from its specification.

---

## Quality Gates

Before any change merges:

1. **TypeScript** — zero errors (`tsc --noEmit`)
2. **Lint** — zero warnings (Biome)
3. **Unit tests** — all pass
4. **Architecture** — zero DDD violations (dependency-cruiser)
5. **Coverage** — does not drop below baseline
6. **Design tokens** — zero hardcoded colors, spacing, or fonts in CSS
7. **Domain purity** — zero impurities in domain layer (no console.*, no process.env, no Date.now)

---

## Why This Process Exists

BlockSight is a 24/7 production system displaying real Bitcoin blockchain data. Every number on screen has a source of truth — the actual Bitcoin network. When we assert that a block has a specific fee, we verify against Bitcoin Core RPC, not a mock.

The ATDD pipeline exists because:
- **Mock-based testing hides real bugs.** We learned this when mocked tests passed but production showed wrong fee estimates.
- **Structure-only tests give false confidence.** A test that checks "page renders" passes even when all numbers show "--".
- **Documentation drift causes regression.** If the state machine diagram says "3 states" but the code has 5, someone will break the unlisted states.

The hard path — writing acceptance criteria first, testing at 5 layers, documenting everything — is the only path that works for a production system where accuracy is the product.
