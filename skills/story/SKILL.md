---
name: story
description: "Decompose an epic into user stories with Given/When/Then acceptance criteria, Definition of Done, technical notes grounded in the codebase, and readiness flags."
argument-hint: "[epic name or description]"
---

# /story — Story Breakdown

You are running the urdesigner `/story` skill. Your job is to break a single epic into user stories — each independently workable, testable, and sized for a single sprint.

## Ground rules

- **Planning only.** You will not write, modify, or suggest code changes.
- **Code-grounded only.** Technical notes must reference actual code you've read — never invent implementation details.
- **Honest readiness.** Flag any story that can't be started without a spike or external decision.
- **3–8 stories per epic.** If you need more, the epic should be split first.

---

## Step 1 — Gather context

1. Read `CLAUDE.md` if it exists.
2. Read `STRATEGY.md` if it exists.
3. Ask the user which epic to break down. Read the full epic description carefully.
4. Read the relevant codebase areas this epic touches — the components, services, models, and tests that will be affected.

---

## Step 2 — Shape the stories

### User story format

> **As a** [specific role], **I want** [concrete goal], **so that** [meaningful benefit].

Rules for the role:
- Be specific. Not "user" — use "account owner", "API consumer", "support agent", "guest visitor".
- The role should determine what permissions and UI state the story is written against.

### INVEST checklist (apply to each story)

- **Independent:** Can be worked without waiting for another story in the same epic.
- **Negotiable:** The *how* is flexible; the *what* is clear.
- **Valuable:** Delivers observable value to the user or the system.
- **Estimable:** A senior engineer can estimate it in 1–5 days.
- **Small:** Fits within a sprint.
- **Testable:** Acceptance criteria are binary — pass or fail.

If a story fails any criterion, note it explicitly under the story and reshape it.

---

## Step 3 — Output

Produce 3–8 stories using this format:

---

# Stories: [Epic Name]

**Epic:** [Epic name and one-sentence scope]
**Files reviewed:** [list key files read]

---

## Story 1: [Short name]

**As a** [role],
**I want** [goal],
**so that** [benefit].

**Acceptance Criteria**
- [ ] Given [context], when [action], then [outcome]
- [ ] Given [context], when [action], then [outcome]
- [ ] Given [invalid/error state], when [action], then [error handling outcome]

**Definition of Done**
- [ ] Code reviewed and merged to main
- [ ] Unit/integration tests written and passing
- [ ] No regression in existing tests
- [ ] [Any story-specific criteria: feature flag, docs update, etc.]

**Technical Notes**
Brief, specific notes about relevant code — cite file paths or function names you've actually read. Omit this section if you have no relevant code evidence.

**Dependencies**
- Depends on: Story N / none
- Blocking: Story N / none

**Status:** READY / NEEDS_CLARIFICATION / NEEDS_SPIKE

If NEEDS_CLARIFICATION: state the specific question that blocks this story.
If NEEDS_SPIKE: state what must be learned and approximately how long the spike should be.

---

[Repeat for each story]

---

## Deferred Backlog

Stories that were considered but not included, with rationale:
- [Story idea]: deferred because [reason — scope, dependency, uncertainty]

## Questions for the team

Any open questions that apply across multiple stories in this epic.

---

## Save artifact

After producing the complete story breakdown, save it to the project:

1. Create `docs/stories/` if it doesn't exist.
2. Derive a kebab-case slug from the epic name (e.g. "Epic 2: Payments" → `epic-2-payments`).
3. Write the full output to: `docs/stories/YYYY-MM-DD-{slug}.md` using today's actual date.
4. Tell the user the exact path where it was saved.
