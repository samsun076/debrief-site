# debrief-site

Public landing site for [Debrief](https://debrief.run) — hand-built static HTML, no framework, no build step.

## Deployment

Push to `main` → GitHub Actions → Cloudflare Pages → live at `debrief.run`.

Manual deploy:

```bash
doppler run -- npx wrangler pages deploy . --project-name debrief-site --branch main --commit-dirty=true
```

## Local setup

```bash
brew install dopplerhq/cli/doppler
npm install -g wrangler
doppler login
doppler setup    # .doppler.yaml auto-selects the project
```

## Infrastructure

See [docs/infrastructure.md](docs/infrastructure.md) for account topology, DNS, secrets, and the operational runbook.
