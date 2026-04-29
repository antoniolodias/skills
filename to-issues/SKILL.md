---
name: to-issues
description: Break a plan, spec, or PRD into independently-grabbable task slices using tracer-bullet vertical slices, saved as a local markdown file. Use when user wants to convert a plan into tasks, create implementation tickets, or break down work into tasks.
---

# To Issues

Break a plan into independently-grabbable task slices using vertical slices (tracer bullets), saved to `.plans/tasks/`.

## Process

### 1. Gather context

Work from whatever is already in the conversation context. If the user passes a path to a PRD file, read it with the Read tool. If the user passes a GitHub issue number or URL as an argument, fetch it with `gh issue view <number>` (with comments).

### 2. Explore the codebase (optional)

If you have not already explored the codebase, do so to understand the current state of the code.

### 3. Draft vertical slices

Break the plan into **tracer bullet** slices. Each slice is a thin vertical cut through ALL integration layers end-to-end, NOT a horizontal slice of one layer.

Slices may be 'HITL' or 'AFK'. HITL slices require human interaction, such as an architectural decision or a design review. AFK slices can be implemented and merged without human interaction. Prefer AFK over HITL where possible.

<vertical-slice-rules>
- Each slice delivers a narrow but COMPLETE path through every layer (schema, API, UI, tests)
- A completed slice is demoable or verifiable on its own
- Prefer many thin slices over few thick ones
</vertical-slice-rules>

<slices-vs-prs>
**Slices ≠ PRs**: Slices are units of planning and execution, not necessarily PR boundaries. Multiple small related slices can be grouped into a single PR (perhaps as separate commits). Consider:

- **Separate PRs** when: slices are large, need separate review, or have different reviewers (e.g. FE vs BE)
- **Grouped in one PR** when: slices are small, tightly coupled, or would be awkward to review separately
- **Commits within PR**: Each slice can be a separate commit for clear history

Be smart about PR boundaries - optimize for review experience and deployment atomicity, not just slice count.
</slices-vs-prs>

### 4. Quiz the user

Present the proposed breakdown as a numbered list. For each slice, show:

- **Title**: short descriptive name
- **Type**: HITL / AFK
- **Blocked by**: which other slice titles (if any) must complete first
- **User stories covered**: which user stories this addresses (if the source material has them)

Ask the user:

- Does the granularity feel right? (too coarse / too fine)
- Are the dependency relationships correct?
- Should any slices be merged or split further?
- Are the correct slices marked as HITL and AFK?
- Should any slices be grouped into the same PR, or does each need a separate PR?

Iterate until the user approves the breakdown.

### 5. Write the tasks file

Derive the feature name from the source PRD filename or conversation (e.g. `cart-checkout-flow`). Write all approved slices to `.plans/tasks/<feature-name>.md`. Create the `.plans/tasks/` directory if it doesn't exist.

Write slices in dependency order (blockers first). Use the template below for each slice as a `##` section.

<tasks-template>
# <Feature Name> Tasks

_Source: [.plans/prd/<feature-name>.md](../prd/<feature-name>.md)_ (omit if no PRD file exists)

---

## <Slice Title>

**Type**: AFK | HITL
**Blocked by**: <Slice Title> | None — can start immediately
**PR grouping**: Can be combined with <Slice Title> in one PR | Separate PR recommended (omit this line if not relevant)

### What to build

A concise description of this vertical slice. Describe the end-to-end behavior, not layer-by-layer implementation.

### Acceptance criteria

- [ ] Criterion 1
- [ ] Criterion 2
- [ ] Criterion 3

---

</tasks-template>

### 6. Write the Jira tickets file (optional)

Ask the user: "Should I also generate `.plans/jira/<feature-name>.md` with Jira-ready tickets?" Only proceed if they say yes.

Write all approved slices to `.plans/jira/<feature-name>.md`. Create the `.plans/jira/` directory if it doesn't exist.

Each slice becomes one Jira-ready ticket. Scale the format to the complexity of the slice — do not add sections that add no value. Simple slices need only a title, context sentence, and AC list. Complex slices may include Out of scope and Open questions.

**Acceptance criteria** must be written as observable outcomes (what a tester can verify), not implementation steps. Include enough detail for someone unfamiliar with the codebase to test the ticket. Group criteria by area (e.g. Backend / Frontend) only when the slice spans multiple systems.

<jira-template>
# <Feature Name> — Jira Tickets

---

## <Slice Title>

**Note**: When a slice is specific to one system in a multi-repo setup, prefix the title with the system identifier (e.g. "FE | Add cart validation", "BE | Add cart endpoint"). Do not use brackets like "[FE]".

<1-2 sentences: what this delivers and why it matters now.>

### Acceptance criteria

- Criterion written as an observable outcome
- Criterion written as an observable outcome
- ...

### Implementation notes

**PR grouping**: This can be combined with <other ticket title> in a single PR as separate commits. (omit this section entirely if not relevant)

### Out of scope

- ... (omit this section entirely if nothing is explicitly excluded)

### Open questions

- ... (omit this section entirely if there are no unresolved decisions)

---

</jira-template>
