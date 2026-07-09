---
name: to-issues
description: Break a plan, spec, or PRD into task slices using tracer-bullet vertical slices, saved as a local Markdown file in .plans/tasks/
disable-model-invocation: true
---

# To Issues

Break a plan into task slices using vertical slices (tracer bullets). Output is saved as a Markdown file in `.plans/tasks/`.

## Process

### 1. Gather context

Work from whatever is already in the conversation context. If the user passes a plan file path as an argument, read the file to understand what needs to be broken down.

### 2. Explore the codebase (optional)

If you have not already explored the codebase, do so to understand the current state of the code. Task titles and descriptions should use the project's domain glossary vocabulary, and respect ADRs in the area you're touching.

Look for opportunities to prefactor the code to make the implementation easier. "Make the change easy, then make the easy change."

### 3. Draft vertical slices

Break the plan into **tracer bullet** task slices. Each slice is a thin vertical slice that cuts through ALL integration layers end-to-end, NOT a horizontal slice of one layer.

<vertical-slice-rules>

- Each slice delivers a narrow but COMPLETE path through every layer (schema, API, UI, tests)
- A completed slice is demoable or verifiable on its own
- Any prefactoring should be done first

</vertical-slice-rules>

### 4. Quiz the user

Present the proposed breakdown as a numbered list. For each slice, show:

- **Title**: short descriptive name
- **Blocked by**: which other slices (if any) must complete first
- **User stories covered**: which user stories this addresses (if the source material has them)

Ask the user:

- Does the granularity feel right? (too coarse / too fine)
- Are the dependency relationships correct?
- Should any slices be merged or split further?

Iterate until the user approves the breakdown.

### 5. Write the task breakdown file

Create `.plans/tasks/` if it doesn't exist. Write the breakdown as a Markdown file named after the feature (e.g., `.plans/tasks/store-selection-ui-breakdown.md`). Use the template below.

<task-breakdown-template>
# <Feature Name> - Task Breakdown

Source PRD: <reference to source plan/PRD file>

## Overview

Brief description of what this feature delivers and how many slices it's broken into.

---

## Slice 1: <Title>

**Status**: Ready to start / Blocked
**Blocked by**: None - can start immediately / Slice N (needs X)

**User stories covered**:
- US#N: <story summary>
- US#M: <story summary>

### What to build

A concise description of this vertical slice. Describe the end-to-end behavior, not layer-by-layer implementation.

Avoid specific file paths or code snippets — they go stale fast. Exception: if a prototype produced a snippet that encodes a decision more precisely than prose can (state machine, reducer, schema, type shape), inline it here and note briefly that it came from a prototype. Trim to the decision-rich parts — not a working demo, just the important bits.

### Acceptance criteria

- [ ] Criterion 1
- [ ] Criterion 2
- [ ] Criterion 3

---

## Slice 2: <Title>

**Status**: Blocked
**Blocked by**: Slice 1 (needs X)

**User stories covered**:
- US#N: <story summary>

### What to build

...

### Acceptance criteria

- [ ] ...

<!-- Repeat for each slice -->

---

## Implementation Order

1. **Slice 1**: <brief summary>
2. **Slice 2**: <brief summary>
3. **Slice N**: <brief summary>

Each slice is independently testable and builds on the previous one. The complete feature ships when Slice N is done.

</task-breakdown-template>
