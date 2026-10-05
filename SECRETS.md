# SECRETS.md — every key, where it goes, copy-paste ready

> Rule: secrets are **never** written in code, chat, or issues.
> They live in exactly two places below. If one leaks, delete it where it
> was made and make a new one. This factory is app-agnostic — substitute
> your own app's secret names wherever you see examples.

## A. The ONE secret you paste by hand (in this repo)

This repo → **Settings** → **Secrets and variables** → **Actions** →
**New repository secret**. Name (left box) must match exactly:

| Secret name | What to paste (right box)                                                               | Where you get it                                                                                                                                                | After setup                                                                                                    |
| ----------- | --------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------- |
| `GH_PAT`    | A GitHub token, classic, with scopes **`repo`**, **`workflow`**, **`admin:public_key`** | Avatar → Settings → Developer settings → **Personal access tokens → Tokens (classic)** → Generate new → tick those 3 boxes → Generate → **copy it immediately** | Workflow 1 uses it once to install everything below. Afterwards you may **delete** it, or keep it for re-runs. |

> That's the ONLY thing you ever paste by hand. Everything in section B is
> made for you by workflow **1 - Generate all keys**.

## B. Secrets the workflow makes for you (automatic)

Check them anytime: this repo → Settings → Secrets → Actions.
Values are hidden — use **Update** to replace, never to view.

| Secret name      | What's inside                                                             | Used where (exact place)                                                                                                                              |
| ---------------- | ------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| `BEAMUP_SSH_KEY` | Private deploy key (starts `-----BEGIN OPENSSH PRIVATE KEY-----`)         | Read by workflow **2** to push code to baby-beamup. Matching public key sits in avatar → Settings → **SSH and GPG keys** (title `babybeamup-deploy`). |
| `APP_TOKEN`      | One random hex token                                                      | A generic password/API token. Use it wherever your app needs one secret (dashboard login, URL token, …).                                              |
| `APP_SECRETS`    | **Your** app's secrets, one `KEY=value` per line — you write these, e.g.: | Read by workflow **2**, applied to your BeamUp app. Examples (any app):                                                                               |

```
DATABASE_URL=postgres://user:pass@host:5432/db
API_KEY=sk-live-....
SERVER_TOKEN=....
```

## C. Field map (which value goes in which box, no guessing)

```
GitHub → avatar → Settings → SSH and GPG keys → New SSH key
  Title: babybeamup-deploy        (workflow does this for you)
  Key:   <public key>             (workflow does this for you)

This repo → Settings → Secrets and variables → Actions → New repository secret
  Name:  GH_PAT                   ← YOU paste your token here (one time)
  Value: ghp_...                  ← the token from Developer settings

  Name:  APP_SECRETS              ← YOU paste your app's KEY=value lines here
  Value: (your app's secrets, one per line)

(nothing else to paste — workflows fill BEAMUP_SSH_KEY + APP_TOKEN)
```

## D. Copy-paste cheat sheet (formats only — never real values)

```
# GH_PAT looks like (classic token):
ghp_XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX

# BEAMUP_SSH_KEY looks like (private key, many lines):
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAA...
-----END OPENSSH PRIVATE KEY-----

# APP_TOKEN looks like (32 hex chars):
aaaabbbbccccddddeeeeffff0000111122223333

# APP_SECRETS looks like (one per line, YOUR keys):
DATABASE_URL=postgres://user:pass@host:5432/db
API_KEY=sk-live-....
```

> The values above are **examples**. Real ones are generated fresh by the
> workflow (or written by you) and differ every time.

## E. Seeing your token once (read this before asking for README)

**Secrets are never written into README or any repo file.** A README is
public and git remembers forever — that would hand your passwords to the
entire internet, permanently. Instead:

1. (Recommended) Make this repo **private**: repo → Settings → General →
   Danger Zone → **Change visibility** → private. Tooling repos don't need
   to be public.
2. Actions → **1 - Generate all keys** → Run workflow → tick
   **reveal_secrets** → Run.
3. The run's **Summary** page shows your `APP_TOKEN` **once**.
   Copy it into your password manager, done. Re-running without the tick
   shows nothing.

No reveal, no view: secret values live only in Settings → Secrets, and even
there GitHub shows `***`. That hiding is the protection — embrace it.
