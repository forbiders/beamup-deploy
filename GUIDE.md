# Deploy to baby-beamup.club — explained like you're 10

> This guide is app-agnostic: it works for **any** repo you deploy with
> this factory. App-specific notes live in [`examples/`](examples/).

## What is all this? (30 seconds)

- **Your code** lives on GitHub (your app repo).
- **baby-beamup.club** is a computer that runs your code day and night so
  the internet can reach it. That computer is called the **host**.
- **Deploying** = sending your code to the host. After that you get a **link**
  like `https://abcd1234-your-app.baby-beamup.club` — open it, install it,
  share it, whatever your app does.

## One-time setup (2 minutes, do it once ever)

You paste exactly ONE thing. The workflows make the rest.

1. Make a token: avatar → Settings → Developer settings →
   **Personal access tokens → Tokens (classic)** → Generate new (classic) →
   tick **`repo`**, **`workflow`**, **`admin:public_key`** → Generate →
   **copy it immediately** (it shows once).
2. This repo → **Settings** → **Secrets and variables** → **Actions** →
   **New repository secret**: name `GH_PAT`, value = that token. Save.

That's the whole setup. (What it unlocks and where everything goes:
[`SECRETS.md`](SECRETS.md).)

## Deploying (two taps)

1. Actions → **1 - Generate all keys** → Run workflow (tick
   `reveal_secrets` only if you want to see your app token once —
   private repos only).
2. Actions → **2 - Deploy app to baby-beamup** → Run workflow (type any
   `owner/repo`, defaults work for the common case).
3. Wait 2–5 minutes. The last step prints your ✅ live link.

Every future update = tap 2 again. One tap.

## Using your deployed app

Open the ✅ live link from the run summary. What "using it" means depends
on your app: install a Stremio manifest, open a dashboard, call an API —
see your app's own README (per-app notes live in [`examples/`](examples/)).

## House rules (read once, save tears)

1. **New apps need ~6 hours before the first deploy lands.** The host
   registers new apps slowly; pushes inside that window fail with weird
   errors (`SyntaxError`, `update out of sequence`). They mean
   _"not yet"_, not _"broken"_. Wait and tap Run again.
2. **Dockerfile apps must have `docker` in the project name.**
   Plain Node apps (like this repo) can be named anything.
3. **Never paste passwords or tokens in chat, code, or issues.**
   Secrets live in repo Settings → Secrets. If one leaks, delete it where
   it was made and make a new one.
4. **Free shared host = be patient.** If a deploy fails twice in a row with
   the same infra-looking error, wait 30+ minutes and retry. The code
   didn't change; the host just hiccuped.

## When something fails

| You see                                     | It means                              | Do this                                                        |
| ------------------------------------------- | ------------------------------------- | -------------------------------------------------------------- |
| `Permission denied (publickey)`             | host doesn't know your key yet        | re-run workflow 1 (it re-installs the key), wait a few minutes |
| `SyntaxError: Unexpected end of JSON input` | host registry hiccup                  | wait, tap Run again later                                      |
| `update out of sequence`                    | host swarm busy/confused              | wait 30+ min, tap Run again                                    |
| App page loads but behaves wrong            | app's own issue (not deploy)          | check your app's logs/health endpoint                          |
| `Total beamup app limit exceeded`           | host is full (global, not your fault) | wait and retry later                                           |

## Where the files are

- Tap 1 (keys): `.github/workflows/1-generate-keys.yml`
- Tap 2 (deploy): `.github/workflows/2-deploy-app.yml`
- Secrets map: `SECRETS.md`
- Per-app notes: `examples/` (e.g. `examples/pencarimovie-server.md`)
