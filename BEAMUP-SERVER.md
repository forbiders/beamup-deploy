# Hosting pencarimovie-server on baby-beamup.club

This is the verified recipe for running
[`aiskendi/pencarimovie-server`](https://github.com/aiskendi/pencarimovie-server)
(the PHP + FrankenPHP + MadelineProto bridge) on the public BeamUp host.
Every claim below was checked against the repo's own files.

## Why it can work (repo facts)

- `heroku.yml` builds the `Dockerfile` (`docker: web:`) and `app.json` uses
  `stack: container` — the project already ships a container deploy path.
- BeamUp builds with Dokku but only syncs **Herokuish** apps to the swarm —
  **unless the project name contains `docker`**, in which case the Docker
  template is used. So the BeamUp project **must** be named something like
  `pencarimovie-docker` (verified: `stremio-beamup` swarm-syncer
  `APP_DOCKER_TMPL`, and the official BeamUp doc's "ugly workaround").
- `Caddyfile` listens on `:{$PORT:8088}` — honors BeamUp's injected `PORT`.
  Plain HTTP, no auto-HTTPS redirect (the file's own comment requires this).
- `docker-entrypoint.sh` is cgroup-aware and has an explicit `$DYNO`
  (Heroku) fallback: ≤512MB → 4/6 threads, 96M PHP limit. It runs on small
  containers.
- First boot on empty storage **self-provisions**: `fd_auto_provision_guest()`
  (`backend.php:797`) mints a guest bot automatically, with stale-lock reclaim
  (`guest_provision.lock` + heartbeat, `:745-790`). No interactive login
  needed for the default guest flow.

## The one real caveat (read before relying on it)

`storage/` holds the Telegram session (`session.madeline`, per-bot
`sessions/…`), `api_secret.key`, `device_id.txt`, `bot_pool.json`,
`auth.json`, locks and cache (`backend.php:159-180,499,735-747`).
`REDIS_URI`/`MYSQL_URI` only offload the MadelineProto ORM/peer DB
(`backend.php:4421-4429`) — **auth keys stay in files**. BeamUp gives you no
volume, so every container restart / redeploy / host migration wipes them:

- what self-heals: guest-bot re-provisioning on next boot (new bot identity,
  a short "Provisioning guest bot..." window, possible Telegram rate limits
  if restarts flap);
- what does not: the previous session, tunnel device id (regenerated,
  `:7703`), any `BOT_TOKEN`/login you did by hand — you must re-enter secrets.

So: it runs, it serves, it recovers — but treat it as **ephemeral**. The VPS
Docker path in the repo README stays the stable option.

## Deploy steps

```bash
# 0. Prereqs: Node.js, a GitHub account, SSH key on GitHub.
npm install beamup-cli -g
beamup config
#   Beamup Host: a.baby-beamup.club
#   GitHub Username: <your-gh-user>

# 1. Get the source (fork first if you want your own copy).
git clone https://github.com/aiskendi/pencarimovie-server.git pencarimovie-docker
cd pencarimovie-docker
#    ^^^ directory/project name MUST contain "docker" (BeamUp rule above).

# 2. First deploy (initializes git remote + beamup.json, then pushes).
beamup
#    prints e.g. https://<12hex>-pencarimovie-docker.baby-beamup.club

# 3. Secrets (never commit these).
beamup secrets SERVER_TOKEN  <32-hex-token-you-generate>
beamup secrets SERVER_PASSWORD <strong-password>
# optional, skips guest minting and survives restarts better:
beamup secrets BOT_TOKEN <123456:ABC...>
# optional performance tuning for small containers:
beamup secrets FRANKENPHP_NUM_THREADS 4
beamup secrets FRANKENPHP_MAX_THREADS 6
beamup secrets PHP_MEMORY_LIMIT 96M

# 4. Verify.
beamup logs
curl -s https://<12hex>-pencarimovie-docker.baby-beamup.club/api/version
curl -s https://<12hex>-pencarimovie-docker.baby-beamup.club/<SERVER_TOKEN>/manifest.json | head -c 300
```

Later updates: `beamup deploy` (or `git push beamup HEAD:master --force`).
Delete: `beamup delete`. Panel: `https://baby-beamup.club` (GitHub login).

## If it fails

| Symptom                           | Cause                                                                               | Fix                                                      |
| --------------------------------- | ----------------------------------------------------------------------------------- | -------------------------------------------------------- |
| `Total beamup app limit exceeded` | shared-host global cap (not per-user)                                               | retry later; nothing code-side fixes it                  |
| build times out                   | Dockerfile pulls the release tarball + vendor (~100MB+ MadelineProto) at build time | retry; or pre-build the image on a VPS registry          |
| `Provisioning guest bot...` loop  | Telegram DC unreachable / rate-limited from host                                    | `beamup logs`, wait, or set your own `BOT_TOKEN`         |
| streams 404 after restart         | storage wiped (see caveat)                                                          | wait for auto re-provision, then re-check `/api/version` |
| OOM / 502 on big files            | tiny container                                                                      | lower threads/chunks via secrets above                   |

## Point the lightweight addon at it

Once the bridge URL is live, paste it into this repo's addon configure page
(`bridgeUrl`) — streams become direct in-app `url`s
(`/api/download?short_code=…`), and the "Test bridge" button pings
`<bridge>/api/version` before you install.
