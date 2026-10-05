# SECRETS.md — where every key lives

> Never in code, chat, issues, or README. Leak = delete where made, make new.

## Made by workflow 1 (verify, don't paste)

Run it, paste the 3 shown blocks:

| #   | Paste this                       | Where it goes (exact clicks)                                                      | Saved as            |
| --- | -------------------------------- | --------------------------------------------------------------------------------- | ------------------- |
| 1   | Public key (`ssh-ed25519 AAAA…`) | Avatar → Settings → **SSH and GPG keys** → New SSH key, title `babybeamup-deploy` | — (stays on GitHub) |
| 2   | Private key (`-----BEGIN…`)      | This repo → Settings → Secrets → Actions → New secret                             | `BEAMUP_SSH_KEY`    |
| 3   | Token (32 hex chars)             | Same place → New secret                                                           | `APP_TOKEN`         |

## Your app's own secrets (you write these)

Same place → New secret → name `APP_SECRETS`, value = your lines:

```
BOT_TOKEN=123456:ABC...
DATABASE_URL=postgres://user:pass@host:5432/db
```

Workflow 2 applies them to your app. Skip if your app needs none.
