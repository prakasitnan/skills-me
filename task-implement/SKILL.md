---
name: task-implement
description: Read an ADR from docs/adr/NNN-*.md and execute the plan it contains. Use when the user runs /task-implement <number> or asks to implement a locked ADR.
---

You are executing a plan that has already been locked into an ADR by the `task-plan` skill. The ADR is the **authoritative spec** — do not re-open plan-level decisions.

Parse the ADR number from the invocation argument. Normalize it to 3-digit zero-padded form: `3` → `003`, `12` → `012`, `003` stays `003`.

Locate the ADR file by prefix match: `docs/adr/NNN-*.md` in the current working directory. If zero files match, stop and tell the user the ADR does not exist. If more than one file matches the same `NNN` prefix, stop and list the candidates — do not guess which one is meant.

Read the ADR fully. Treat its sections as binding:

- **`## Context`** — the reason for the change. Use it to inform judgment calls but do not re-litigate it.
- **`## Decisions`** — settled trade-offs. Do not revisit unless a genuine contradiction appears mid-implementation.
- **`## Plan`** — the ordered steps to execute.
- **`## Files to touch`** — the whitelist of paths you may modify.
- **`## Verification`** — the checks that must pass when you finish.
- **`## Out of scope`** — the guardrail. Anything listed here is forbidden even if it looks helpful.

Do not re-grill the user. If a _genuine_ ambiguity or contradiction blocks you mid-implementation (not a preference question, a real blocker), stop and ask _one targeted question_ about that specific point rather than re-opening the whole plan.

Execute the `## Plan` steps in order. Stay strictly within `## Files to touch`. If the task genuinely needs a file outside that list, stop and ask the user before adding it — do not silently expand the scope.

After each meaningful step, note progress in one short line so the user can follow along. Do not narrate internal reasoning.

When all steps are done:

1. Run every check in `## Verification` and report the results plainly (pass / fail with the actual output).
2. If any check fails, fix the underlying cause rather than editing the check — unless the check itself is wrong, in which case stop and ask.
3. Update the ADR: change `**Status:** Proposed` to `**Status:** Implemented`, and append an `## Outcome` section at the end containing a short summary of what was done and a bulleted list of the files actually changed.

Do **not** create git commits, push, or open PRs — leave version-control actions to the user unless they explicitly ask.
