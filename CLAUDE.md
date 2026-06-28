# CLAUDE.md

Guidance for Claude Code (and other AI assistants) working in this repository.

## What this repository is

`vermont09/vermont09` is a **Claude Code plugin marketplace**. It is not an
application — there is no build, test, or runtime to execute. The repo's
content is entirely Claude Code configuration: a marketplace manifest, one
first-party plugin (commands + an agent), and a reference to a third-party
plugin.

## Structure

```
.claude-plugin/
  marketplace.json   # Marketplace manifest — lists every plugin this marketplace offers
  plugin.json         # Manifest for the first-party "github-workflow-helper" plugin
agents/
  github-pr-reviewer.md   # Custom subagent definition (used via the Agent tool)
commands/
  pr-status.md         # /pr-status slash command definition
  triage-issues.md     # /triage-issues slash command definition
superpowers-main.zip   # Vendored snapshot of github.com/obra/superpowers (unzipped, unreferenced)
```

### `.claude-plugin/marketplace.json`

Declares the marketplace named `vermont09-marketplace`, owned by `vermont09`.
Its `plugins` array currently lists:

- `github-workflow-helper` — `source: "./"`, i.e. this repo's own root is the
  plugin (the `agents/` and `commands/` directories below).
- `marketing-skills` — `source: "github:alirezarezvani/claude-skills"`, an
  external plugin pulled in by reference only (no vendored code here).

When adding a new plugin to the marketplace, add an entry here with a
`name`, `description`, and a `source` (a local path or a `github:` /
url-style reference) — do not vendor third-party plugin code into this repo
unless that is explicitly requested.

### `.claude-plugin/plugin.json`

The manifest for the `github-workflow-helper` plugin itself: name, semver
`version`, `description`, `author`, `license`, and `keywords`. Bump
`version` when the plugin's commands or agents change in a way consumers
should notice.

### `agents/github-pr-reviewer.md`

A subagent definition (frontmatter: `name`, `description`, `tools`, `model`)
plus a system prompt. It restricts itself to `Read, Grep, Glob, Bash` — no
edit tools — because it's a read-only reviewer. Follow this pattern for new
agents: keep the tool list as narrow as the agent's job allows, and write
the prompt as direct instructions to the agent, not a description of it.

### `commands/*.md`

Slash command definitions. Frontmatter has a single `description` field;
the body is the prompt that runs when the command is invoked, with
`$ARGUMENTS` as the placeholder for whatever the user passes after the
command name (e.g. `/pr-status owner/repo`). Both existing commands
(`pr-status`, `triage-issues`) default to operating on "the current
repository" when no argument is given, and end with an explicit instruction
not to take side-effecting actions (modifying PRs/issues) unless asked —
preserve that read-only-by-default convention in any new command.

### `superpowers-main.zip`

A zipped snapshot of the external `obra/superpowers` skills-library plugin,
added via a raw file upload. It is **not** referenced by `marketplace.json`
or `plugin.json` and is not unpacked anywhere in the repo — it appears to be
leftover/staged content rather than an active part of the marketplace. Don't
assume code from inside the zip is part of this repo's behavior; if
superpowers should actually be offered through this marketplace, that needs
an explicit `source` entry in `marketplace.json` (pointing at the
`obra/superpowers` GitHub repo, the way `marketing-skills` references its
source) rather than a vendored zip.

## Working conventions

- This repo has **no package.json, build step, linter, or test suite**.
  "Testing" a change means validating the JSON manifests parse and that
  command/agent markdown files have well-formed YAML frontmatter.
- Keep `agents/` and `commands/` flat and named after what they do; the
  filename (minus extension) is the command/agent name Claude Code uses to
  invoke them.
- Treat `marketplace.json` as the source of truth for "what plugins exist
  here" — if you add a command, agent, or new plugin directory, make sure
  there's a corresponding entry (or that it's clearly part of the existing
  `github-workflow-helper` plugin's `./` source).
- Default to non-destructive behavior in command/agent prompts (read,
  report, suggest) and require explicit user instruction before describing
  any action that modifies GitHub state (labels, comments, merges).

## Branch history note

This repository currently has several independent feature branches (no
shared `main`/`master`) that diverged after the initial plugin scaffold,
each adding a different piece: a `.claude/rules/context7.md` MCP rule, an
expanded (unzipped) copy of `superpowers/`, or the `marketing-skills`
marketplace entry documented above. When reconciling branches, check
`.claude-plugin/marketplace.json` and `plugin.json` on each side for
conflicting plugin entries before merging.
