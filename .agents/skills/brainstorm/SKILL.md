---
name: brainstorm
description: >-
  Interactive idea interview with the user before anything enters the backlog:
  question the idea, challenge assumptions, propose alternatives and improvements,
  converge on a conclusion. Triggers: "brainstorm", "tenho uma ideia", "quero criar",
  "vamos pensar em", any new tool/project idea not yet in todos.md.
---

# Brainstorm

Interactive only — never run inside the Noodle loop. The goal is the best version
of the idea, reached by debate, not by agreeing with the first framing.

The user is a student: explain trade-offs in plain Portuguese, define technical
terms the first time they appear.

## 1. Understand the problem, not the solution

Ask (a few at a time, not a questionnaire dump):
- What pain does this solve, and for whom? How is it done today?
- What does "done" look like? How will we know it worked?
- Constraints: budget, deadline, where it runs, data sensitivity.
- Is it a personal project, portfolio piece, or both? (Portfolio → favor clarity,
  README, demo-ability.)

**Before proposing anything, look for what already exists** — `brain/plans/`, `estudos/`,
and the user's connected tools (Notion, etc.) — and check whether it was *used*, not just
whether it exists. An unused earlier attempt means the problem is not a missing tool: say
so, and start from what is there instead of building a second one. (2026-10-05: a study
panel built four days earlier had zero diary entries; it was found only at plan time.)

## 2. Challenge

- Point out weak assumptions and risks directly.
- Offer 2–3 genuinely different approaches (e.g. no-code vs script vs full app),
  each with cost, complexity and what the user would learn from it.
- Suggest the smallest version that delivers value (MVP) and what to leave for later.
- Push back when the idea is bigger than needed.

## 3. Decide the stack together

Defaults to propose, not impose: Python for automation/scripts/data extraction;
Next.js + React + TypeScript for web and app screens. Justify any deviation.
Name the verification that will enforce quality (linter, type check, tests) — these
become the project's hard constraints.

## 4. Converge

Stop when the user agrees on a direction. Write a brief to
`brain/plans/<slug>/brief.md`:

```
# <Name>
- Problem:
- Users:
- MVP scope (in):
- Out of scope (later):
- Chosen approach and why:
- Rejected alternatives and why:
- Stack:
- Verification (lint/types/tests):
- Success criteria:
- Open questions:
```

Then ask the user whether to proceed to the `plan` skill and add it to `todos.md`.

## 5. Teach

If the session produced a real learning, add or update a case study in `estudos/`
following `estudos/README.md`.
