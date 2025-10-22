# Repository Guidelines

## Project Structure & Module Organization
This repository is a static single-page app. The UI, logic, and styling live together in `index.html`; Tailwind utility classes are pulled from the CDN, and inline JavaScript handles polling, localStorage, and the order card renderer. `example.png` illustrates the expected UI state, `CNAME` anchors the deployed hostname, and `README.md` is the public-facing overview. Keep new assets in the root unless you introduce a dedicated directory and update paths accordingly.

## Build, Test, and Development Commands
- `python3 -m http.server 5173` — start a local static server for live testing.
- `npx http-server .` — alternative dev server with directory listing; requires Node installed.
- `open index.html` (macOS) — quick smoke check without a server; some URL-sync features may be limited.
When adding tooling (e.g., bundlers or linters), document the new command in this section and keep zero-config usage possible.

## Coding Style & Naming Conventions
Follow the existing two-space indentation for HTML, JavaScript, and JSON literals. Favor `const`/`let`, arrow functions, template literals, and object literals that mirror `STATUS_CONFIG`. Tailwind class strings should stay grouped by purpose (layout → spacing → color) to aid diff reviews. Prefer descriptive IDs or keys such as `shopNumber` and `orderNumber`; avoid abbreviations that mask intent. The current code omits semicolons—stay consistent unless you add an automated formatter.

## Testing Guidelines
Manual browser testing is the norm: verify order lookup, the collected-orders collapse, URL sync toggling, and the hourly auto-refresh logic (use DevTools throttling and mocked timestamps). Clear `localStorage` between scenarios to mimic first-run behavior. If you introduce automated tests, place them under a new `/tests` directory, choose a lightweight harness (e.g., Playwright), and ensure they can run headlessly via a single command.

## Commit & Pull Request Guidelines
Write imperative, present-tense commit subjects under ~60 characters (`Support direct 12-digit order ID`). Squash small fixups locally before opening PRs. Pull requests should describe the change, list manual test results, link any related issues, and include refreshed screenshots or GIFs when the UI changes. Keep PRs focused; split refactors from feature work so reviewers can reason about diffs quickly.

## Security & Configuration Tips
Never embed private API keys or internal endpoints—everything should operate against the public DM Fotoparadies interface. Treat `CNAME` as deployment-critical; coordinate before altering it. When logging or sharing debugging output, redact order identifiers to respect user privacy.
