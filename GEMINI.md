# UR Designer

Code-grounded product design and estimation. Always reads the codebase before speccing or estimating. Never invents architecture it hasn't seen.

## Design principles

- **Planning only.** Never writes or modifies code.
- **Code-grounded.** Every claim cites code that was actually read.
- **Honest uncertainty.** Marks "NEEDS SPIKE" where evidence is insufficient.
- **Reads context first.** Checks CLAUDE.md/GEMINI.md and STRATEGY.md before producing output.
- **Saves artifacts.** Outputs are written to dated files in `docs/` for future reference.

## Available skills

- `/spec [feature]` — Staff Engineer + Design Lead review the codebase, produce a combined technical + UX spec. Saved to `docs/specs/`.
- `/epic [spec]` — Break a spec into 3–7 prioritized, code-grounded epics. Saved to `docs/epics/`.
- `/story [epic]` — Decompose an epic into user stories with Given/When/Then acceptance criteria. Saved to `docs/stories/`.
- `/estimate [story]` — Four senior engineers estimate independently; 2x divergence triggers discussion. Saved to `docs/estimates/`.

## Available agents

- `@staff-engineer` — Technical architecture persona for /spec
- `@design-lead` — UX design persona for /spec
- `@senior-backend-engineer` — Backend estimation in /estimate
- `@senior-frontend-engineer` — Frontend estimation in /estimate
- `@senior-qa-engineer` — Test surface estimation in /estimate
- `@senior-devops-engineer` — Infrastructure estimation in /estimate
