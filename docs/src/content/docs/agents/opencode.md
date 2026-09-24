---
title: OpenCode
description: Where OpenCode reads AGENTS.md, skills, agents, and commands, and what Marginal lists for it.
sidebar: { order: 5 }
---

[OpenCode](https://opencode.ai/docs/) is an open-source terminal coding
agent. Its instructions are `AGENTS.md`; skills, agents, and commands live
in `.opencode` folders in the project and in `~/.config/opencode`.

Select it in **Settings ▸ AI ▸ Coding agent**.

## What Marginal lists

| Section | In the workspace | In your home folder |
|---|---|---|
| **AGENTS.md** | `AGENTS.md` in the root and in any subfolder; `CLAUDE.md` where there is no `AGENTS.md`, [see below](#claudemd-where-there-is-no-agentsmd) | `~/.config/opencode/AGENTS.md`, or `~/.claude/CLAUDE.md` without it |
| **SKILLS** | `.opencode/skills/*/SKILL.md` (or `skill/`), `.claude/skills/*/SKILL.md`, `.agents/skills/*/SKILL.md`, in the root and in any subfolder | `~/.config/opencode/skills` (or `skill`), `~/.claude/skills`, `~/.agents/skills` |
| **AGENTS** | `.opencode/agents/**/*.md` (or `agent/`), in the root and in any subfolder | `~/.config/opencode/agents/**/*.md` (or `agent/`) |
| **COMMANDS** | `.opencode/commands/**/*.md` (or `command/`), in the root and in any subfolder | `~/.config/opencode/commands/**/*.md` (or `command/`) |

OpenCode has no rules folder, so there is no **RULES** section. Extra
instruction files come from the `instructions` key of `opencode.json`
instead, and Marginal does not read that key.

`OPENCODE_CONFIG_DIR`, when set in the environment Marginal was launched
from, replaces `~/.config/opencode`. The `~/.claude` and `~/.agents` folders
stay where they are.

Agents and commands nested in subfolders are named by the path:
`team/reviewer`, `/team/review`.

### CLAUDE.md where there is no AGENTS.md

OpenCode reads `CLAUDE.md`, Claude Code's file, where there is no
`AGENTS.md`. Marginal lists the ones it would read, with *Read in place of
AGENTS.md* as their second line:

- `CLAUDE.md` at the root, when neither the root nor any folder above it has
  an `AGENTS.md`. OpenCode looks up to the root of the git clone, or up to
  the filesystem root outside one.
- `CLAUDE.md` in a subfolder that has no `AGENTS.md` of its own. Each
  subfolder decides for itself: an `AGENTS.md` at the root does not hide
  them, unlike the rule Claude Code applies the other way round.
- `~/.claude/CLAUDE.md`, when there is no `~/.config/opencode/AGENTS.md`.

`OPENCODE_DISABLE_CLAUDE_CODE` or `OPENCODE_DISABLE_CLAUDE_CODE_PROMPT`, set
to `1`, `true`, `yes`, `on`, or `y` in the environment Marginal was
launched from, turns all of these off, as it does in OpenCode.
`OPENCODE_DISABLE_CLAUDE_CODE` also turns off the `.claude/skills` folders
in OpenCode; Marginal still lists those. The deprecated `CONTEXT.md`, which
OpenCode reads when there is neither file, is not listed.

## How OpenCode uses these files

- **AGENTS.md** is collected from the current directory up to the git
  worktree root, every match on that chain; outside a git repository the
  chain runs to the filesystem root. `CLAUDE.md` is consulted only when no
  `AGENTS.md` exists anywhere on the chain, and the deprecated `CONTEXT.md`
  only when neither does. Globally, the first of
  `~/.config/opencode/AGENTS.md` and `~/.claude/CLAUDE.md` that exists is
  used. When OpenCode reads a file, each folder between that file and the
  current directory adds its `AGENTS.md`, or its `CLAUDE.md` when it has
  none, which is why nested ones matter.
- **Skills** are `SKILL.md` files with a required `name` and `description`.
  The `.claude/skills` and `.agents/skills` folders are read for
  compatibility; `OPENCODE_DISABLE_CLAUDE_CODE_SKILLS=1` turns the first
  off. Precedence, lowest to highest: built-in, `.claude/skills`,
  `.agents/skills`, the global folder, `.opencode/skills`, then the
  `skills` array in the config.
- **Agents** are markdown with a front matter `description` (required),
  `mode` (`primary`, `subagent`, or `all`), `model`, `permission`, and
  more; the body is the prompt. The older `mode/` and `modes/` folders are
  still discovered as primary agents.
- **Commands** are markdown with an optional `description`, `agent`,
  `model`, and `subtask` in the front matter; the body is the template,
  with `$ARGUMENTS`, `$1`…`$n`, and shell substitutions. A command named
  like a built-in overrides it.

Both the singular and plural folder names are accepted; the documentation
recommends plural. Every `.opencode` folder from the current directory up
to the worktree root is a config directory, so Marginal looks for them in
every folder of the workspace.

OpenCode V2, in beta, keeps the same folders but recognises only
`AGENTS.md` for instructions and renames several config keys to plural.

## Not listed

`opencode.json` and `opencode.jsonc` (project and global), `tui.json`,
plugins and custom tools (`.ts`, `.js`), themes, and the managed
configuration under `/Library/Application Support/opencode`,
`/etc/opencode`, or `%ProgramData%\opencode`.

Checked against the OpenCode documentation and source on 24 September 2026.
