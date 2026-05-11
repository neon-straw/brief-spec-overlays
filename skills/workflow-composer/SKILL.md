---
name: workflow-composer
description: Compose a single non-overlapping workflow across custom planning skills, mattpocock/skills, and Superpowers. Use when a user asks which skills to run, wants to combine brief-writer and to-spec with matt-pocock-skills or superpowers, is moving from a vague idea toward implementation, or risks creating duplicate briefs, PRDs, specs, plans, or issues.
---

# Workflow Composer

Use this skill to choose the next skill sequence, not to create another planning document.
The goal is one clear path from idea to code, with one source of truth at each stage.

## First Move

1. Read the user's request and any named skill invocation.
2. Inspect repo guidance if available: `AGENTS.md` or `CLAUDE.md`, `CONTEXT.md` or `CONTEXT-MAP.md`, relevant ADRs, configured issue-tracker instructions, and existing planning docs.
3. Identify the current stage:
   - vague idea
   - scoped brief
   - PRD-ready feature
   - complex spec-needed feature
   - issue-ready work
   - implementation/debugging/review
4. Recommend or run the smallest next skill sequence. Do not run two skills that produce competing artifacts for the same stage.

## Skill Roles

Use the custom skills for early product shape:

- `brief-writer`: turn vague intent into a compact brief and scope boundary.
- `to-spec`: turn resolved PRD/brief context into a feature-local spec pack when the feature is too complex for PRD-only execution.
- `workflow-composer`: choose the sequence and prevent duplicate planning layers.

Use `mattpocock/skills` for engineering workflow:

- `setup-matt-pocock-skills`: configure issue tracker, triage labels, and domain-doc layout.
- `grill-with-docs`: stress-test a plan against domain language and update `CONTEXT.md` or ADRs.
- `grill-me`: stress-test a plan without updating docs.
- `prototype`: validate risky state, API, or UI assumptions with throwaway code.
- `to-prd`: create a PRD from already-discussed context.
- `to-issues`: split a PRD/spec/plan into vertical implementation issues.
- `tdd`: implement one issue with red-green-refactor.
- `diagnose`: debug a bug or regression through a disciplined reproduction loop.
- `triage`: classify incoming issues and make them ready for agents.
- `zoom-out` / `improve-codebase-architecture`: understand or improve architecture after real pressure appears.

Use Superpowers when the user explicitly wants that methodology, when the repo already tracks Superpowers artifacts, or when its execution/review discipline is the clearest fit:

- `brainstorming`: explore unclear creative/product direction.
- `writing-plans`: produce an implementation plan from accepted requirements or specs.
- `executing-plans`: execute an approved plan with checkpoints.
- `test-driven-development`: use Superpowers' TDD loop if that is the chosen local standard.
- `verification-before-completion`: verify before claiming completion.
- `requesting-code-review` / `receiving-code-review`: review and respond to review feedback.

## Routing

For a vague idea:

```text
brief-writer -> optional grill-with-docs -> to-prd -> optional to-spec -> to-issues -> tdd
```

For a vague idea where the user explicitly asks for Superpowers:

```text
brief-writer -> superpowers:brainstorming -> superpowers:writing-plans -> superpowers:executing-plans
```

For a feature already discussed in the chat:

```text
to-prd -> optional to-spec -> to-issues -> tdd
```

For a complex feature with multiple modules, external APIs, state machines, or contracts:

```text
to-prd -> to-spec -> to-issues
```

Tell `to-issues` to read `PRD.md`, `spec/`, and `plan/` if `to-spec` created them.

For a concrete implementation issue:

```text
tdd
```

For a bug, failing test, performance regression, or strange behavior:

```text
diagnose
```

For repo confusion or architecture orientation:

```text
zoom-out -> optional improve-codebase-architecture
```

For a small concrete edit with clear requirements:

```text
no planning skill -> implement -> verification-before-completion if available
```

## Source of Truth Ladder

Keep one canonical artifact per layer. Resolve tracker-specific paths from repo guidance first:
`AGENTS.md` or `CLAUDE.md`, the issue-tracker configuration provided by `setup-matt-pocock-skills`, GitHub/GitLab issues, and the active branch may override the local examples below.

| Layer | Canonical artifact |
| --- | --- |
| Scope and why | `docs/brief.md` or the feature brief |
| Domain language | `CONTEXT.md` and `docs/adr/` |
| Product requirements | the configured PRD location, for example `.scratch/<feature>/PRD.md` or a linked GitHub issue/PRD |
| Complex design/contracts | the configured spec location, for example `.scratch/<feature>/spec/` or docs linked from the tracking issue |
| Execution phases | the accepted plan: local `plan/`, Superpowers plan, GitHub issue checklist, or PR plan |
| Work items | the configured tracker: GitHub issues, local `.scratch/<feature>/issues/`, or another repo-defined tracker |
| Active implementation state | the active branch, PR branch, worktree, code, tests, and verified behavior |

If two artifacts claim the same role, stop and choose the newer or explicitly accepted one.
If implementation contradicts a spec or ADR, update the durable artifact or record the drift.

## Anti-Duplication Rules

- Do not create both a Superpowers plan and a separate phase plan unless one explicitly supersedes the other.
- Do not run `to-prd` if a current accepted PRD already exists; update it only if the user asks.
- Do not run `to-spec` for simple CRUD, copy edits, small bug fixes, or one-file changes.
- Do not use `brainstorming` after scope is accepted unless new uncertainty appears.
- Do not let `to-issues` split from PRD alone when `spec/` and `plan/` exist.
- Do not turn every decision into an ADR. Promote only durable, surprising, or hard-to-reverse decisions.

## Response Shape

When advising the user, answer with:

1. Current stage.
2. Next skill sequence.
3. Source-of-truth artifact to read or create.
4. The first concrete command/invocation or action.

Keep it short. This skill exists to remove workflow fog, not to add another ceremony layer.
