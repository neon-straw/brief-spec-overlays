# Brief Spec Overlays Best Practices

Brief Spec Overlays is a small set of agent skills for moving from a vague idea
to implementation-ready work without creating duplicate planning artifacts.

The repository contains three custom skills:

- `brief-writer` captures a rough idea as a compact project brief.
- `to-spec` expands accepted requirements into a feature-local spec pack when a
  PRD is not detailed enough.
- `workflow-composer` chooses the next skill sequence across this repository,
  `mattpocock/skills`, and Superpowers.

These skills are an overlay. They do not replace upstream skill systems and they
do not vendor them.

## Install

Install the upstream skills directly from their original repositories if you want
the full workflow:

```bash
npx skills add mattpocock/skills
npx skills add obra/superpowers
```

Install this repository:

```bash
npx skills add neon-straw/brief-spec-overlays
```

Install only one skill:

```bash
npx skills add neon-straw/brief-spec-overlays --skill brief-writer
npx skills add neon-straw/brief-spec-overlays --skill to-spec
npx skills add neon-straw/brief-spec-overlays --skill workflow-composer
```

## When To Use Each Skill

Use `brief-writer` when the idea is still rough. It interviews you one question
at a time and writes a short Markdown brief, usually `docs/brief.md`. The brief
defines the problem, goals, non-goals, constraints, assumptions, acceptance
criteria, open questions, and glossary.

Use `to-spec` when a feature is too complex to implement from a brief or PRD
alone. Good triggers include multiple modules, new data flow, external APIs,
state machines, security or privacy boundaries, API contracts, or multi-phase
delivery. It creates a feature-local `spec/` and `plan/` package.

Use `workflow-composer` when you are unsure which skill should run next, or when
the repo already has multiple planning systems available. It chooses one
non-overlapping path so the agent does not create competing briefs, PRDs, specs,
plans, or issues.

## Recommended Flow

For a new vague idea:

```text
brief-writer -> optional grill-with-docs -> to-prd -> optional to-spec -> to-issues -> tdd
```

For an already discussed feature:

```text
to-prd -> optional to-spec -> to-issues -> tdd
```

For a complex feature:

```text
to-prd -> to-spec -> to-issues
```

For workflow confusion:

```text
workflow-composer
```

For a small concrete edit with clear requirements, skip planning skills and ask
the agent to implement and verify the change.

## Source Of Truth

Keep one canonical artifact per layer:

| Layer                | Typical artifact                                              |
| -------------------- | ------------------------------------------------------------- |
| Scope and why        | `docs/brief.md` or a feature brief                            |
| Domain language      | `CONTEXT.md`, `CONTEXT-MAP.md`, and `docs/adr/`               |
| Product requirements | `.scratch/<feature>/PRD.md` or a linked tracker issue         |
| Complex design       | `.scratch/<feature>/spec/`                                    |
| Phase order          | `.scratch/<feature>/plan/` or an accepted implementation plan |
| Work items           | `.scratch/<feature>/issues/` or GitHub/GitLab issues          |
| Implementation truth | branch, worktree, code, tests, PR, and verified behavior      |

If your repository has `AGENTS.md`, `CLAUDE.md`, tracker instructions, ADRs, or
branch-specific notes, those local rules take priority over the examples above.

## Local Markdown Mode

Local mode works well for solo MVPs, prototypes, and small repositories.

Typical layout:

```text
.scratch/<feature-slug>/
  PRD.md
  spec/
  plan/
  issues/
    01-some-slice.md
```

Use `brief-writer` for project scope, `to-prd` for feature requirements,
`to-spec` for complex design, and `to-issues` for small vertical slices. Then
execute one issue at a time through TDD and verification.

## GitHub Issues Mode

GitHub mode works better for longer backlogs, PR review, collaboration, and
multi-agent work.

Typical flow:

1. Create the PRD as an issue or linked document.
2. Split the PRD and optional spec pack into child issues.
3. Use labels such as `needs-triage`, `needs-info`, `ready-for-agent`,
   `ready-for-human`, and `wontfix`.
4. Implement one issue per branch or worktree.
5. Link the PR or commit with `Fixes #NN`.

## Working With Superpowers

Use this overlay for product and domain shaping. Use Superpowers for execution
discipline: worktrees, implementation plans, TDD, review, verification, and
branch finishing.

Recommended split:

```text
brief-writer / to-prd / to-spec / to-issues create the work artifacts.
Superpowers executes exactly one accepted issue or plan at a time.
```

Avoid creating both a Superpowers plan and a separate phase plan for the same
feature unless one explicitly supersedes the other.

## Prompt Templates

Start a new project brief:

```text
Use the brief-writer skill. Interview me one question at a time and write the
brief to docs/brief.md.
```

Create a complex spec pack:

```text
Use the to-spec skill for this feature. Read the accepted brief/PRD, repo
guidance, domain docs, ADRs, and current code. Create architecture, data-flow,
behavior, decisions, testing, API contract, and phase plan docs.
```

Choose the next workflow step:

```text
Use workflow-composer. Tell me the current stage, the next skill sequence, the
source-of-truth artifact, and the first concrete action.
```

Split a designed feature into issues:

```text
Split the PRD, spec pack, and phase plan into small vertical slice issues. Each
issue must be independently testable and demoable.
```

Execute one issue:

```text
Implement only issue #NN. Use TDD. Read the brief, PRD, spec pack, ADRs, and
repo guidance first. Verify acceptance criteria before calling it complete.
```

## Quality Gates

Before implementation starts:

- The brief or PRD has clear goals and non-goals.
- Critical open questions are either answered or explicitly accepted as risk.
- The source of truth is clear.
- Complex features have a spec pack or an accepted reason to skip it.

Before a task is complete:

- Acceptance criteria are met.
- Tests or manual checks were run.
- Prototype and debug code were removed.
- The implementation still matches the accepted brief, PRD, spec, or issue.
- Any important drift is written back to the durable docs.
