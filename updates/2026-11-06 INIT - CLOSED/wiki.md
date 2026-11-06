# INIT — wiki

Durable decisions and lessons from bringing OpenCut under AI Control.

## This is upstream's code, not ours to redesign
`opencut-app/opencut` is a rewrite **in progress**, explicitly not accepting
outside contributions yet (per their README). Default to running and
observing it, not restructuring it. If upstream's architecture looks
unfinished or odd, that's expected — check their README/Discord/issues before
assuming it's a bug to fix here.

## moon's tasks are thin wrappers — bun alone is enough for apps/web
`apps/web`'s `moon.yml` `dev` task is literally `command: 'bun run dev'`, which
is itself just `vite dev --port 5173` (see `apps/web/package.json`). `proto`
and `moon` give pinned toolchain versions and cross-app orchestration, but
aren't required to run a single app — `bun install && bun run dev` inside
`apps/web` is sufficient and was all that was installed for the INIT
verification.

## No secrets needed to run the app
The only env var referenced anywhere (`.env.example`: `R2_BUCKET`) is consumed
by a single ops-only moon task (`upload-logos`, root `moon.yml`) that pushes
brand SVGs to Cloudflare R2 — it's a bucket name, not a credential, and it's
irrelevant to `dev`/`build`. Don't assume a video-editor app needs cloud
storage credentials just to boot; check actual `process.env`/`import.meta.env`
usage before adding anything to `.env`.

## The desktop shell is GPUI, not Tauri
The AI Control scaffold (`.project`, `CLAUDE.md`) initially described
`apps/desktop` as a Tauri shell — wrong. It's built on
[GPUI](https://www.gpui.rs) (Zed's UI framework), a plain Rust binary with no
Tauri/webview layer at all. Corrected in both files. Lesson: don't trust a
first-pass scaffold description of upstream's stack without checking
`Cargo.toml`/the app's own README — upstream READMEs are the source of truth.

## macOS needs the Metal Toolchain installed separately for GPUI
`cargo build -p opencut-desktop` failed with `metal shader compilation
failed: cannot execute tool 'metal' due to missing Metal Toolchain` even with
Xcode command line tools installed (`xcode-select -p` succeeding). Xcode CLT
alone doesn't ship the Metal shader compiler on current macOS/Xcode — it's a
separate downloadable component:
```sh
xcodebuild -downloadComponent MetalToolchain   # ~840MB
```
After that, `cargo build -p opencut-desktop` / `moon run desktop:dev` compile
clean. Worth checking for anyone else building a GPUI (or other
Metal-shader-using Rust) app on a fresh Mac.

## proto's shims aren't on PATH by default
The proto install script (`curl -fsSL https://moonrepo.dev/install/proto.sh |
bash`) installs the binaries to `~/.proto/bin` and `~/.proto/shims` but
doesn't touch `~/.zshrc` — needed `export PATH="$HOME/.proto/shims:$HOME/.proto/bin:$PATH"`
added manually. Without the shims dir specifically, `proto run`-aware tool
version switching won't engage even if the raw binaries are found.

## Fresh history, not a fork
We cloned upstream, dropped its `.git`, and started this repo's own history
at one commit containing the full upstream snapshot plus the AI Control
scaffold. This is a personal working copy under `stageerdman/opencut`, not a
GitHub fork of `opencut-app/opencut` — intentional, since AI Control projects
each own their own repo and update history.
