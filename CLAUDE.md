# OpenCut — working agreement

Compiled from the global AI Control modules (WORKFLOW, STRUCTURE, CODING, UX)
for this project. See `.project` for the module list; rebuild with the
`rebuild-claude-md` routine when the modules change.

OpenCut is a **free, open-source video editor for web, desktop, and mobile**,
pulled in as-is from upstream `opencut-app/opencut` (the ground-up rewrite).
We did not reinvent or restructure the app — we wrapped it in the standard AI
Control scaffold so it can be run and iterated on here. Respect upstream's own
direction and architecture (Editor API, plugin-first, one Rust core, MCP
server, headless mode, in-editor scripting) rather than designing around it.

## Structure
- Standard AI Control scaffold lives at the project root: `.project`,
  `CLAUDE.md`, `.gitignore` (covers `.env`), `.env` (secrets, never
  committed — currently none needed), `updates/`, `issues.txt`.
- Everything else is upstream's own monorepo layout — **don't impose a
  foreign structure on it.** It's a moon + bun workspace:
  - `apps/web` — the editor (TanStack Start / React 19 / Vite, Cloudflare).
  - `apps/api` — Cloudflare Workers API stub (Elysia).
  - `apps/desktop` — GPUI (Rust, Zed's UI framework) desktop shell.
  - `crates/media` — shared Rust media core.
  - `.prototools` pins `moon`, `bun`, `rust` versions workspace-wide.
- **Rust (`apps/desktop` on GPUI, `crates/media`):** build output goes to the
  shared Cargo target (`~/.cargo-shared-target`), never a per-project
  `target/`. Don't add a `CARGO_TARGET_DIR` override.
- **Updates** go in `updates/YYYY-MM-DD NAME - OPEN|CLOSED/` folders, each with
  `update vX.md` (goal + phased roadmap + live status) and `wiki.md` (durable
  decisions and lessons). Reopen an update by flipping `CLOSED` → `OPEN`.

## Coding
- **Modular, isolatable.** The monorepo already draws the big boundaries
  (web / api / desktop / media core) — keep changes inside the right
  boundary and keep cross-app coupling low, so a change in one app doesn't
  ripple into the others.
- **Minimal.** This is someone else's in-progress rewrite: build only what a
  task actually needs, don't add abstraction on speculation, and don't
  "clean up" architecture decisions that are still in flux upstream.
- **Idiomatic to the ecosystem.** Match the existing conventions exactly:
  TanStack Start/React patterns in `apps/web`, Elysia in `apps/api`, GPUI/Rust
  idioms in `apps/desktop` and `crates/media`. Read the neighboring code before
  writing.
- **Test what matters.** `apps/web` uses `vitest` (`bun run test`). Cover core
  logic and risky paths; skip trivial glue.

## UX
- OpenCut is a **user-facing video editor** — for any change to the editing
  surface, **launch a dedicated UX-expert agent** (several in parallel for
  multi-part surfaces: timeline, preview, export, etc.) and synthesize their
  thinking rather than improvising it.
- **Cut everything unnecessary** (Steve-Jobs default: does this control earn
  its place?). A video editor has a lot of surface area — resist adding more
  than the task needs.
- The stack already leans on solid primitives (Tailwind v4, Radix/`radix-ui`,
  `shadcn`, `base-ui`) — build from those rather than hand-rolling one-offs,
  matching the existing component patterns in `apps/web/src`.
- Responsive and accessible by default — real contrast, keyboard reachability,
  sensible focus, honest error/loading states, especially around media
  import/export which fail in visible, recoverable ways.

## Workflow
- **Commit AND push after every logical change** — small, focused, one unit
  per commit. Plain, honest messages. Never commit secrets (`.env` stays
  ignored; none are needed today).
- **Everything lives on GitHub:** https://github.com/stageerdman/opencut.
  Push regularly; nothing important stays local-only.
- **Roadmaps in phases** for any non-trivial work — design phases first,
  record them in the update's `update vX.md`, track status as you go, most
  phases end with tests.
- **Research spikes** for new APIs/libraries/designs run *outside* the main
  code, inside the update folder, in isolation; capture findings in `wiki.md`.
  Especially relevant here given how much of the rewrite (Rust core, plugin
  architecture, MCP server) is still being designed upstream.
- **Orchestrate:** prefer delegating to focused agents over doing everything
  inline; decompose, fan out, synthesize.
- **Verify by running the actual app** (`bun run dev` in `apps/web`, open it,
  exercise the real flow), not just green tests, before moving on.
