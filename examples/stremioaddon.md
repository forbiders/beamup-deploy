# Example: deploying the PencariMovie streams addon

App repo: `zunex69/stremioaddon` (pure-stream Stremio addon, no catalogs).

## Deploy it

Actions → **2 - Deploy app to baby-beamup** → Run workflow with:

| Input         | Value                      |
| ------------- | -------------------------- |
| `target_repo` | `zunex69/stremioaddon`     |
| `project`     | _(empty — uses repo name)_ |
| `ref`         | `main`                     |

Live at `https://<hash>-stremioaddon.baby-beamup.club`.
Install in Stremio: `<live-url>/manifest.json`
(with bridge, see below: `<live-url>/<config>/manifest.json`).

## Secrets it understands

None — this addon takes **no** env secrets (`APP_SECRETS` stays empty).
To turn its Telegram tap-through links into direct in-app DDLs, point it
at a bridge **in the install URL**, not env: open `<live-url>/`, fill the
form (bridge URL + quality prefs), and install from the generated link.
See `pencarimovie-server.md` for hosting the bridge itself.
