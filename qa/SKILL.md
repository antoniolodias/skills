---
name: qa
description: Interactive QA session where user reports bugs or issues conversationally, and the agent writes them to local markdown files. Explores the codebase in the background for context and domain language. Use when user wants to report bugs, do QA, file issues conversationally, or mentions "QA session".
---

# QA Session

Run an interactive QA session. The user describes problems they're encountering. You clarify, explore the codebase for context, and write durable, user-focused bug reports to `.plans/qa/`.

## For each issue the user raises

### 1. Listen and lightly clarify

Let the user describe the problem in their own words. Ask **at most 2-3 short clarifying questions** focused on:

- What they expected vs what actually happened
- Steps to reproduce (if not obvious)
- Whether it's consistent or intermittent

Do NOT over-interview. If the description is clear enough to file, move on.

### 2. Explore the codebase in the background

While talking to the user, kick off an Agent (subagent_type=Explore) in the background to understand the relevant area. The goal is NOT to find a fix — it's to:

- Learn the domain language used in that area (check UBIQUITOUS_LANGUAGE.md)
- Understand what the feature is supposed to do
- Identify the user-facing behavior boundary

This context helps you write a better report — but the report itself should NOT reference specific files, line numbers, or internal implementation details.

### 3. Assess scope: single report or breakdown?

Before writing, decide whether this is a **single report** or needs to be **broken down** into multiple sections.

Break down when:

- The fix spans multiple independent areas
- There are clearly separable concerns that different people could work on in parallel
- The user describes something with multiple distinct failure modes or symptoms

Keep as a single report when:

- It's one behavior that's wrong in one place
- The symptoms are all caused by the same root behavior

### 4. Write the report(s)

Derive a short kebab-case name from the bug description (e.g. `checkout-total-wrong`). Write to `.plans/qa/<bug-name>.md`, creating `.plans/qa/` if needed. Do NOT ask the user to review first — just write and share the file path.

Reports must be **durable** — they should still make sense after major refactors. Write from the user's perspective.

#### For a single report

```markdown
## What happened

[Describe the actual behavior the user experienced, in plain language]

## What I expected

[Describe the expected behavior]

## Steps to reproduce

1. [Concrete, numbered steps a developer can follow]
2. [Use domain terms from the codebase, not internal module names]
3. [Include relevant inputs, flags, or configuration]

## Additional context

[Any extra observations that help frame the issue — use domain language but don't cite files]
```

#### For a breakdown (multiple issues in one file)

Use `##` sections per issue in the same file. Write sections in dependency order (blockers first).

```markdown
## <Issue Title>

**Blocked by**: <Other Issue Title> | None — can start immediately

### What's wrong

[Describe this specific behavior problem — just this slice]

### What I expected

[Expected behavior for this specific slice]

### Steps to reproduce

1. [Steps specific to THIS issue]

### Additional context

[Any extra observations relevant to this slice]
```

#### Rules for all reports

- **No file paths or line numbers** — these go stale
- **Use the project's domain language** (check UBIQUITOUS_LANGUAGE.md if it exists)
- **Describe behaviors, not code** — "the sync service fails to apply the patch" not "applyPatch() throws"
- **Reproduction steps are mandatory** — if you can't determine them, ask the user
- **Keep it concise** — a developer should be able to read the report in 30 seconds

After writing, share the file path and ask: "Next issue, or are we done?"

### 5. Continue the session

Keep going until the user says they're done. Each report is independent — don't batch them.
