---
name: to-spec
description: Turn resolved PRD context into a feature-local spec pack (architecture, data-flow, behavior, decisions, testing, API contracts, phase plans). Use when a feature is too complex for a single PRD before to-issues, spans multiple modules, introduces new data-flow or external APIs, or needs C4/DFD/sequence-level design before implementation.
---

# to-spec

Use this skill between `grill-with-docs`/`to-prd` and `to-issues` when a feature needs a real design package before implementation. This skill introduces `spec/` and `plan/` only for complex features; the normal lightweight workflow can stay as `PRD.md -> issues/`.

## Output Shape

For complex features, create feature-local docs under `.scratch/<feature-slug>/`:

```text
spec/
  README.md
  01-architecture.md
  02-data-flow.md
  03-behavior.md
  04-decisions.md
  05-testing.md
  06-api-contract.md
plan/
  phase-01.md
  phase-02.md
```

## Workflow

1. Read repo guidance from `AGENTS.md` or `CLAUDE.md`, the project's domain glossary (`CONTEXT.md` or `CONTEXT-MAP.md`), relevant ADRs, the current PRD/brief, and the existing code shape.
2. Ask only blocking questions. If a choice is reversible, propose a default and mark it as an assumption.
3. Generate the spec pack:
   - `README.md` links the docs and states the feature goal.
   - `01-architecture.md` captures C4-lite context/container/component views, module ownership, and seams.
   - `02-data-flow.md` captures DFD, sequence diagrams, external systems, inputs, outputs, and trust boundaries.
   - `03-behavior.md` captures user scenarios, states, edge cases, errors, no-results behavior, and fallback behavior.
   - `04-decisions.md` captures trade-offs, rejected alternatives, assumptions, and ADR candidates.
   - `05-testing.md` captures integration behaviors, public interfaces, seams, fixtures, and manual checks.
   - `06-api-contract.md` captures inputs, outputs, errors, invariants, rate limits, and idempotency rules.
4. Generate phase docs under `plan/` with small, ordered phases that can later become vertical slice issues.
5. Mark which decisions should be promoted to ADR and which should stay feature-local.
6. Hand off to `to-issues`: if this skill created `.scratch/<feature-slug>/spec/` and `.scratch/<feature-slug>/plan/`, tell it to split from `PRD.md`, `spec/`, and `plan/`, not from PRD alone.

## Rules

- Keep PRD focused on why/what. Put how/contracts in `spec/`.
- Do not duplicate durable domain language. Add or refine domain terms through `grill-with-docs` and `CONTEXT.md`.
- Do not create ADRs for every decision. Promote only hard-to-reverse, surprising trade-offs.
- Prefer diagrams when they clarify architecture or data-flow. Mermaid is fine.
- Keep each phase independently verifiable.
- If implementation later changes architecture, data-flow, or API contract, update the spec pack or ADR to avoid spec drift.
