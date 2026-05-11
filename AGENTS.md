# AGENTS.md

This file provides guidance to AI coding agents when working in this repository.

## Repository Purpose

This repository contains custom agent skills for brief/spec workflow overlays:

- `brief-writer` creates compact project briefs from rough ideas.
- `to-spec` expands accepted brief/PRD context into feature-local spec packs.
- `workflow-composer` chooses a non-overlapping sequence across this repository,
  `mattpocock/skills`, and Superpowers.

This repository is an overlay. Do not vendor or copy upstream skills from
`mattpocock/skills` or `obra/superpowers` unless the user explicitly asks for a
forked or modified copy.

## Source Of Truth

- Public landing page: `README.md`
- End-user best practices: `docs/best-practices.md` and
  `docs/best-practices.ru.md`
- Skill implementations: `skills/<skill-name>/SKILL.md`
- Handoff/current repository decisions: `handoff.md`

Treat `docs/SKILLS_CHEAT_SHEET.md` and `docs/SUPERPOWERS_INTEGRATION_IDEAS.md`
as source notes, not polished public documentation.

## Skill Layout

Use this structure for custom skills:

```text
skills/
  <skill-name>/
    SKILL.md
    assets/
    agents/
```

Rules:

- Skill directories use lowercase kebab-case.
- The skill definition file is always named `SKILL.md`.
- Optional runtime metadata may live under `agents/`.
- Optional reusable templates may live under `assets/`.
- Keep large examples or reference material outside `SKILL.md` and link to it
  only when it is needed.

## Editing Guidelines

- Keep README short and user-facing.
- Put usage guidance and workflow advice in `docs/best-practices.md` and
  `docs/best-practices.ru.md`.
- Keep English and Russian user docs in sync when changing user-facing guidance.
- Do not duplicate the same long workflow explanation in README and docs.
- Prefer concise skills with clear trigger descriptions and progressive
  disclosure.
- Preserve the repository position as an additive overlay, not a replacement for
  upstream skill systems.

## Naming Conventions

- Markdown docs in `docs/` should use lowercase kebab-case, for example
  `best-practices.md`.
- Russian companion docs should use the `.ru.md` suffix.
- Skill names should stay short, lowercase, and kebab-case.

## Before Publishing

- Remove `.DS_Store` files and other local artifacts.
- Check that install examples use the final GitHub owner/repository name.
- Verify README links to all public docs.
- Verify each `SKILL.md` has valid frontmatter with `name` and `description`.
