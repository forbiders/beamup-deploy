# SECRETS.md — where every key lives

> Never in code, chat, or issues. Leak = delete where made, make new.

## Paste once (only for AUTO mode)

This repo → Settings → Secrets → Actions → New secret:

| Name     | Value = classic token with scopes                                                                               |
| -------- | --------------------------------------------------------------------------------------------------------------- |
| `GH_PAT` | `repo` + `workflow` + `admin:public_key` (avatar → Settings → Developer settings → Tokens (classic) → Generate) |

Skip entirely for MANUAL mode.

## Made by workflow 1 (verify, don't paste)

This repo → Settings → Secrets → Actions:

| Name             | What's inside      | Used by                                          |
| ---------------- | ------------------ | ------------------------------------------------ |
| `BEAMUP_SSH_KEY` | Private deploy key | Workflow 2 pushes code to baby-beamup            |
| `APP_TOKEN`      | Random hex token   | Your app's password/token, wherever it needs one |

Public halves: avatar → Settings → SSH and GPG keys (`babybeamup-deploy`).

## Your app's own secrets (you write these)

Same place → New secret → name `APP_SECRETS`, value = your lines:

```
DATABASE_URL=postgres://user:pass@host:5432/db
API_KEY=sk-live-....
```

Workflow 2 applies them to your BeamUp app. Skip if your app needs none.

## See values once

Secrets show as `***` everywhere. Copy them into your password manager
when you make them, never into README or any file.

## Manual mode (no token at all)

Prefer zero tokens? Do once, in any terminal:

```bash
ssh-keygen -t ed25519 -f ~/.ssh/beamup -N "" -C "babybeamup-deploy"
openssl rand -hex 16   # your APP_TOKEN, save it somewhere safe
```

Then paste by hand:

1. **Public key** (`cat ~/.ssh/beamup.pub`) → avatar → Settings →
   **SSH and GPG keys** → New SSH key, title `babybeamup-deploy`.
2. **Private key** (`cat ~/.ssh/beamup`) → this repo → Settings →
   Secrets → Actions → new secret named `BEAMUP_SSH_KEY`.
3. **App token** (the hex from above) → same place → new secret named
   `APP_TOKEN`.

Then run workflow **2** directly — workflow 1 is skipped entirely.
