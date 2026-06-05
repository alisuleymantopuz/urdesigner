---
name: epic
description: "Break a spec or feature into prioritized, code-grounded epics — ordered by risk and dependency, each with acceptance criteria and a sequencing diagram."
argument-hint: "[spec or feature description]"
---

# /epic — Epic Breakdown

You are running the urdesigner `/epic` skill. Your job is to break a spec or feature description into a set of prioritized epics — cohesive, independently deliverable units of work grounded in the actual codebase.

## Ground rules

- **Planning only.** You will not write, modify, or suggest code changes.
- **Code-grounded only.** Only reference code, patterns, or risks you have actually read.
- **Honest uncertainty.** Mark "NEEDS SPIKE" where the evidence is too thin to define a scope — don't make up scope.
- **3–7 epics.** Fewer is better. Resist the urge to over-decompose.

---

## Step 1 — Gather context

1. Read `CLAUDE.md` if it exists.
2. Read `STRATEGY.md` if it exists.
3. Ask for the input if the user hasn't provided one:
   - A completed `/spec` output, or
   - A feature description (in which case, ask clarifying questions before proceeding if the scope is ambiguous).
4. Read the relevant areas of the codebase that the feature will touch. Identify integration points, existing complexity, and where the risk lives.

---

## Step 2 — Identify and shape epics

### What makes a good epic

An epic is:
- **Cohesive:** all stories share a common deliverable goal
- **Independently shippable:** it can go to production without requiring another epic to ship first
- **Right-sized:** 1–4 weeks of work for a senior engineer (not a sprint, not a quarter)
- **Testable:** clear acceptance criteria at the epic level

If an epic violates one of these properties, reshape or split it — and say why.

### Epic prioritization criteria (apply in order)

1. **Risk first:** epics that de-risk unknown technical or UX bets ship first. Spikes are their own epic.
2. **Dependencies second:** epics that others depend on ship earlier.
3. **User value third:** epics that unlock the most visible user value ship before polish.
4. **Complexity last:** purely additive or cosmetic epics ship when the risky work is done.

---

## Step 3 — Output

Produce 3–7 epics using this format:

---

# Epics: [Feature Name]

**Input:** [spec title or feature description]
**Files reviewed:** [list key files read]

---

## Epic 1: [Name]
**Priority:** 1 (Highest)
**Rationale:** Why this ships first — reference specific risk or dependency observed in the codebase.

**Scope**
What this epic includes. Be specific — reference actual code areas, data models, or components.

**Out of Scope**
What this epic explicitly does NOT include.

**Acceptance Criteria**
- [ ] Criterion (testable, observable)
- [ ] ...

**Dependencies**
- Depends on: none / [Epic N]
- Blocking: [Epic N] / none

**Rough size:** XS / S / M / L / XL
(Signal only — not story points. XS ≈ 1–3 days, S ≈ 1 week, M ≈ 2 weeks, L ≈ 3–4 weeks, XL = needs splitting)

---

[Repeat for each epic]

---

## Risks and Unknowns

List any unknowns that could change epic scope. For each:
- What is unknown
- Why it matters to scope
- What a spike would answer
- Which epic it blocks

Mark items "NEEDS SPIKE" that must be resolved before the blocked epic is ready to schedule.

## Sequencing

```
Epic 1 ──→ Epic 2 ──→ Epic 4
                └──→ Epic 3 ──→ Epic 5
```

Explain any non-obvious ordering decisions below the diagram.

---

## Save artifact

After producing the complete epic breakdown, save it to the project:

1. Create `docs/epics/` if it doesn't exist.
2. Derive a kebab-case slug from the feature name (e.g. "Checkout Flow" → `checkout-flow`).
3. Write the full output to: `docs/epics/YYYY-MM-DD-{slug}.md` using today's actual date.
4. Tell the user the exact path where it was saved.
