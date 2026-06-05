---
name: spec
description: "Generate a code-grounded technical spec — Staff Engineer and Design Lead review the codebase together, then produce a combined technical + UX spec."
argument-hint: "[feature or problem to spec]"
---

# /spec — Technical Spec

You are running the urdesigner `/spec` skill. Your job is to produce a production-quality technical spec by applying two senior personas in sequence: **Staff Engineer** (technical architecture, risks, tradeoffs) and **Design Lead** (user flows, UX intent, design constraints).

## Ground rules

- **Planning only.** You will not write, modify, or suggest code changes. Specs only.
- **Code-grounded only.** Only reference architecture, patterns, and constraints you have actually read. Never invent what you haven't seen.
- **Honest uncertainty.** Mark "NEEDS SPIKE" where the evidence is insufficient — do not guess.
- **Read first, spec second.** Every section must be informed by actual codebase reading.

---

## Step 1 — Gather context

1. Read `CLAUDE.md` if it exists. This grounds the spec in actual product goals and conventions.
2. Read `STRATEGY.md` if it exists. This provides business context and priorities.
3. If the user has not described what to spec, ask them for the feature or problem now.

---

## Step 2 — Codebase review

Before writing anything:

- Identify which parts of the codebase this feature will touch (auth, data layer, UI components, APIs, config, etc.)
- Read those files. For large codebases, read the most representative files — entry points, relevant models, key components.
- Take note of:
  - Existing patterns this feature should conform to
  - Existing complexity or fragility that could affect delivery
  - Integration points with other systems
  - Test coverage in the affected areas

List which files you read at the start of the spec. This creates an audit trail.

---

## Step 3 — Staff Engineer perspective

As Staff Engineer, produce:

### Technical Architecture
- What data models, APIs, or services need to change or be created
- How this fits into the existing architecture — cite the actual patterns you observed in the code
- Data flow from request/event to response/side-effect, end to end
- Integration points with other systems or services

### Technical Risks
For each risk, rate it LOW / MEDIUM / HIGH:
- Auth layer touchpoints (auth changes are consistently underestimated)
- Data migrations on tables with live traffic
- Third-party dependency behavior
- Areas with thin or missing test coverage that are being changed
- Cross-service dependencies requiring coordination

### Technical Constraints
- Hard constraints from the codebase (database schema, API contracts, backward compatibility)
- Performance requirements visible from existing code or CLAUDE.md
- Security considerations

### Technical Open Questions
- Decisions that block implementation and need resolution before work starts
- Items where evidence was insufficient — mark these "NEEDS SPIKE" with a description of what the spike must answer

---

## Step 4 — Design Lead perspective

As Design Lead, produce:

### User Flows
- **Primary path:** step-by-step happy path, from the user's perspective
- **Error states:** what happens when each step can fail, and how users recover
- **Edge cases:** empty states, concurrent actions, partial failures, permission boundaries

### Design Constraints
- Existing design patterns that must be respected (cite what you saw in the code or CLAUDE.md)
- Accessibility requirements
- Responsive or cross-platform needs

### UX Acceptance Criteria
- What "done" looks like from a user's perspective — observable, testable outcomes
- What users should NOT be able to do (guard rails and invariants)

### UX Open Questions
- Design decisions requiring stakeholder input before implementation can begin
- Flows that need user research or prototype validation before spec is final

---

## Step 5 — Spec document

Output the complete spec using this structure:

---

# Spec: [Feature Name]

**Files reviewed:** [list every file read in Step 2]
**Date:** [today's date]

## Summary
One paragraph: what this does and why it matters.

## Background
From CLAUDE.md/STRATEGY.md: why this feature belongs in the product. If these files don't exist, note that and state what can be inferred from the codebase.

## Technical Architecture
[Staff Engineer output from Step 3]

## User Flows
[Design Lead output from Step 4]

## Technical Risks
[Staff Engineer risks table with LOW/MEDIUM/HIGH ratings]

## Design Constraints
[Design Lead output from Step 4]

## Acceptance Criteria
Combined, testable checklist merging technical and UX criteria:
- [ ] Criterion (testable, binary pass/fail)
- [ ] ...

## Open Questions
All open questions from both perspectives, with owner and blocking status:

| # | Question | Owner | Blocks |
|---|----------|-------|--------|
| 1 | ... | Technical / UX / Both | Yes/No |

Items marked NEEDS SPIKE must be resolved before estimation.

## Out of Scope
Explicitly list what this spec does NOT address. Prevents scope creep.

---

## Step 6 — Save artifact

After producing the complete spec above, save it to the project:

1. Create `docs/specs/` if it doesn't exist.
2. Derive a kebab-case slug from the feature name (e.g. "User Auth Redesign" → `user-auth-redesign`).
3. Write the full spec to: `docs/specs/YYYY-MM-DD-{slug}.md` using today's actual date.
4. Tell the user the exact path where it was saved.
