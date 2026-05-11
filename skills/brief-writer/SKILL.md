---
name: brief-writer
description: Creates a one-page Markdown project brief through a guided single-question interview. The brief follows an RFC 2119 structure (Problem, Goals, Non-goals, Constraints, Assumptions, Acceptance criteria, Open questions, Glossary) and is intended as the AI agent's source of truth for "why" and scope. Invoked manually.
---

# Brief Writer

Create a one-page Markdown brief that becomes the AI agent's source of truth for "why" and scope. The brief is short on purpose: anything longer than one screen escapes into separate specs.

## Why this skill exists

AI agents fail when scope is fuzzy: they over-build, drift toward optimizing the wrong metric, or hallucinate domain facts. A tight brief with explicit non-goals and RFC 2119 constraints prevents most of that — it gives the agent a fence and a compass on the same page.

## Workflow

1. Before asking anything, scan the user's initial message and prefill what is already there. Treat the interview as filling gaps, not as an empty-form questionnaire.
2. Show a visible `Brief TODO` checklist (use the TodoWrite tool when available — it renders nicely; otherwise fall back to a markdown checklist in chat).
3. Ask exactly one question per assistant message. Never ask a numbered list of questions — the user answers the easiest one and the rest get lost.
4. Wait for the answer before asking the next question. After each answer update the TODO compactly, then ask the next single question.
5. Prefer concrete, checkable answers over polished language. If the user is unsure, capture the uncertainty in `Open questions` and move on — do not stall the interview.
6. If the user says "хватит вопросов, пиши" (or any equivalent: "just write it", "достаточно", "skip the rest"), stop interviewing and write the brief with what you have. Move every still-empty TODO item into `Open questions`.
7. After enough context is collected, write the file. If the user did not specify a path, use `docs/brief.md` in the current working directory (create the `docs/` folder if it does not exist).
8. Use `assets/brief-template.md` as the structural source of truth. Preserve every top-level section and the template's ordering.

## Brief TODO

```markdown
Brief TODO:
- [ ] Target file path
- [ ] Problem / TL;DR
- [ ] Goals (3–5)
- [ ] Non-goals (2–4)
- [ ] Constraints
- [ ] Assumptions
- [ ] Acceptance criteria
- [ ] Open questions
- [ ] Glossary
```

The project name is not a separate question — derive it from the TL;DR, the user's first message, or the working-directory name and use it in the brief title.

Mark a TODO item complete only when there is enough information to write that section. If the user declines or cannot answer, leave it open and record the uncertainty in `Open questions`.

## Interview Map

Ask in this order. Skip questions that are obviously irrelevant for the project type — for a CLI utility there is no "PII privacy" question; for a one-off internal script there is no "cost/model" question. Use judgment, do not robotically march through the list.

**Core (always ask):**

- Target file path. If the user has no preference, use `docs/brief.md`.
- One-line TL;DR: what this is and for whom.
- Problem only: who is blocked or suffering, and from what. Do not ask for solutions here.
- Demonstrable goals. Ask follow-ups until there are 3–5 goals.
- Explicit non-goals. Ask follow-ups until there are 2–4 forbidden positions, features, scenarios, metrics, or complexity traps.
- Binary acceptance criteria.

**Conditional (ask only if relevant):**

- Stack and forbidden toolchains.
- Source of truth: APIs, databases, files, repositories, or human inputs the agent may rely on.
- Privacy and secrets rules — only if the project handles user data or credentials.
- Editing boundaries: what the agent may change, what is out of scope.
- Cost/model expectations — only if the project makes many LLM calls.
- Manual override rule for conflicts.
- Assumptions with cheap first-day checks.
- Open questions that must be closed before specs or implementation.
- Domain glossary terms whose ambiguity could cause hallucinations.

## Writing Rules

- Keep the brief compact. If the content does not fit on one screen, the brief stays as the summary and details move into separate specs. The point of a brief is to be re-readable in 30 seconds.
- Use modal verbs only in `Non-goals` and `Constraints`. Other sections are descriptive — modal verbs there dilute the ones that actually carry weight.
- Capitalize RFC 2119 modal verbs exactly: `MUST`, `MUST NOT`, `SHOULD`, `MAY`, `NEVER`. Use `NEVER` for hard non-goals; use `MUST`, `MUST NOT`, `SHOULD`, `MAY` for enforceable constraints.
- Do not invent links, IDs, metrics, APIs, citations, targets, or domain facts. Missing facts go into `Open questions`. A bullshit number is worse than an explicit gap — the agent will treat the number as truth and optimize for it.
- Make goals demonstrable or measurable. Make acceptance criteria binary checkboxes. If you cannot tell whether a goal is met by looking at the artifact or a number, the goal is not yet a goal.
- Make assumptions cheap to verify in minutes, not days. Expensive verification turns into sabotage: the user will not do it, the brief will hang, and the project drifts.
- Keep glossary definitions one line each. The glossary catches terms that have multiple meanings in the user's domain and one wrong one in Claude's training — it prevents whole categories of hallucination.

## Examples: vague vs. demonstrable

**Goals**

- ❌ "Make a convenient summarization tool" — not demonstrable.
- ✅ "Cut the time to read a PubMed abstract from 2 minutes to 20 seconds for a pilot group of 5 bioinformaticians."

**Non-goals**

- ❌ "Don't make it too complicated" — not enforceable.
- ✅ "**NEVER** add user authentication. **NEVER** optimize for SEO or virality. **NEVER** position as a clinical decision tool."

**Acceptance criteria**

- ❌ "Works well on real articles" — subjective.
- ✅ "[ ] Given any PubMed PMID, the app returns a summary in under 10 seconds. [ ] Summary contains no claim that is not present in the abstract (verified by spot-check on 20 articles)."

**Assumption with a cheap check**

- ❌ "Users will adopt the tool" → check via 6-month engagement study.
- ✅ "Bioinformaticians on the team currently spend ≥30 min/day reading abstracts" → check by asking 3 of them in Slack today.

## File Creation

When writing the final file:

1. Open `assets/brief-template.md` and use it as the structural skeleton.
2. Fill placeholders from user answers. Replace every `<...>` placeholder — including the `<project>` in the title — or move the unknown into `Open questions`.
3. Before saving, scan the file for any remaining `<...>` placeholders. None should survive into the final file: each one is either filled or surfaced as an open question.
4. Write the Markdown file to the agreed path.
5. Report the file path. If `Open questions` is non-empty, tell the user the brief is not yet ready to start work, and call out the 2–3 questions that block the most. Briefs with open critical questions get used anyway and cause the exact ambiguity-driven failures this skill is designed to prevent.

## Notes

- `agents/openai.yaml` is metadata for an OpenAI-compatible runtime; it is not read by Claude. Leave it alone unless the user asks.
