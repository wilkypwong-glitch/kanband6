# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

A single-file IT PMO Kanban demo/training app: `index.html` (markup, `<style>`, `<script>` all inline). There is no build step, package manager, linter or test suite. Run it by opening `index.html` directly in a browser (`file://`).

## Hard constraints (from the original brief)

- Vanilla HTML/CSS/JS only: no frameworks, bundlers, CDNs, web fonts or image files. Icons are Unicode/inline SVG.
- No persistence: no `localStorage`, `sessionStorage`, IndexedDB or cookies. A refresh resets to seed data on purpose (the UI says so).
- The only network call is the FormSubmit AJAX endpoint (`FORMSUBMIT_ENDPOINT`, config constant at the top of the script). The email address must appear only there.
- No real bank logo/trademarks; text wordmark "IT PMO" and a blue palette only.
- CSS uses custom properties; no `!important`.

## Architecture

- `state = { tasks: [], filters: {} }` is the source of truth. A separate `ui` object holds transient flags (which card shows delete-confirm or the move picker, current drag id).
- All card/board DOM is produced in `renderBoard()` / `renderCard()` / `renderSummary()` via string templates; every user-supplied value must go through `escapeHtml()`. Never mutate card DOM directly; change `state`/`ui` and re-render.
- Board events use delegation on `#board` (click actions via `data-action`, native HTML5 drag/drop). Mutations go through `moveTask`, `deleteTask`, `addTask`.
- Add-task flow (`handleSubmit`): validate → optimistic `addTask` + render → toast → `notifyNewTask` in try/catch. A FormSubmit failure must only show a warning toast, never break the board.
- Security briefing dialog (`#briefBackdrop`): `init()` schedules `openBriefing` after `BRIEFING.delayMs` (10 s); it shows once per page load and the date is always the coming Monday. It owns Escape/Tab while open, so keep its check first in the `keydown` handler.
- WhatsApp IT support widget (`#chat`, fixed bottom-right, z-index 9 so modals cover it): `CHAT` config (number + suggested queries) renders plain `https://wa.me/<number>?text=…` links; no script sends anything. Toasts sit above it (`bottom: 88px`). Escape closes it after the briefing/form checks. Keep query texts free of personal data (they go in the URL).
- Task IDs are `UOB-ITPM-####` from an incrementing `idCounter` (seeded tasks consume the first IDs).
- Seed due dates are computed relative to today, so overdue examples stay overdue.
