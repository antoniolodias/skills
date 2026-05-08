# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is a collection of reusable agent skills for Claude Code. Each skill lives in its own directory and is installed globally by symlinking `SKILL.md` into `~/.claude/commands/`.

## Folder Structure

Skills are organized into bucket folders under `skills/`:

- `engineering/` — daily code work
- `productivity/` — daily non-code workflow tools
- `misc/` — kept around but rarely used
- `personal/` — tied to my own setup, not promoted
- `in-progress/` — drafts not yet ready to ship
- `deprecated/` — no longer used

Every skill in `engineering/`, `productivity/`, or `misc/` must have a reference in the top-level `README.md` and an entry in `.claude-plugin/plugin.json`. Skills in `personal/`, `in-progress/`, and `deprecated/` must not appear in either.

Each skill entry in the top-level `README.md` must link the skill name to its `SKILL.md`.

Each bucket folder has a `README.md` that lists every skill in the bucket with a one-line description, with the skill name linked to its `SKILL.md`.

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

```text
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

## Adding a New Skill

1. Create directory under the appropriate `skills/<bucket>/` folder
2. Write `SKILL.md` — use the template in `write-a-skill/SKILL.md`
3. Add supporting files only if needed (split at ~100 lines)
4. Symlink: `ln -sf "$PWD/<skill-name>/SKILL.md" ~/.claude/commands/<skill-name>.md`
5. Update `README.md` and the bucket's `README.md` to list the new skill

The `/write-a-skill` command in Claude Code will guide you through this process interactively.
