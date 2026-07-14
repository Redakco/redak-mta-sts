# redak-mta-sts

MTA-STS (RFC 8461) policy host for **redak.com** on Azure Static Web Apps.
Custom domain: `mta-sts.redak.com`

## How the policy is served (important)

Azure SWA's default build (Oryx) can drop dot-folders like `.well-known` from the
deployed output. To make this immune to that, the policy physically lives at the
repo root as **`mta-sts.txt`**, and `staticwebapp.config.json` rewrites the
canonical URL `/.well-known/mta-sts.txt` to it. A copy also sits in
`.well-known/` as belt-and-braces. The workflow uses `skip_app_build: true`
(no build needed for static files; direct upload preserves all files).

| File | Purpose |
|---|---|
| `mta-sts.txt` | The policy (physical file, root) |
| `.well-known/mta-sts.txt` | Copy at the canonical path |
| `staticwebapp.config.json` | Rewrite + text/plain + cache headers |
| `index.html` | Info page |

## Updating the policy
1. Edit **both** `mta-sts.txt` and `.well-known/mta-sts.txt` identically
   (e.g. `mode: testing` → `mode: enforce`, `max_age: 86400` → `604800`).
2. Commit to `main` — GitHub Actions redeploys (~2 min).
3. **Bump the `id`** in the `_mta-sts.redak.com` TXT record (new timestamp).
