# 🚀 beamup-deploy — baby-beamup.club without touching a terminal

[![1 - Generate all keys](https://img.shields.io/badge/1-Generate_all_keys-8a5aab)](https://github.com/zunex69/beamup-deploy/actions/workflows/1-generate-keys.yml)
[![2 - Deploy app](https://img.shields.io/badge/2-Deploy_app-7b5bf5)](https://github.com/zunex69/beamup-deploy/actions/workflows/2-deploy-app.yml)

Deploy **any** repo to [baby-beamup.club](https://baby-beamup.club) with two
taps. No CLI, no `ssh` on your PC — everything happens inside GitHub.

## The whole thing in 3 taps

1. **Paste one secret.** This repo → Settings → Secrets → Actions → New
   secret: name `GH_PAT`, value = your classic token (scopes `repo`,
   `workflow`, `admin:public_key`). Full click-path in [`SECRETS.md`](SECRETS.md).
2. **Tap 1:** Actions → **1 - Generate all keys** → Run workflow.
   Makes your deploy key + app passwords and stores them. Done — nothing
   to copy anywhere by hand.
3. **Tap 2:** Actions → **2 - Deploy app to baby-beamup** → Run workflow.
   (Inputs have sane defaults: deploys `zunex69/stremioaddon`.)
   Ends by printing your ✅ live link.

## Files

| File                                    | What it is                                                     |
| --------------------------------------- | -------------------------------------------------------------- |
| `.github/workflows/1-generate-keys.yml` | Tap 1: makes SSH keypair + random tokens, installs them        |
| `.github/workflows/2-deploy-app.yml`    | Tap 2: pushes any repo to BeamUp, applies secrets, prints URL  |
| [`SECRETS.md`](SECRETS.md)              | Every secret: name, exact box it goes in, format, who makes it |

## Rules that bite (learned the hard way)

- **New apps need ~6h before the first deploy lands.** Pushes inside that
  window fail (`SyntaxError`, `update out of sequence`) — they mean
  _"not yet"_, retry later. The deploy workflow retries 3× automatically.
- **Dockerfile apps must have `docker` in the project name.** Plain Node
  apps can be named anything.
- Secrets live in Settings → Secrets. Never in code, chat, or issues.
