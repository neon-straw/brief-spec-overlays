# Brief Spec Overlays

A small set of custom agent skills for turning rough ideas into concise briefs,
expanding resolved requirements into feature specs, and choosing a clean skill
sequence across multiple agent-skill systems.

This repository is an overlay, not a replacement for upstream skill projects. It
is designed to be used alongside:

- [`mattpocock/skills`](https://github.com/mattpocock/skills)
- [`obra/superpowers`](https://github.com/obra/superpowers)

It does not vendor or redistribute those projects. Install upstream skills from
their original repositories.

## When to Use

Use these skills when you want a lightweight bridge between early product
shaping and downstream engineering workflows:

- capture a vague idea as a short, reusable brief;
- expand a resolved brief or PRD into implementation-oriented spec docs;
- choose which skill system should own the next step without creating duplicate
  briefs, PRDs, specs, plans, or issues.

For detailed usage guidance, read [Workflow](docs/workflow.md).

## Skills

- `brief-writer` - turns rough notes, meeting material, or early product ideas
  into a compact structured brief.
- `to-spec` - turns a resolved brief or PRD context into a feature-local spec
  pack when the work needs more design detail before implementation.
- `workflow-composer` - chooses a non-overlapping sequence between
  `brief-writer`, `to-spec`, `mattpocock/skills`, and `obra/superpowers` so the
  agent does not create competing briefs, PRDs, specs, plans, or issues.

## Repository Layout

```text
skills/
  brief-writer/
    SKILL.md
    assets/brief-template.md
    agents/openai.yaml
  to-spec/
    SKILL.md
  workflow-composer/
    SKILL.md
```

Additional maintainer notes and source material live in `docs/`.

## Installation

Install upstream skills separately:

```bash
npx skills add mattpocock/skills
npx skills add obra/superpowers
```

Install this repository:

```bash
npx skills add neon-straw/brief-spec-overlays
```

Install one skill:

```bash
npx skills add neon-straw/brief-spec-overlays --skill brief-writer
npx skills add neon-straw/brief-spec-overlays --skill to-spec
npx skills add neon-straw/brief-spec-overlays --skill workflow-composer
```

## License and Attribution

These custom skills are maintained independently. Upstream projects are not
included in this repository and remain under their own licenses.
