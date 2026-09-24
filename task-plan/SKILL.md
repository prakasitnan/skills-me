---
name: task-plan
description: Grill the user about a task, then write the agreed plan to docs/adr/NNN-<slug>.md and stop. Use when the user wants to plan a task and lock the plan into an ADR before implementation.
---

Interview the user relentlessly until you reach a shared understanding of the task. Map this as a **design tree**: every decision branches into the decisions that hang off it.

Work the tree in **rounds**. The **frontier** is every decision whose prerequisites are already settled: the questions you can ask _now_ without guessing at answers you haven't heard yet. Ask the whole frontier in one round: number each question and give your recommended answer. Then wait for the user's answers before the next round.

Format a round like so:

```
❓ **Q1** - **<question title>**: <question body, might be multiple paragraphs, including multiple choices>

➡️ <your recommended answer>

---

❓ **Q2** - **<question title>**: <question body, might be multiple paragraphs, including multiple choices>

➡️ <your recommended answer>
```

Each round the user answers reshapes the tree: settled decisions push the frontier outward and unblock questions that depended on them. Recompute the frontier and ask the next round. A question whose answer depends on another question still open in this round belongs to a _later_ round, not this one.

Finding _facts_ is your job, never the user's. When a frontier question needs a fact from the environment (filesystem, tools, existing code, etc.), look it up yourself; don't ask the user for anything you could find. Don't block on it: a running exploration is an unsettled prerequisite, so only the questions downstream of it wait; ask the rest of the frontier now. The _decisions_ are the user's: put each to them and wait.

The grilling phase is done when the frontier is empty AND the user has explicitly confirmed shared understanding ("ตกลง", "ok", "go", "ยืนยัน", or similar). Until you have that confirmation, keep grilling.

Once confirmed, **do not implement**. Instead, lock the plan into an ADR:

1. Ensure `docs/adr/` exists in the current working directory. Create it if missing.
2. Scan `docs/adr/*.md` for filenames matching `^(\d{3})-.*\.md$`. Take the highest `NNN` and compute `NNN+1`, zero-padded to 3 digits. Start at `001` if the folder is empty or missing.
3. Derive a kebab-case slug from the task title: lowercase ASCII, words joined by `-`, max ~40 chars, no trailing hyphen.
4. Write the ADR to `docs/adr/NNN-<slug>.md` using the template below, filled from the grilling rounds.
5. Reply with only two lines: the file path, and `Plan locked. Run /task-implement NNN when ready.` Do not touch any other file. Do not run tests, commits, or edits outside `docs/adr/`.

ADR template:

```
# NNN — <Task title>

**Status:** Proposed · **Date:** YYYY-MM-DD

## Context

<Why this task exists — problem, motivation, constraints — distilled from the grilling.>

## Decisions

- **<question title>**: <chosen answer, one line>
- **<question title>**: <chosen answer, one line>

## Plan

1. <Concrete step: file path, function, command.>
2. <Next step.>

## Files to touch

- `<relative/path/one>`
- `<relative/path/two>`

## Verification

- <Test command, manual check, or MCP call that confirms the change works end-to-end.>

## Out of scope

- <What this task explicitly does not do.>
```
