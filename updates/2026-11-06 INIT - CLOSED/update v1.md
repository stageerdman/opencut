# INIT — OpenCut

**Status:** CLOSED · created 2026-11-06 · closed 2026-11-06 · plan v1

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

### Phase 4 — Verify the remaining apps + full toolchain
- Install `apps/api` deps and run `wrangler dev`; hit `/`, `/health`, `/echo`.
- Install `proto` + the pinned toolchain (`moon`, `bun`, `rust` per
  `.prototools`); put `proto`'s shims on `PATH` via `.zshrc`.
- Build and run `apps/desktop` (GPUI, not Tauri — corrected a scaffold
  inaccuracy; see `wiki.md`). Fix the macOS Metal Toolchain gap this
  surfaced.
- Confirm `moon run <app>:dev`/`:check` orchestration actually works end to
  end, not just the direct `bun`/`cargo` commands.
- **Verify:** all three apps (web, api, desktop) install and run clean via
  both the direct commands and `moon run`.

## Status tracking
- [x] Phase 1 — scaffold — DONE.
- [x] Phase 2 — install and run `apps/web` — DONE & VERIFIED.
- [x] Phase 3 — GitHub repo + first commit — DONE.
- [x] Phase 4 — verify `apps/api`, `apps/desktop`, `proto`+`moon` — DONE &
      VERIFIED (see `wiki.md` for the Metal Toolchain and GPUI-vs-Tauri
      lessons).

## Where we are (handoff — 2026-11-06)
All three apps now install and run clean:
- `apps/web` — `bun install && bun run dev`, or `moon run web:dev`.
- `apps/api` — `bun install && bun run dev` (wrangler on `:8787`), or
  `moon run api:dev`. `/`, `/health`, `/echo` all verified.
- `apps/desktop` — `cargo run` (binary lands in `~/.cargo-shared-target/debug/`
  per the shared-target convention, not a local `target/`), or
  `moon run desktop:dev`. Needed the macOS Metal Toolchain component
  installed first.

`proto` + `moon` are installed and on `PATH` (`~/.proto/shims`,
`~/.proto/bin`, added to `~/.zshrc`). Pinned versions match `.prototools`:
moon 2.3.3, bun 1.3.11, rust 1.97.0.

## NEXT STEP (resume here)
Everything in the original "make it work" goal is done and verified. No open
next step — future work on this project is feature/task-driven, not setup.
Re-check `issues.txt` for the one standing item (upstream is mid-rewrite,
re-diff deliberately rather than drifting out of sync).
