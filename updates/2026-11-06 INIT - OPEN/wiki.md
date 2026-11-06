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

## Fresh history, not a fork
We cloned upstream, dropped its `.git`, and started this repo's own history
at one commit containing the full upstream snapshot plus the AI Control
scaffold. This is a personal working copy under `stageerdman/opencut`, not a
GitHub fork of `opencut-app/opencut` — intentional, since AI Control projects
each own their own repo and update history.
