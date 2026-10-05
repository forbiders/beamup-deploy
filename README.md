# 🚀 beamup-deploy — baby-beamup.club with two taps

Deploy any repo you own to [baby-beamup.club](https://baby-beamup.club).
No CLI, no terminal — everything happens in GitHub.

## Tap 1 — keys

Actions → **1 - Generate all keys** → Run workflow.

- Have a `GH_PAT` secret (classic token: `repo` + `workflow` +
  `admin:public_key`)? Everything installs itself.
- Don't? Tick `reveal_secrets` (private repo!) and paste the 3 shown
  values by hand — takes 2 minutes. Details: [`SECRETS.md`](SECRETS.md).

## Tap 2 — deploy

Actions → **2 - Deploy app to baby-beamup** → Run workflow, type any
`owner/repo`. Prints your ✅ live link. Re-tap for every update.

## Rules

- New apps need ~6h before the first deploy lands (`SyntaxError` /
  `update out of sequence` = "not yet", retry later).
- Dockerfile apps must have `docker` in the project name.
- Secrets live in Settings → Secrets. Never in code, chat, or issues.
