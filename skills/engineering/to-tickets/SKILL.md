---
name: to-tickets
description: Break a plan, spec, or PRD into tracer-bullet task slices, saved as a local Markdown file in .plans/tasks/
disable-model-invocation: true
---

# To Tickets

Break a plan, spec, or conversation into a set of **tickets** — tracer-bullet vertical slices that build on each other. Output is saved as a Markdown file in `.plans/tasks/`.

## Process

### 1. Gather context

Work from whatever is already in the conversation context. If the user passes a reference (a spec path, plan file, or issue number/URL) as an argument, read it to understand what needs to be broken down.

### 2. Explore the codebase (optional)

If you have not already explored the codebase, do so to understand the current state of the code. Ticket titles and descriptions should use the project's domain glossary vocabulary, and respect ADRs in the area you're touching.

Look for opportunities to prefactor the code to make the implementation easier. "Make the change easy, then make the easy change."

### 3. Draft vertical slices

Break the work into **tracer bullet** tickets.

<vertical-slice-rules>

- Each slice cuts a narrow but COMPLETE path through every layer (schema, API, UI, tests) — vertical, NOT a horizontal slice of one layer
- A completed slice is demoable or verifiable on its own
- Each slice is sized to fit in a single fresh context window
- Any prefactoring should be done first

</vertical-slice-rules>

Give each ticket its **blocking edges** — the other tickets that must complete before it can start. A ticket with no blockers can start immediately.

**Wide refactors are the exception to vertical slicing.** A **wide refactor** is one mechanical change — rename a column, retype a shared symbol — whose **blast radius** fans across the whole codebase, so a single edit breaks thousands of call sites at once and no vertical slice can land green. Don't force it into a tracer bullet; sequence it as **expand–contract**. First expand: add the new form beside the old so nothing breaks. Then migrate the call sites over in batches sized by blast radius (per package, per directory), each batch its own ticket blocked by the expand, keeping CI green batch to batch because the old form still exists. Finally contract: delete the old form once no caller remains, in a ticket blocked by every migrate batch. When even the batches can't stay green alone, keep the sequence but let them share an integration branch that all block a final integrate-and-verify ticket — green is promised only there.

### 4. Quiz the user

Present the proposed breakdown as a numbered list. For each ticket, show:

- **Title**: short descriptive name
- **Blocked by**: which other tickets (if any) must complete first
- **What it delivers**: the end-to-end behaviour this ticket makes work
- **User stories covered**: which user stories this addresses (if the source material has them)

Ask the user:

- Does the granularity feel right? (too coarse / too fine)
- Are the blocking edges correct — does each ticket only depend on tickets that genuinely gate it?
- Should any tickets be merged or split further?

Iterate until the user approves the breakdown.

### 5. Write the task breakdown file

Create `.plans/tasks/` if it doesn't exist. Write the breakdown as a Markdown file named after the feature (e.g., `.plans/tasks/store-selection-ui-breakdown.md`). Use the template below.

<task-breakdown-template>
# <Feature Name> - Task Breakdown

Source: <reference to source plan/PRD file or issue>

## Overview

Brief description of what this feature delivers and how many tickets it's broken into.

Work the **frontier**: any ticket whose blockers are all done. For a purely linear chain that means top to bottom.

---

## Ticket 1: <Title>

**Blocked by**: None - can start immediately

**User stories covered**:
- US#N: <story summary> (if applicable)

### What to build

The end-to-end behaviour this ticket makes work, from the user's perspective — not a layer-by-layer implementation list.

Avoid specific file paths or code snippets — they go stale fast. Exception: if a prototype produced a snippet that encodes a decision more precisely than prose can (state machine, reducer, schema, type shape), inline it here and note briefly that it came from a prototype. Trim to the decision-rich parts — not a working demo, just the important bits.

### Acceptance criteria

- [ ] Criterion 1
- [ ] Criterion 2
- [ ] Criterion 3

---

## Ticket 2: <Title>

**Blocked by**: Ticket 1 (needs X)

**User stories covered**:
- US#N: <story summary>

### What to build

...

### Acceptance criteria

- [ ] ...

<!-- Repeat for each ticket -->

</task-breakdown-template>

Work the frontier one ticket at a time with `/implement`, clearing context between tickets.
