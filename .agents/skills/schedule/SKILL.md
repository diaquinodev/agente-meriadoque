---
name: schedule
description: >-
  Reads project state and writes the next set of orders. Use when the
  scheduling stage runs to decide what work gets dispatched next.
schedule: "When orders are empty, after backlog changes, or when session history suggests re-evaluation."
---

# Schedule

## Read state

Read `.noodle/mise.json` for the current backlog, active agents, recent
history, and registered `task_types`. Check recent events since the last
scheduling run before deciding anything — a failing item shouldn't be
retried blindly.

## Decide what to schedule

For each open backlog item:

- **Straightforward item** → a single `execute` stage (add `quality` as a
  follow-up if the config has a quality skill registered).
- **Complex or ambiguous item** → schedule planning first (`extra_prompt`
  pointing at `/plan` or similar) before implementation.
- **Nothing actionable** → write `{"orders": []}`. Do this explicitly
  rather than leaving the file stale, so the loop doesn't spin.

Prefer advancing one item/plan to completion over spreading partial
progress across many. Only run independent items in parallel (same
`group`) when they don't touch overlapping files or state.

## Route providers

Use `routing.defaults` from `.noodle.toml` unless a task type or backlog
item specifies otherwise. Set `runtime` explicitly on every stage.

## Write orders

Write only to `.noodle/orders-next.json` — never touch `orders.json`
directly, the loop promotes it atomically. Run `noodle schema orders` if
you need to double check the schema. Every order needs a `rationale`
citing why it was scheduled (or why something was deliberately left out).

Deschedule or split a backlog item that has failed the same stage twice in
a row rather than resubmitting it unchanged.
