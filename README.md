# 🚀 beamup-deploy — baby-beamup.club with two taps

Deploy any repo you own to [baby-beamup.club](https://baby-beamup.club).
No CLI, no terminal — everything happens in GitHub.

## Tap 1 — keys

Actions → **1 - Generate all keys** → Run workflow.

- Have a `GH_PAT` secret (classic token: `repo` + `workflow` +
  `admin:public_key`)? Everything installs itself.
- Don't? Follow the manual paste steps in [`SECRETS.md`](SECRETS.md#manual-mode-no-token-at-all)
  — 2 minutes, no token ever created.

## Tap 2 — deploy

Actions → **2 - Deploy app to baby-beamup** → Run workflow, type any
`owner/repo`. Prints your ✅ live link. Re-tap for every update.

---

# 📖 The full BeamUp manual (every bit)

## 1. What BeamUp actually is

BeamUp is a shared hosting computer (one cheap VPS) that runs small apps
for the Stremio community. You can read Stremio's own one-page doc here:
[`stremio-addon-sdk/docs/deploying/beamup.md`](https://github.com/Stremio/stremio-addon-sdk/blob/master/docs/deploying/beamup.md).
How a deploy flows, end to end:

1. Your code is pushed with `git push` to `dokku@<host>:<your-hash>/<project>`
   (`<your-hash>` = first 12 letters of the SHA256 of your lowercase
   GitHub username + a newline — the workflows compute this for you).
2. The host **builds** it with [Dokku](http://dokku.viewdocs.io/dokku/)
   (same build system, so anything Dokku can build, BeamUp can — except
   extras like custom NGINX config).
3. It **runs** it as a swarm service and puts it on the internet at
   `https://<your-hash>-<project>.baby-beamup.club` (HTTPS handled for you).

## 2. Which files your project needs (the hoster files)

There is no single magic file — the builder **detects** your project type:

| What you have                           | What happens                                                                                                                                                              | Example                        |
| --------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------ |
| `package.json` (Node.js)                | Heroku Node buildpack: `npm install` + `npm start`                                                                                                                        | This factory's example apps    |
| `requirements.txt` / `Pipfile` (Python) | Heroku Python buildpack                                                                                                                                                   | Any Flask/FastAPI app          |
| `composer.json` (PHP)                   | Heroku PHP buildpack                                                                                                                                                      | Plain PHP apps                 |
| `Dockerfile`                            | Docker build — **only if your project name contains `docker`** (BeamUp rule, from Stremio's own doc: _"an ugly workaround … is to include 'docker' in the project name"_) | `pencarimovie-docker`          |
| `app.json` / `heroku.yml` / `Procfile`  | Extra Dokku instructions (processes, formation)                                                                                                                           | Optional, most apps skip these |

**The one hard requirement for every project:** your app must listen on
the `PORT` environment variable BeamUp injects (e.g. Node:
`process.env.PORT || 7000`). If nothing listens, the deploy "succeeds"
but the URL never answers.

**Static-only repos** (plain HTML/JSON, no server process) do **not**
deploy — something must hold the port open.

## 3. RAM, disk, and limits (how the host behaves)

- **Small shared box.** Dozens of apps share one machine. Keep apps light:
  small dependencies, low worker/thread counts, no giant in-memory caches.
  (Well-built apps read the container's memory limit and tune themselves
  down — see any `docker-entrypoint.sh` with cgroup checks for the pattern.)
- **Disk is temporary.** There are no permanent folders. Anything your app
  writes (sessions, logins, uploads, sqlite files) **vanishes on restart,
  redeploy, or host maintenance**. Design stateless: keep state in env
  vars, or an external database over the network — never on local disk.
- **Restarts happen.** Updates, host maintenance, out-of-memory kills.
  Apps must boot cleanly from zero with no hand-holding (auto-provision,
  defaults for every setting).
- **Global caps exist.** The host sometimes answers `Total beamup app
limit exceeded` (host-full, nobody's fault) — wait and retry. New apps
  can also take **~6 hours** before the registry accepts them; pushes
  inside that window fail with `SyntaxError` / `update out of sequence`
  (means _"not yet"_, not _"broken"_).

## 4. Rules that bite (learned the hard way)

- Secrets live in GitHub Settings → Secrets. Never in code, chat, issues,
  or README (git never forgets — a leaked secret must be deleted where it
  was made and replaced).
- Dockerfile apps must have `docker` in the project name (see table).
- If a deploy fails twice with the same infra-looking error, wait 30+
  minutes and retry. The code didn't change; the host hiccuped.

## 5. Files in this factory

| File                                    | What it is                                                            |
| --------------------------------------- | --------------------------------------------------------------------- |
| `.github/workflows/1-generate-keys.yml` | Tap 1: makes SSH keypair + random app token, installs them            |
| `.github/workflows/2-deploy-app.yml`    | Tap 2: pushes any `owner/repo` to BeamUp, applies secrets, prints URL |
| [`SECRETS.md`](SECRETS.md)              | Every secret: name, exact box it goes in, format, who makes it        |
