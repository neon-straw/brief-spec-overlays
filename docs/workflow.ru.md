# Лучшие практики Brief Spec Overlays

Brief Spec Overlays - это небольшой набор agent skills, который помогает пройти
путь от сырой идеи до задач, готовых к реализации, и не создать несколько
конфликтующих планов одновременно.

В репозитории три собственных skill:

- `brief-writer` превращает сырую идею в компактный проектный brief.
- `to-spec` разворачивает принятые требования в feature-local spec pack, если
  одного PRD уже недостаточно.
- `workflow-composer` выбирает следующий шаг между этим репозиторием,
  `mattpocock/skills` и Superpowers.

Эти skills являются overlay. Они не заменяют upstream skill-системы и не
вендорят их.

## Установка

Если нужен полный workflow, установите upstream skills напрямую из оригинальных
репозиториев:

```bash
npx skills add mattpocock/skills --agent codex
npx skills add obra/superpowers --agent codex
```

Установите этот репозиторий:

```bash
npx skills add <github-user>/brief-spec-overlays --agent codex
```

Установите только один skill:

```bash
npx skills add <github-user>/brief-spec-overlays --skill brief-writer --agent codex
npx skills add <github-user>/brief-spec-overlays --skill to-spec --agent codex
npx skills add <github-user>/brief-spec-overlays --skill workflow-composer --agent codex
```

Замените `<github-user>` на GitHub-владельца опубликованного репозитория.

## Когда использовать каждый skill

Используйте `brief-writer`, когда идея еще сырая. Skill проводит интервью по
одному вопросу за раз и пишет короткий Markdown brief, обычно в `docs/brief.md`.
Brief фиксирует problem, goals, non-goals, constraints, assumptions, acceptance
criteria, open questions и glossary.

Используйте `to-spec`, когда фича слишком сложная для реализации только по brief
или PRD. Хорошие признаки: несколько модулей, новый data flow, внешний API,
state machine, security/privacy boundary, API contract или несколько фаз
поставки. Skill создает feature-local `spec/` и `plan/`.

Используйте `workflow-composer`, когда непонятно, какой skill запускать дальше,
или когда в репозитории уже подключено несколько planning-систем. Он выбирает
один непротиворечивый путь, чтобы агент не создал конкурирующие briefs, PRD,
specs, plans или issues.

## Рекомендуемый flow

Для новой сырой идеи:

```text
brief-writer -> optional grill-with-docs -> to-prd -> optional to-spec -> to-issues -> tdd
```

Для фичи, которую уже обсудили:

```text
to-prd -> optional to-spec -> to-issues -> tdd
```

Для сложной фичи:

```text
to-prd -> to-spec -> to-issues
```

Если непонятно, какой workflow выбрать:

```text
workflow-composer
```

Для маленькой конкретной правки с понятными требованиями planning skills не
нужны: попросите агента реализовать изменение и проверить результат.

## Source Of Truth

Держите один канонический artifact на каждый слой:

| Слой | Типичный artifact |
| --- | --- |
| Scope и why | `docs/brief.md` или feature brief |
| Доменный язык | `CONTEXT.md`, `CONTEXT-MAP.md`, `docs/adr/` |
| Product requirements | `.scratch/<feature>/PRD.md` или linked tracker issue |
| Complex design | `.scratch/<feature>/spec/` |
| Порядок фаз | `.scratch/<feature>/plan/` или принятый implementation plan |
| Work items | `.scratch/<feature>/issues/` или GitHub/GitLab issues |
| Implementation truth | branch, worktree, code, tests, PR и verified behavior |

Если в репозитории есть `AGENTS.md`, `CLAUDE.md`, tracker instructions, ADR или
branch-specific notes, локальные правила имеют приоритет над примерами выше.

## Local Markdown Mode

Локальный режим подходит для solo MVP, прототипов и небольших репозиториев.

Типичная структура:

```text
.scratch/<feature-slug>/
  PRD.md
  spec/
  plan/
  issues/
    01-some-slice.md
```

Используйте `brief-writer` для границ проекта, `to-prd` для требований фичи,
`to-spec` для сложного дизайна и `to-issues` для маленьких vertical slices.
Затем выполняйте по одному issue через TDD и verification.

## GitHub Issues Mode

GitHub-режим лучше подходит для длинного backlog, PR review, совместной работы и
multi-agent разработки.

Типичный flow:

1. Создайте PRD как issue или linked document.
2. Разбейте PRD и optional spec pack на дочерние issues.
3. Используйте labels вроде `needs-triage`, `needs-info`, `ready-for-agent`,
   `ready-for-human`, `wontfix`.
4. Реализуйте один issue на одну branch или worktree.
5. Свяжите PR или commit через `Fixes #NN`.

## Работа с Superpowers

Используйте этот overlay для product/domain shaping. Используйте Superpowers для
execution discipline: worktrees, implementation plans, TDD, review, verification
и finishing branch workflow.

Рекомендуемое разделение:

```text
brief-writer / to-prd / to-spec / to-issues создают work artifacts.
Superpowers выполняет ровно один принятый issue или plan за раз.
```

Не создавайте одновременно Superpowers plan и отдельный phase plan для одной и
той же фичи, если один явно не заменяет другой.

## Шаблоны промптов

Начать новый project brief:

```text
Use the brief-writer skill. Interview me one question at a time and write the
brief to docs/brief.md.
```

Создать complex spec pack:

```text
Use the to-spec skill for this feature. Read the accepted brief/PRD, repo
guidance, domain docs, ADRs, and current code. Create architecture, data-flow,
behavior, decisions, testing, API contract, and phase plan docs.
```

Выбрать следующий workflow step:

```text
Use workflow-composer. Tell me the current stage, the next skill sequence, the
source-of-truth artifact, and the first concrete action.
```

Разбить спроектированную фичу на issues:

```text
Split the PRD, spec pack, and phase plan into small vertical slice issues. Each
issue must be independently testable and demoable.
```

Выполнить один issue:

```text
Implement only issue #NN. Use TDD. Read the brief, PRD, spec pack, ADRs, and
repo guidance first. Verify acceptance criteria before calling it complete.
```

## Quality Gates

Перед началом реализации:

- Brief или PRD содержит понятные goals и non-goals.
- Критические open questions закрыты или явно приняты как риск.
- Понятно, какой artifact является source of truth.
- Для сложных фич есть spec pack или принятое решение его не делать.

Перед завершением задачи:

- Acceptance criteria выполнены.
- Tests или manual checks запущены.
- Prototype/debug code удален.
- Реализация все еще соответствует принятому brief, PRD, spec или issue.
- Важный drift записан обратно в durable docs.
