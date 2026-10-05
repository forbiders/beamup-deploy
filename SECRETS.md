# SECRETS.md — every key, where it goes, copy-paste ready

> Rule: secrets are **never** written in code, chat, or issues.
> They live in exactly two places below. If one leaks, delete it where it
> was made and make a new one.

## A. Secrets YOU paste once (in this repo)

Go to this repo → **Settings** → **Secrets and variables** → **Actions** →
**New repository secret**. Name (left box) must match exactly:

| Secret name | What to paste (right box)                                                               | Where you get it                                                                                                                                                | After setup                                                                                                             |
| ----------- | --------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| `GH_PAT`    | A GitHub token, classic, with scopes **`repo`**, **`workflow`**, **`admin:public_key`** | Avatar → Settings → Developer settings → **Personal access tokens → Tokens (classic)** → Generate new → tick those 3 boxes → Generate → **copy it immediately** | The setup workflow uses it once to install everything below. Afterwards you may **delete** it (or keep it for re-runs). |

> That's the ONLY thing you ever paste by hand. Everything in section B is
> made for you by workflow **1 - Generate all keys**.

## B. Secrets the workflow makes for you (automatic)

Check them anytime: this repo → Settings → Secrets → Actions.
Values are hidden — use **Update** to replace, never to view.

| Secret name      | What's inside                                                     | Used where (exact place)                                                                                                                              |
| ---------------- | ----------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| `BEAMUP_SSH_KEY` | Private deploy key (starts `-----BEGIN OPENSSH PRIVATE KEY-----`) | Read by workflow **2** to push code to baby-beamup. Matching public key sits in avatar → Settings → **SSH and GPG keys** (title `babybeamup-deploy`). |
| `APP_SECRETS`    | App passwords, one `KEY=value` per line, e.g.:                    | Read by workflow **2**, applied to your BeamUp app. Example content:                                                                                  |
|                  | `SERVER_TOKEN=<32 random hex chars>`                              | Token inside Stremio manifest URLs: `/<token>/manifest.json`                                                                                          |
|                  | `SERVER_PASSWORD=<random string>`                                 | Dashboard login on public URLs                                                                                                                        |

## C. Field map (which value goes in which box, no guessing)

```
GitHub → avatar → Settings → SSH and GPG keys → New SSH key
  Title: babybeamup-deploy        (workflow does this for you)
  Key:   <public key>             (workflow does this for you)

This repo → Settings → Secrets and variables → Actions → New repository secret
  Name:  GH_PAT                   ← YOU paste your token here (one time)
  Value: ghp_...                  ← the token from Developer settings

(nothing else to paste — workflows fill BEAMUP_SSH_KEY + APP_SECRETS)
```

## D. Copy-paste cheat sheet (formats only — never real values)

```
# GH_PAT looks like (classic token):
ghp_XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX

# BEAMUP_SSH_KEY looks like (private key, many lines):
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAA...
-----END OPENSSH PRIVATE KEY-----

# APP_SECRETS looks like (one per line):
SERVER_TOKEN=aaaabbbbccccddddeeeeffff0000111122223333
SERVER_PASSWORD=Change-Me-To-Something-Random-123
```

> The values above are **examples**. Yours are generated fresh by the
> workflow and differ every run.
