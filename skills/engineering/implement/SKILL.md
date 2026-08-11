---
name: implement
description: "Implement a piece of work based on a spec or set of tickets."
disable-model-invocation: true
---

Implement the work described by the user in the spec or tickets.

Use /tdd where possible, at pre-agreed seams.

Commit after every RED → GREEN → REFACTOR cycle — no exceptions. Never let multiple cycles accumulate without committing; small, frequent commits let you bisect history, roll back safely, and show clear progress. Each commit message is one line only, conventional commits format (`type: subject`), no co-authored information.

Run typechecking regularly, single test files regularly, and the full test suite once at the end.

Once done, use /code-review to review the work.
