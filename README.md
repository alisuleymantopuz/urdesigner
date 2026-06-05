# urdesigner

Code-grounded product design and estimation for engineering teams.

Reads your codebase before speccing or estimating. Never invents architecture it hasn't seen. Uses senior engineering personas that cite evidence, surface risks, and mark "NEEDS SPIKE" when uncertain. Saves every artifact to dated files in `docs/` so outputs persist across sessions.

---

## Platform support

| Platform | Install |
|----------|---------|
| Claude Code | `claude plugin install ./urdesigner` |
| Cursor | Install via Cursor plugin marketplace or point to `.cursor-plugin/` |
| Codex | Install via Codex plugin marketplace or point to `.codex-plugin/` |
| Gemini CLI | `gemini extensions install ./urdesigner` or `gemini extensions link ./urdesigner` |

---

## Skills

### `/spec [feature or problem]`

Two senior personas review the codebase together, then produce a combined technical + UX spec.

**What it does:**
1. Reads `CLAUDE.md` and `STRATEGY.md` for product and business context
2. Reads relevant codebase files — only references what it actually reads
3. **Staff Engineer** produces: technical architecture, data flow, integration points, risks (LOW/MEDIUM/HIGH), constraints, technical open questions
4. **Design Lead** produces: user flows (happy path + error states + edge cases), design constraints, UX acceptance criteria, UX open questions
5. Combines both into a single spec document

**Output format:**
```
# Spec: [Feature Name]
## Summary
## Background
## Technical Architecture
## User Flows
## Technical Risks
## Design Constraints
## Acceptance Criteria
## Open Questions
## Out of Scope
```

**Saved to:** `docs/specs/YYYY-MM-DD-{feature-slug}.md`

---

### `/epic [spec or feature description]`

Breaks a spec into 3–7 prioritized, independently deliverable epics.

**What it does:**
1. Reads `CLAUDE.md` and `STRATEGY.md`
2. Reads relevant codebase to ground epic scope in reality
3. Applies risk-first prioritization — de-risking epics ship before value-add epics
4. Produces a sequencing diagram showing epic dependencies

**Each epic includes:**
- Scope and explicit out-of-scope
- Acceptance criteria (testable, observable)
- Dependencies (depends on / blocking)
- Rough size: XS / S / M / L / XL
- Rationale for its priority position

**Output also includes:** Risks and unknowns table, sequencing diagram, NEEDS SPIKE flags

**Saved to:** `docs/epics/YYYY-MM-DD-{feature-slug}.md`

---

### `/story [epic name or description]`

Decomposes an epic into 3–8 user stories, each independently workable within a sprint.

**What it does:**
1. Reads `CLAUDE.md` and `STRATEGY.md`
2. Reads the relevant codebase the epic touches
3. Validates each story against INVEST criteria (Independent, Negotiable, Valuable, Estimable, Small, Testable)

**Each story includes:**
- User story format: "As a [specific role], I want [goal], so that [benefit]"
- Given/When/Then acceptance criteria (binary pass/fail)
- Definition of Done
- Technical notes (only from code actually read)
- Dependencies within the epic
- Status flag: **READY** / **NEEDS_CLARIFICATION** / **NEEDS_SPIKE**

**Output also includes:** Deferred backlog with rationale, open questions for the team

**Saved to:** `docs/stories/YYYY-MM-DD-{epic-slug}.md`

---

### `/estimate [story, epic, or feature]`

Four senior engineering personas estimate independently from the actual codebase, then reach consensus.

**What it does:**
1. Reads `CLAUDE.md` and relevant codebase
2. Each persona reads their domain-specific files independently
3. All four estimate using Fibonacci points (1, 2, 3, 5, 8, 13, 21, ?)
4. If any two estimates diverge by **2x or more**, a discussion round runs to surface the disagreement before consensus

**Personas and domains:**

| Persona | Domain |
|---------|--------|
| Backend Engineer | APIs, DB schema, migrations, service layer, auth/authz, background jobs, external integrations |
| Frontend Engineer | UI components, state management, routing, API integration, accessibility, responsive design |
| QA Engineer | Test surface, edge cases, coverage gaps, regression risk, test infrastructure |
| DevOps Engineer | Migrations, infra changes, CI/CD, feature flags, monitoring/alerting, rollback strategy |

**Each persona provides:**
- List of files reviewed
- Estimate in Fibonacci points (or `?` if insufficient information)
- Justification with specific code evidence
- Confidence rating: HIGH / MEDIUM / LOW

**Consensus output includes:**
- Summary table across all personas
- Consensus estimate with method explanation
- Total cross-discipline effort signal
- Recommendation: READY TO SCHEDULE / NEEDS SPIKE / NEEDS SPLITTING
- Top risks to monitor

**Saved to:** `docs/estimates/YYYY-MM-DD-{story-slug}.md`

---

## Design principles

| Principle | What it means |
|-----------|---------------|
| **Planning only** | Skills never write, modify, or suggest code changes |
| **Code-grounded** | Every claim cites code that was actually read — no invented architecture |
| **Honest uncertainty** | `NEEDS SPIKE` is the output when evidence is insufficient — never a guess |
| **Reads context first** | All skills check `CLAUDE.md` and `STRATEGY.md` before producing output |
| **Persistent artifacts** | All outputs are saved to dated files in `docs/` — survives session resets |

---

## Workflow

The four skills form a natural pipeline:

```
/spec  →  /epic  →  /story  →  /estimate
```

Each skill's output is saved to `docs/` and can be passed directly as input to the next skill. Example:

```
/spec  add multi-tenant billing support
# → saves docs/specs/2026-06-05-multi-tenant-billing.md

/epic  docs/specs/2026-06-05-multi-tenant-billing.md
# → saves docs/epics/2026-06-05-multi-tenant-billing.md

/story  Epic 2: Stripe integration
# → saves docs/stories/2026-06-05-epic-2-stripe-integration.md

/estimate  Story 3: Webhook handler for invoice.paid events
# → saves docs/estimates/2026-06-05-webhook-handler-invoice-paid.md
```

---

## Agents

Agents are dispatched internally by skills. They are not invoked directly by users.

| Agent | Used in | Role |
|-------|---------|------|
| `staff-engineer` | `/spec` | Technical architecture, risks, constraints — reads code first |
| `design-lead` | `/spec` | User flows, design constraints, UX acceptance criteria — reads code first |
| `senior-backend-engineer` | `/estimate` | API, DB, migration, auth complexity |
| `senior-frontend-engineer` | `/estimate` | Component, state, routing, accessibility complexity |
| `senior-qa-engineer` | `/estimate` | Test surface, coverage gaps, regression risk |
| `senior-devops-engineer` | `/estimate` | Deployment risk, infra changes, rollback strategy, observability |

All agents: `model: inherit`, `tools: Read, Grep, Glob` (read-only — planning only).

---

## File structure

```
urdesigner/
├── plugin.json                        # Claude Code manifest
├── gemini-extension.json              # Gemini CLI manifest
├── GEMINI.md                          # Gemini context file
├── README.md
│
├── .cursor-plugin/
│   └── plugin.json                    # Cursor manifest
│
├── .codex-plugin/
│   └── plugin.json                    # Codex manifest (includes interface + defaultPrompt)
│
├── skills/
│   ├── spec/SKILL.md                  # /spec skill
│   ├── epic/SKILL.md                  # /epic skill
│   ├── story/SKILL.md                 # /story skill
│   └── estimate/SKILL.md              # /estimate skill
│
└── agents/
    ├── staff-engineer.md
    ├── design-lead.md
    ├── senior-backend-engineer.md
    ├── senior-frontend-engineer.md
    ├── senior-qa-engineer.md
    └── senior-devops-engineer.md
```

**Generated in each project (by the skills at runtime):**

```
docs/
├── specs/        YYYY-MM-DD-{feature}.md
├── epics/        YYYY-MM-DD-{feature}.md
├── stories/      YYYY-MM-DD-{epic}.md
└── estimates/    YYYY-MM-DD-{story}.md
```

---

## Project context files

urdesigner reads these files at the start of every skill if they exist:

| File | Purpose |
|------|---------|
| `CLAUDE.md` | Product goals, conventions, team standards |
| `GEMINI.md` | Same, for Gemini CLI sessions |
| `STRATEGY.md` | Business context, priorities, constraints |

None are required. If missing, the skill proceeds with codebase context only and notes the absence.
