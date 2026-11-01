# my-zen

One fresh-boot OpenCode machine (`zen1`). No 9router, no backup: every run
starts from zero, builds its OpenCode config from scratch, and leaves
nothing behind in this repo when the runner dies.

## How it works

- `zen1` boots Laravel + PostgreSQL + OpenCode V2 `serve` on `:4096`, over
  Tailscale, exactly like the my-opencode fleets — minus steps 11/12
  (9router) and minus the state restore / sync-back steps.
- Model auth is the Console service-account key, as the environment
  variable `OPENCODE_API_KEY` on the OpenCode step. The run fails fast
  when it is empty, and an advisory smoke check hits the Inference API
  models endpoint before the keep-alive starts. No key is ever written
  to a file or committed anywhere.
- The default model comes from `opencode/default-models.txt` (one full
  model id per line, instance number wraps around); edit it by commit.

## Secrets (set these yourself — values never go through chat)

In this repo: Settings > Secrets and variables > Actions > Secrets:

- `MY_PAT` — GitHub PAT (gh CLI, git push for the fork-beta sync)
- `TAILSCALE_AUTHKEY` — Tailscale auth key
- `RUNNER_PASSWORD` — SSH + OpenCode web UI password
- `OPENCODE_API_KEY` — OpenCode Console service-account key
  (Settings > Service accounts > Add API Key in the Console).
  A repository Variable of the same name works as a fallback, but
  prefer the Secret: Variable values are not masked.

Optional Variable: `OPENCODE_DEFAULT_MODELS` (only used when
`opencode/default-models.txt` is missing or empty).

## Running

Actions > `zen1` > Run workflow, branch `opc`. No schedule; dispatch
manually. Timeout 340 minutes, as before.

## Warnings

- Do NOT run a Remote-Op "Update" on this repo: its embedded template
  still renders the old 9router form and would overwrite `zen1.yml`.
- Remote-Op's readiness probe still requires port 20128, so `zen1`
  shows as STARTING (orange) even when up. Run and the web UI work.
- Fresh boot means sessions, the codebase-memory index and all caches
  are lost at the end of every run. That is the point of this repo.
