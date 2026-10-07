# 🚀 beamup-deploy — our apps on baby-beamup.club, two taps

No CLI, no terminal. Everything happens in GitHub.

## Tap 1 — keys (once ever)

Settings → Secrets → Actions → new secret: name `GH_PAT`, value = your
classic token (`repo` + `workflow` + `admin:public_key`).

Then Actions → **🔑 Setup: Generate & Install BeamUp Keys** → Run
workflow. It makes your deploy key + app token and stores them as
`BEAMUP_SSH_KEY` + `APP_TOKEN` secrets. Your app's own secrets go in
`APP_SECRETS` (one `KEY=value` per line). Never put secrets in code,
chat, or README.

## Tap 2 — deploy (per app)

Actions → **🚀 Deploy: Push App to BabyBeamUp** → Run workflow, type the
repo, get your link. Re-tap for updates.

- **App repo** — which repo to host (`owner/repo`).
- **Project** — empty = repo name (`Dockerfile` apps MUST include `docker`).
- **Entry file** — which file starts your app when the repo has no
  `Dockerfile`/`Procfile` (`bot.js`, `main.py`, `start.sh`,
  `package.json`…). Matching `Dockerfile` + `Procfile` are generated for
  the push only; your repo stays untouched. Repos shipping their own
  `Dockerfile` are used as-is.

Ends printing your ✅ app link + the panel link. Re-tap for updates.

## 🌐 Our apps (auto-updated by every deploy)

| Repo (what's hosted)   | Live link                                                  | Since      |
| ---------------------- | ---------------------------------------------------------- | ---------- |
| `zunex69/stremioaddon` | https://11be42da33dd-pencarimovie-streams.baby-beamup.club | 2026-10-05 |

| `SA7ANI/chole-bhature` | https://11be42da33dd-chole-bhature-docker.baby-beamup.club | 2026-10-06 |
| `forbiders/chitra` | https://1e6395aae4d1-chitra-docker.baby-beamup.club | 2026-10-07 |
| `forbiders/chole-bhature` | https://1e6395aae4d1-chole-bhature.baby-beamup.club | 2026-10-07 |
| `forbiders/pencarimovie-server` | https://1e6395aae4d1-pencarimovie-server-docker.baby-beamup.club | 2026-10-07 |
<!-- hosted-apps-end -->

Panel (all apps): https://baby-beamup.club — log in with GitHub.

## Good to know

- Apps must listen on the `PORT` env var; disk is temporary (stay stateless).
- New apps need ~6h before the first deploy lands (`SyntaxError` /
  `update out of sequence` = "not yet", retry later).
