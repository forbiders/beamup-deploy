# 🚀 beamup-deploy — our apps on baby-beamup.club, two taps

No CLI, no terminal, no tokens. Everything happens in GitHub.

## Tap 1 — keys (once ever)

Actions → **1 - Generate all keys** → Run workflow → paste the 3 shown
blocks where it says (~2 minutes). Details: [`SECRETS.md`](SECRETS.md).

## Tap 2 — deploy (per app)

Actions → **2 - Deploy app to baby-beamup** → Run workflow:

- **App repo** — which repo to host (`owner/repo`).
- **Project** — empty = repo name (`Dockerfile` apps MUST include `docker`).
- **Entry file** — which file starts your app if it has no `Procfile`
  (`bot.js`, `main.py`, `start.sh`, `package.json`…). We generate the
  `Procfile` for the push; your repo stays untouched.

Ends printing your ✅ app link + the panel link. Re-tap for updates.

## 🌐 Our apps (auto-updated by every deploy)

| Repo (what's hosted)   | Live link                                                  | Since      |
| ---------------------- | ---------------------------------------------------------- | ---------- |
| `zunex69/stremioaddon` | https://11be42da33dd-pencarimovie-streams.baby-beamup.club | 2026-10-05 |

<!-- hosted-apps-end -->

Panel (all apps): https://baby-beamup.club — log in with GitHub.

## Good to know

- Apps must listen on the `PORT` env var; disk is temporary (stay stateless).
- New apps need ~6h before the first deploy lands (`SyntaxError` /
  `update out of sequence` = "not yet", retry later).
- Secrets live in Settings → Secrets. Never in code, chat, or README.
