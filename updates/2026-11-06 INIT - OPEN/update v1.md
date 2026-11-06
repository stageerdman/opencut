# INIT — OpenCut

**Status:** OPEN · created 2026-11-06 · plan v1

## Goal
Setup this project and make it work as an app — no invented features, no
adoption of anything further. Bring upstream
[`opencut-app/opencut`](https://github.com/opencut-app/opencut) (the in-progress
rewrite of the free, open-source video editor) under AI Control as its own
project, and get it actually **running**.

## Scope decisions (agreed at INIT)
- **Source:** the exact upstream rewrite repo, `opencut-app/opencut` — not the
  classic version (`opencut-app/opencut-classic`) and not a reinvented app
  under the same name. Code pulled in as-is, no restructuring.
- **Visibility:** **public** GitHub repo (`stageerdman/opencut`).
- **History:** upstream's own git history was dropped — this repo starts a
  fresh history of its own at the first commit. (It is a personal working
  copy, not a GitHub fork relationship.)
- **"Make it work":** confirmed by actually installing and running the app,
  not just reading the README.

## What's in this repo (as pulled from upstream)
Moon + bun monorepo:
- `apps/web` — the editor: TanStack Start (React 19) on Vite, Cloudflare Vite
  plugin. Dev on `localhost:5173`.
- `apps/api` — Cloudflare Workers API stub (Elysia): `/`, `/health`, `/echo`.
- `apps/desktop` — Tauri (Rust) desktop shell.
- `crates/media` — shared Rust media core.
- `brand/` — logos/marks; `changelog/`.

Upstream's own README states the rewrite is early: not yet open to outside
contributions, still building an Editor API, plugin architecture, one Rust
core across web/desktop/mobile, an MCP server, headless mode, and an in-editor
scripting tab.

## Phased roadmap

### Phase 1 — Scaffold under AI Control
- Choose modules: WORKFLOW, STRUCTURE, CODING, UX (compiled to `CLAUDE.md`).
- Create `.project`, `.gitignore` (upstream's already covers `.env`/`.env.*`),
  `.env`, `updates/`, `issues.txt`.
- **Verify:** standard scaffold present, `.env` stays gitignored.

### Phase 2 — Install and run
- Install `bun` (not present on the machine; installed via Homebrew, 1.4.4 —
  close to the `.prototools`-pinned 1.3.11).
- `cd apps/web && bun install` (576 packages, clean) `&& bun run dev`.
- **Verify:** dev server boots on `:5173` with no errors; `curl` returns
  `HTTP 200` and the real page title ("OpenCut rewrite | beta.opencut.app"),
  confirming TanStack Start + the Cloudflare adapter render correctly.

### Phase 3 — GitHub + first commit
- Create `stageerdman/opencut` (public), connect as `origin`, push the first
  commit (upstream snapshot + AI Control scaffold).
- **Verify:** repo visible on GitHub, default branch pushed, `git status`
  clean locally.

## Status tracking
- [x] Phase 1 — scaffold — DONE.
- [x] Phase 2 — install and run `apps/web` — DONE & VERIFIED (see above).
      `apps/api` and `apps/desktop` **not yet** installed/run — tracked in
      `issues.txt`.
- [x] Phase 3 — GitHub repo + first commit — DONE.

## Where we are (handoff — 2026-11-06)
`apps/web` runs clean locally with `bun install && bun run dev`. No secrets
are required for local dev. `apps/api` and the Rust/Tauri `apps/desktop` are
present but unverified — next session should install `bun` deps for `apps/api`
and confirm `wrangler dev` works, then attempt a Tauri dev build once/if the
desktop app is prioritized. `proto`/`moon` aren't installed; everything so far
was run with `bun` directly since moon's `web:dev` task is just `bun run dev`
under the hood.

## NEXT STEP (resume here)
1. `cd apps/api && bun install && bun run dev` — verify the Workers stub
   responds on `:8787` (`/health`).
2. Decide whether to install `proto` + `moon` for the full pinned-toolchain
   workflow, or keep driving each app directly with `bun`/`cargo`.
3. If/when desktop work is wanted: install the Tauri prerequisites and attempt
   `apps/desktop`'s dev build per `apps/desktop/README.md`.
