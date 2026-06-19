---
name: strict-review
description: Comprehensive, opinionated code review using 3 parallel specialist agents (security, tests, architecture). Findings reported inline to the user only — never posts comments anywhere. Use when the user wants to review code on the current branch, a PR number, a PR URL, or a colleague's branch. Checks correctness, bugs, missing tests, dead code, separation of concerns, scalability, security, and acceptance criteria coverage.
---

# Strict Review

Comprehensive code review using 3 parallel specialist agents. Output is a single unified inline report — nothing is written or posted anywhere.

## Setup

### 1. Get the diff

- **No argument**: `git diff main...HEAD` + `git log main..HEAD --oneline`
- **PR number or URL**: `gh pr diff <number-or-url>` + `gh pr view <number-or-url>`
- **Branch name**: `git diff main...<branch>` + `git log main...<branch> --oneline`

### 2. Read project standards

Read if they exist in the repo:
- `CLAUDE.md` — architecture patterns, conventions
- `TESTING-PRINCIPLES.md` — test standards

### 3. Establish intent

Ask the user:

> "Do you have a Jira ticket for this change? Share the ticket number and I'll read it. Otherwise describe the intent in a few sentences, or say 'infer it' and I'll work from the diff and commits."

- **Jira ticket** → fetch via `mcp__claude_ai_Atlassian__getJiraIssue` (cloudId: `commercetools.atlassian.net`), extract summary, description, and acceptance criteria
- **Free-text** → use as-is
- **Infer** → summarise intent from diff + commits, clearly flag it as inferred

### 4. Identify ghost changes

Before spawning agents, scan the diff and note which files conspicuously did NOT change but should have — e.g. hook interface changed but tests didn't update, feature added but `CLAUDE.md` not touched, GraphQL operation added but generated types not regenerated.

## Parallel Review

Spawn all 3 agents simultaneously. Each receives: the full diff, project standards, intent, acceptance criteria (if any), and the ghost changes list.

**Tone rules for all agents — non-negotiable:**
- Be brutally honest. If something is wrong, say so with an explanation of the consequences.
- Challenge entire approaches if they won't scale or are structurally misguided.
- No yes-manning. No softening findings to protect feelings.
- You have no attachment to this code. You did not write it.
- Never write or post comments anywhere. Report findings only.

### Agent 1 — Security & Correctness

- Security vulnerabilities: XSS, injection, exposed secrets, unsafe dependencies, missing input validation at system boundaries
- Runtime bugs: wrong logic, incorrect assumptions, off-by-one errors
- Unhandled edge cases and error paths
- React-specific: missing error boundaries, unhandled promise rejections, stale closures
- Data passed through without validation

### Agent 2 — Test Quality & Completeness

- Missing tests for new or changed behaviour
- Tests that verify implementation details instead of observable behaviour (brittle)
- Missing error scenario coverage
- Whether each acceptance criterion is implemented AND testable
- Files that changed but whose test files did not

### Agent 3 — Architecture & Code Quality

- Dead code: unused imports, unreferenced exports, variables or functions written but never called — flag for removal
- DRY violations within the diff (copy-paste, repeated logic)
- **Cross-codebase duplication**: actively search for existing utilities, hooks, helpers, or functions in the repo that already do what the new code is doing. New code that reinvents something already centralised elsewhere is a DRY violation even if the diff itself looks clean. Grep for similar function names, patterns, or concepts before concluding no duplication exists.
- **Over-engineering**: flag code that is more complex than the problem requires. Patterns, abstractions, or indirection that could be replaced with a straightforward implementation. Ask: could this be half the code and just as correct? If yes, say so and sketch the simpler version.
- Magic strings that should be named constants
- Separation of concerns: logic in display components, business logic in lifecycle hooks, orchestration scattered across files
- Scalability: flag if an entire approach won't scale, explain exactly why and what breaks
- Refactoring opportunities: file isolation, module extraction, components doing too much
- React performance (React 19): expensive computations running on every render, avoidable re-renders caused by unstable object/array references created inline, components with too many responsibilities that cause cascading renders
- Missing updates to types, docs, related hooks, or exports

## Merge & Report

Collect all findings from the 3 agents. Deduplicate: same issue spotted by multiple agents = one entry. Sort by severity. Assign a sequential number to every finding (start at 1, increment across all sections). Output a single inline report.

## Report Format

```
## Strict Review — <branch or PR>

**Intent**: <one-line summary>
**Verdict**: 🔴 Blocked | 🟡 Needs work | 🟢 Ready (with N suggestions)

---

### Acceptance Criteria Coverage
(omit section entirely if no AC was available)
- ✅ <criterion> — implemented and testable
- ❌ <criterion> — not implemented: <explanation>
- ⚠️ <criterion> — partially implemented: <explanation>

---

### 🔴 Blocking Issues
**#1 [file:line or area]** — <problem>. <why it matters>. <what to do instead>.

### 🟡 Warnings
**#2 [file:line or area]** — <problem>. <why it matters>. <what to do instead>.

### 💡 Suggestions
**#3 [file:line or area]** — <improvement>. <rationale>.

---

### 👻 Ghost Changes
Files that should have changed but didn't:
- `path/to/file` — <why it was expected>

### 🗑️ Dead Code
Unused code in the diff that should be removed:
- `path/to/file` — <what is unused and where>
```

Numbers are sequential across all sections (blocking, warnings, suggestions) so any finding can be referenced unambiguously in follow-up conversation (e.g. "fix #3 and #7").
