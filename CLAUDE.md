# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is a collection of reusable agent skills for Claude Code. Each skill lives in its own directory and is installed globally by symlinking `SKILL.md` into `~/.claude/commands/`.

## Installation

**One-time global setup** (run from repo root):

```bash
mkdir -p ~/.claude/commands
for dir in "$PWD"/*/; do
  name=$(basename "$dir")
  if [ -f "$dir/SKILL.md" ]; then
    ln -sf "$dir/SKILL.md" ~/.claude/commands/"$name".md
  fi
done
```

**Add a single new skill after creation:**

```bash
ln -sf "$PWD/<skill-name>/SKILL.md" ~/.claude/commands/<skill-name>.md
```

Edits to `SKILL.md` take effect immediately — no re-linking needed.

## Skill Structure

Each skill is a directory containing:

```
skill-name/
├── SKILL.md           # Required: YAML frontmatter + instructions
├── REFERENCE.md       # Optional: detailed docs (when SKILL.md would exceed ~100 lines)
├── EXAMPLES.md        # Optional: usage examples
├── *-FORMAT.md        # Optional: output templates (e.g., CONTEXT-FORMAT.md, ADR-FORMAT.md)
├── AGENT-BRIEF.md     # Optional: guidance for sub-agents
├── OUT-OF-SCOPE.md    # Optional: explicit non-goals knowledge base
└── scripts/           # Optional: deterministic utility scripts
    └── helper.sh
```

**SKILL.md frontmatter:**

```yaml
---
name: skill-name
description: One-sentence capability summary. Use when [specific triggers, keywords, or file types].
---
```

## Key Authoring Rules

**Description is critical** — it's the only thing the agent sees when selecting which skill to load. It must include:
1. What capability the skill provides (first sentence)
2. When to trigger it — specific keywords, use cases, or contexts (second sentence, "Use when...")
3. Max 1024 characters, written in third person

**SKILL.md must stay under 100 lines.** Split to separate files (`REFERENCE.md`, `EXAMPLES.md`, etc.) when content is larger or covers distinct domains.

**Add scripts only for deterministic operations** — validation, formatting, blocking hooks. Scripts save tokens and improve reliability over repeatedly generating the same code.

**No time-sensitive information** in skill files — they are durable and version-controlled.

## Skill Categories

| Category | Skills |
|----------|--------|
| Planning & Design | `to-prd`, `to-issues`, `grill-me`, `design-an-interface`, `request-refactor-plan` |
| Development | `tdd`, `triage-issue`, `improve-codebase-architecture`, `migrate-to-shoehorn`, `scaffold-exercises` |
| Tooling & Setup | `setup-pre-commit`, `git-guardrails-claude-code` |
| Writing & Knowledge | `write-a-skill`, `edit-article`, `ubiquitous-language`, `obsidian-vault`, `domain-model`, `qa`, `caveman`, `zoom-out` |

## Adding a New Skill

1. Create directory: `mkdir <skill-name>`
2. Write `SKILL.md` — use the template in `write-a-skill/SKILL.md`
3. Add supporting files only if needed (split at ~100 lines)
4. Symlink: `ln -sf "$PWD/<skill-name>/SKILL.md" ~/.claude/commands/<skill-name>.md`
5. Update `README.md` to list the new skill under the appropriate category

The `/write-a-skill` command in Claude Code will guide you through this process interactively.
