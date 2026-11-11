# OpenCode Skills

A small, opinionated set of [OpenCode](https://opencode.ai) skills for shipping changes safely: test the right surface, show evidence, get approval, write clean commits and PRs, and keep diffs reviewable.

These skills are repo-agnostic. They read the host repository's own instructions and CI instead of hard-coding commands, so the same workflow works across projects.

## Skills

| Skill | Trigger | What it does |
| --- | --- | --- |
| `testing-before-push` | Before staging, committing, pushing or opening a PR | Chooses the right test for the change, runs the browse MCP flow for UI changes, collects evidence, enforces approval order, scans for secrets and reports what was not tested |
| `create-commits` | Staging changes or writing a commit message | Keeps staging scoped to the change and formats an imperative commit message |
| `create-pull-requests` | Opening a PR or writing its title and body | Applies the repo template, checks every checklist item and keeps the PR to summary plus verification |
| `code-quality` | Implementing or reviewing a change | Enforces existing conventions, small diffs, explicit errors, tests, boundary validation and self-review |
| `deep-project-research` | Researching a codebase or tracing a path | Keeps research read-only, starts from entry points, stops when the question is answerable and cites file paths |

## How the skills fit together

`testing-before-push` is the gate. `create-commits` and `create-pull-requests` do not repeat its rules; they point to it. `code-quality` applies while you write the change. `deep-project-research` is for understanding the code before you change it.

```text
research -> change -> test -> show evidence -> approval -> commit -> PR
```

## Install

Clone the repository and copy the skill folders into your OpenCode skills directory:

```bash
git clone https://github.com/tharinfernando/opencode-skills.git ~/src/opencode-skills
mkdir -p ~/.config/opencode/skills
cp -a ~/src/opencode-skills/*/ ~/.config/opencode/skills/
```

Each destination folder must contain `SKILL.md`. Restart OpenCode or reload the window so the skills are discovered.

For a single project, copy the folders into `.opencode/skills/` instead.

## Usage

Skills are selected by their descriptions. Ask naturally:

- "Commit this" loads `create-commits`, which first sends you through `testing-before-push`.
- "Open a PR" loads `create-pull-requests`, with the same test gate.
- "Review this diff" loads `code-quality`.
- "Trace how authentication works" loads `deep-project-research`.

## The workflow

1. Identify the change surface.
2. Run the tests that surface requires.
3. Show the evidence.
4. Ask for approval to stage and commit.
5. Stage only the relevant files and commit.
6. Open a PR only when it is requested and approved.

## Design rules

- The repo is the source of truth. Commands come from `AGENTS.md`, CI config, package scripts, then docs. CI wins on conflict.
- Never invent a command, URL, environment variable or deployment step.
- Approval is per action. A PR request does not approve a new commit.
- UI changes use the browse MCP. If it is unavailable, stop instead of switching browser tools.
- No em dashes (U+2014) in commits, PRs or skill files.

## Requirements

- An OpenCode build with skills support.
- The browse MCP for the browser-test workflow in `testing-before-push`.
- Git for the commit and PR workflows.

## Browser MCP

UI testing uses the **browse MCP** only. It drives your real browser through the browse extension and is required for the browser-test workflow in `testing-before-push`.

- Project: https://github.com/anomalyco/browse
- Install: `curl -fsSL https://opencode.ai/browse/install | bash`
- Or with Node.js: `npx @opencode/browse install`
- Browser extension: https://chromewebstore.google.com/detail/mfnicocicmmlkpjnaffgihfjhdgjkdjg

The installer adds the `browse` MCP server to your agent and pairs the browser extension. Verify it with `browse status`.

These skills explicitly do not use the Playwright MCP. Do not add it as a fallback.

## Layout

```text
.
├── code-quality/SKILL.md
├── create-commits/SKILL.md
├── create-pull-requests/SKILL.md
├── deep-project-research/SKILL.md
├── testing-before-push/SKILL.md
└── README.md
```

## Editing a skill

- Keep the description focused on when the skill should trigger.
- Put rules in the body.
- Reference the skill that owns a rule instead of duplicating it.
- Keep `name` equal to the folder name.
- Check for U+2014 before committing.

There is no build step. Each skill is a single `SKILL.md` file.
