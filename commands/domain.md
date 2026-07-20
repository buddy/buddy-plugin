---
description: Attach a domain to a Buddy deployment
argument-hint: [target] [domain]
allowed-tools: Bash(bdy:*), AskUserQuestion
model: sonnet
---

Attach a domain to an existing Buddy deployment (sandbox endpoint, artifact, or tunnel).

**Arguments:**
- `$1`: Optional deployment name (sandbox/artifact) to attach the domain to
- `$2`: Optional domain name to use

## Workflow

1. **Resolve target:** if `$1` not provided, list deployments (`bdy sandbox list`, `bdy artifact list`) and pick via AskUserQuestion

2. **Ask about domain source** via AskUserQuestion (skip if `$2` provided). Keep option labels short — no CLI commands in labels:
   - **Free Buddy domain** — claim a free subdomain of one of the Buddy-owned domains (see list below), e.g. `myapp.live.page`
   - **Buy a new domain** — search and purchase: `bdy domain search <query>`, then `bdy domain buy <name>`
   - **Existing domain** — reuse a domain already in the workspace: `bdy domain list`

3. **Free subdomains:** users can claim a subdomain under any of these Buddy domains for free:

   `24h.sh`, `alternative.to`, `andriod.io`, `approve.io`, `browser.io`, `chili.io`, `convoy.io`, `ctrl.sh`, `daj.to`, `deployment.to`, `developed.io`, `developertools.io`, `dockerbox.io`, `drivers.io`, `drupal.io`, `edit.sh`, `expressjs.io`, `html.to`, `ico.ai`, `joomla.io`, `jump.to`, `key.sh`, `live.page`, `nitro.io`, `prove.io`, `secrets.io`, `smoo.sh`, `swish.io`, `tested.io`, `ticker.io`, `ubuntu.io`, `upload.sh`, `uptown.io`, `yeah.io`

   Suggest 2-3 subdomain ideas matching the app name (e.g. app `blog` → `blog.html.to`, `blog.live.page`). Check `bdy domain --help` for the claim subcommand.

4. **Wire it up:** route the domain to the deployment with a distribution:
   - `bdy distro create -i <name>` (if none exists)
   - `bdy distro route create <name> -t PROXY -d <domain>` pointing at the deployment URL
   - Run `bdy distro route --help` for target/backend flags

5. **Show results:** final URL, and verify it responds

> **CLI tool:** `bdy` (not "buddy")
