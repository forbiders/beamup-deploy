# Deploy to baby-beamup.club — explained like you're 10

## What is all this? (30 seconds)

- **Your code** lives on GitHub (this repo).
- **baby-beamup.club** is a computer that runs your code day and night so
  Stremio can reach it. That computer is called the **host**.
- **Deploying** = sending your code to the host. After that you get a **link**
  like `https://abcd1234-your-app.baby-beamup.club`, and that link is what
  you install in Stremio.

## One-time setup (5 minutes, do it once ever)

### 1. Make a key (your ID card for the host)

The host only trusts computers that show the right key. Make one here —
or in GitHub Codespaces / any terminal:

```bash
ssh-keygen -t ed25519 -f ~/.ssh/beamup -N "" -C "babybeamup-deploy"
cat ~/.ssh/beamup.pub
```

It prints one long line starting with `ssh-ed25519`. Copy it.

### 2. Give GitHub the public half

1. GitHub → your avatar (top right) → **Settings**
2. **SSH and GPG keys** → **New SSH key**
3. Title: `babybeamup-deploy`, paste the line, save.

### 3. Give this repo the private half

1. Open this repo → **Settings** → **Secrets and variables** → **Actions**
2. **New repository secret**, name it exactly:
   - `BEAMUP_SSH_KEY` → paste the _private_ key (the file `~/.ssh/beamup`,
     the one that starts with `-----BEGIN OPENSSH PRIVATE KEY-----`).
3. (Optional, same place) `APP_SECRETS` → lines like:
   ```
   SERVER_TOKEN=any-random-text-you-make-up
   ```
   …if your app reads secrets. This addon needs none, so you can skip it.

That's the whole setup. You never touch keys again.

## Deploying (10 seconds, the TAP part)

1. Open this repo on GitHub → **Actions** tab (top).
2. Click **Deploy to baby-beamup** (left side).
3. Click the **Run workflow** button → **Run workflow** again.
4. Wait 2–5 minutes. The last step prints your ✅ live link.

That's it. Every future update = same button, one tap.

## Install it in Stremio

- **This addon (movie streams):** paste the manifest link in Stremio →
  Addons → search box, or tap on your phone:
  `stremio://11be42da33dd-pencarimovie-streams.baby-beamup.club/manifest.json`
  (replace with _your_ app link from the step above).
- Open any movie from your catalog addon → streams appear.

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

| You see                                     | It means                              | Do this                                            |
| ------------------------------------------- | ------------------------------------- | -------------------------------------------------- |
| `Permission denied (publickey)`             | host doesn't know your key yet        | check step 2; keys sync a few minutes after adding |
| `SyntaxError: Unexpected end of JSON input` | host registry hiccup                  | wait, tap Run again later                          |
| `update out of sequence`                    | host swarm busy/confused              | wait 30+ min, tap Run again                        |
| App page loads but streams are empty        | source site down or no match          | check `/health` on your app link                   |
| `Total beamup app limit exceeded`           | host is full (global, not your fault) | wait and retry later                               |

## Where the files are

- Tap-to-deploy button: `.github/workflows/deploy-beamup.yml`
- This addon code: `index.js`, `manifest.js`, `lib/`
- PHP bridge hosting recipe: `BEAMUP-SERVER.md`
