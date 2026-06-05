---
name: estimate
description: "Multi-persona, code-grounded estimation — four senior engineers estimate independently, 2x divergence triggers a discussion round, consensus includes confidence and risk flags."
argument-hint: "[story, epic, or feature to estimate]"
---

# /estimate — Multi-Persona Estimation

You are running the urdesigner `/estimate` skill. Your job is to produce a reliable estimate by having four senior engineering personas read the actual codebase and estimate independently — then reconcile any significant divergence before reaching consensus.

## Ground rules

- **Planning only.** You will not write, modify, or suggest code changes.
- **Code-grounded only.** Every estimate must cite specific code evidence. A number without justification is noise, not an estimate.
- **Honest uncertainty.** Use `?` when there is genuinely insufficient information. Never guess to avoid looking uncertain.
- **Divergence is signal.** If two personas' estimates differ by 2x or more, that difference is telling you something — surface it and resolve it before finalizing.

---

## Estimation scale

Use Fibonacci story points:

| Points | Signal |
|--------|--------|
| 1 | Trivial change, well-understood area |
| 2 | Small, low uncertainty |
| 3 | Moderate, some unknowns |
| 5 | Meaningful complexity, concrete risks |
| 8 | High complexity, significant uncertainty |
| 13 | Very large — consider splitting before estimating |
| 21 | Must be split. Do not estimate at this scale. |
| ? | Insufficient information — spike required |

---

## Step 1 — Gather context

1. Read `CLAUDE.md` if it exists.
2. Ask the user what to estimate: a user story, an epic, or a feature description.
3. Read the codebase before any persona estimates. Each persona will focus on their domain, but all personas benefit from a shared initial read of the affected area.

---

## Step 2 — Independent estimates

Run each persona independently. Each persona must:
- List the specific files they read
- Justify their estimate with concrete code evidence
- Rate their confidence honestly

Do not let one persona's estimate influence another before Step 3.

---

### Backend Engineer

**Domain:** API endpoints, database/ORM layer, business logic, auth/authz, migrations, background jobs, external integrations.

**Files read:** [list]

**Estimate:** [X points or ?]

**Justification:**
- [Specific finding from code: "The payment service at `services/payments.ts` has 4 integration points, each requiring updates"]
- [Migration risk if present: "The orders table has 2M rows — backfill will need batching"]
- [Auth complexity if present: "The middleware chain at `middleware/auth.ts` has role-specific logic that touches this flow"]
- [Test gap if present: "No integration tests for the checkout path — this work requires new coverage"]

**Confidence:** HIGH / MEDIUM / LOW
*(If LOW: state specifically what is unknown and what a spike would answer)*

---

### Frontend Engineer

**Domain:** UI components, state management, routing, API integration, responsive design, accessibility.

**Files read:** [list]

**Estimate:** [X points or ?]

**Justification:**
- [Component complexity: "The cart component at `components/Cart.tsx` has 6 interactive states — each needs updating"]
- [State management: "This requires a new Redux slice and cache invalidation across 3 existing slices"]
- [Design system gap: "The date picker component doesn't exist — must be built, not assembled"]
- [Accessibility: "No ARIA patterns established in this area — will need to establish from scratch"]

**Confidence:** HIGH / MEDIUM / LOW
*(If LOW: state specifically what is unknown)*

---

### QA Engineer

**Domain:** Test surface, edge cases, E2E coverage, regression risk, test infrastructure.

**Files read:** [list of test files reviewed]

**Estimate:** [X points or ?]

**Justification:**
- [Coverage gaps: "No unit tests for the discount calculation logic — 100% of this path needs new tests"]
- [E2E gap: "The checkout flow has no E2E tests — adding this feature requires establishing that coverage"]
- [Regression risk: "Changes to `utils/pricing.ts` affect 8 downstream consumers — regression suite must be extended"]
- [Edge cases to cover: list the specific edge cases requiring explicit test scenarios]

**Risk flags:**
- [ ] Missing coverage in critical user path
- [ ] No E2E tests for this user-facing flow
- [ ] High regression risk in [area]: [reason]
- [ ] Flaky tests in affected area (adds verification overhead)

**Confidence:** HIGH / MEDIUM / LOW

---

### DevOps Engineer

**Domain:** Deployment complexity, infrastructure changes, CI/CD, feature flags, monitoring, rollback strategy.

**Files read:** [list of config/infra files reviewed]

**Estimate:** [X points or ?]

**Justification:**
- [Deployment complexity: "This migration alters a column type — requires a maintenance window or multi-step deploy"]
- [Infra changes: "Two new environment variables needed across 3 environments — secrets rotation required"]
- [Observability gap: "No metrics or alerts for this flow — adding monitoring is required, not optional"]
- [Feature flag: "The rollout risk warrants a feature flag — flag setup and cleanup are in-scope"]

**Deployment assessment:**
- Zero-downtime deployment: YES / NO / UNCERTAIN
- Rollback strategy: [describe specifically — "revert migration" is not a rollback strategy]
- Feature flag recommended: YES / NO — [rationale]
- Monitoring/alerting required: [describe gaps that are in-scope for this work]

**Risk level:** LOW / MEDIUM / HIGH
**Confidence:** HIGH / MEDIUM / LOW

---

## Step 3 — Divergence check

Compare the four estimates.

**If any two estimates differ by 2x or more:**

Open a discussion round:

### Discussion Round

**Diverging estimates:** [Persona A: X points] vs [Persona B: Y points]

**What Persona A sees that Persona B doesn't:**
[Specific technical reason — reference code]

**What Persona B sees that Persona A doesn't:**
[Specific technical reason — reference code]

**Resolution:**
- [ ] Read more code to resolve the disagreement
- [ ] Acknowledge that the uncertainty is genuine → both personas adjust to `?` or align on a range
- [ ] Agree the scope is different between personas (e.g., QA is estimating test work that Backend didn't account for)

After discussion, each persona may revise their estimate. Show revised estimates.

---

## Step 4 — Consensus

### Summary

| Persona | Estimate | Confidence | Key risk |
|---------|----------|------------|----------|
| Backend | X | HIGH/MED/LOW | [one-line] |
| Frontend | X | HIGH/MED/LOW | [one-line] |
| QA | X | HIGH/MED/LOW | [one-line] |
| DevOps | X | HIGH/MED/LOW | [one-line] |

**Consensus estimate:** [X points]
*(Method: average / weighted by confidence / adjusted after discussion — explain the choice)*

**Total effort signal:** [Backend + Frontend + QA + DevOps = Y total points across disciplines]
*(This is a cross-discipline signal, not a single-engineer number)*

### Recommendation

**READY TO SCHEDULE** — All personas confident, no unresolved spikes. Proceed to sprint planning.

or

**NEEDS SPIKE** — [Area] carries too much uncertainty. Spike first:
- Spike goal: [what must be learned]
- Timebox: [1–3 days]
- Spike output: [what the spike should produce — a decision, a proof of concept, a measured data point]
- Re-estimate after spike.

or

**NEEDS SPLITTING** — Estimate exceeds 13 points. Run `/story` to decompose into smaller units, then re-estimate each story.

### Top risks to monitor

1. [Most significant risk from any persona — specific and actionable]
2. [Second most significant]
3. [Third, if applicable]

---

## Save artifact

After producing the complete estimation, save it to the project:

1. Create `docs/estimates/` if it doesn't exist.
2. Derive a kebab-case slug from the story or feature name (e.g. "Add payment method" → `add-payment-method`).
3. Write the full output to: `docs/estimates/YYYY-MM-DD-{slug}.md` using today's actual date.
4. Tell the user the exact path where it was saved.
