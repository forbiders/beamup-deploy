# SECRETS.md — where every key lives

> Never in code, chat, issues, or README. Leak = delete where made, make new.

## You paste once

This repo → Settings → Secrets → Actions → New secret:

| Name     | Value = classic token with scopes                                                                                                  |
| -------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| `GH_PAT` | `repo` + `workflow` + `admin:public_key` (avatar → Settings → Developer settings → Tokens (classic) → Generate → copy immediately) |

Workflow 1 uses it to install everything below. Afterwards you may delete
it or keep it for re-runs.

## Made for you by workflow 1

Same place (Settings → Secrets → Actions), verify — never paste:

| Name             | What's inside      | Used by                                          |
| ---------------- | ------------------ | ------------------------------------------------ |
| `BEAMUP_SSH_KEY` | Private deploy key | Workflow 2 pushes code to baby-beamup            |
| `APP_TOKEN`      | Random hex token   | Your app's password/token, wherever it needs one |

Public half: avatar → Settings → SSH and GPG keys (`babybeamup-deploy`).

## Your app's own secrets (you write these)

Same place → New secret → name `APP_SECRETS`, value = your lines:

```
BOT_TOKEN=123456:ABC...
DATABASE_URL=postgres://user:pass@host:5432/db
```

Workflow 2 applies them to your app. Skip if your app needs none.
