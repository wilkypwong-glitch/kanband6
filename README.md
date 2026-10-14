# IT PMO Kanban

A single-file Kanban board demo for an IT Project Management Office. Track projects across status columns, drag cards between them, and add new tasks. It is a training/demo app, not a production tool.

**Live demo:** https://wilkypwong-glitch.github.io/kanband6/

## Features

- Board with status columns, task cards and a summary bar (totals, per-status counts, overdue)
- Drag and drop cards, or use the move picker on each card
- Add task form with validation, plus delete with confirmation
- Priority and category pills; overdue highlighting (seed due dates are relative to today)
- Task IDs in the form `UOB-ITPM-####`
- Optional email notification on new tasks via [FormSubmit](https://formsubmit.co)

## Run locally

No build step or dependencies. Open `index.html` in a browser (`file://` works).

## Configuration

Set your notification address in the `FORMSUBMIT_ENDPOINT` constant at the top of the script in `index.html`. If the call fails, the board keeps working and a warning toast is shown.

## Design constraints

- Vanilla HTML/CSS/JS only: no frameworks, bundlers, CDNs, web fonts or image files
- No persistence (no `localStorage`, cookies, etc.): a refresh resets to seed data
- All user-supplied values are escaped before rendering
- A strict Content-Security-Policy limits network access to `formsubmit.co`

## Deployment and security

Pushes to `main` run `.github/workflows/pages.yml`:

1. **security-scan**: fails the build on secrets/tokens, banned APIs (`eval`, storage, cookies), `!important`, external scripts/styles, or any URL other than the FormSubmit endpoint
2. **deploy**: publishes `index.html` to GitHub Pages

## Project layout

| Path | Purpose |
| --- | --- |
| `index.html` | The whole app (markup, styles, script) |
| `.github/workflows/pages.yml` | Security scan and Pages deployment |
| `.claude/commands/push-to-github.md` | `/push-to-github` custom command |
| `CLAUDE.md` | Guidance for Claude Code |
