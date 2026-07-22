# Infrastructure — Debrief Program

> Covers deployment, DNS, secrets, and account topology for **debrief.run** and **seedmark.run**.
> This is the operational reference — decisions live in `RunOrchestrator/DECISIONS.md`.

---

## Account Topology

| Service | Email / Username | Account ID | Owns | Notes |
|---------|-----------------|------------|------|-------|
| **Cloudflare** (program) | `Samsun076.program@gmail.com` | `a19e4a45a976f928d5e60345454a0d56f` | `debrief.run` zone, `seedmark.run` zone, Pages projects | Dedicated account — separate from personal `automagic.ly` CF account |
| **Dynadot** | `samsun076` / `davemarcinowski@gmail.com` (secondary: `dave@automagic.ly`) | `d_m_cheshire_us` | `debrief.run`, `seedmark.run` domain registrations | Registered 2026-07-21 |
| **Doppler** | `dave@automagic.ly` / workspace: `automagicly` | `4abdc8ec573543535d36` | Agent-accessible secrets (Track A, O5) | Developer Plan (free), 5-project cap |
| **GitHub** | `samsun076` | — | `debrief-site` (public), `runtrack` (private, rename pending #42) | Push-to-deploy source for CF Pages |
| **Fly.io** | _TBD_ | — | `seedmark` org (future) | Existing account, new org per D16 |

---

## Domain → DNS → Deployment

```
┌─────────────┐       ┌──────────────────────┐       ┌─────────────────────┐
│   Dynadot   │  NS   │     Cloudflare       │       │      GitHub         │
│  registrar  │──────▶│  DNS + edge proxy    │       │  samsun076/         │
│             │       │                      │       │  debrief-site       │
│ debrief.run │       │  Zone: debrief.run   │◀──────│  (push-to-deploy)   │
│seedmark.run │       │  Zone: seedmark.run  │ Pages │                     │
└─────────────┘       └──────────────────────┘       └─────────────────────┘
```

### debrief.run

| Layer | Provider | Config |
|-------|----------|--------|
| **Registrar** | Dynadot | Nameservers: `garret.ns.cloudflare.com`, `miki.ns.cloudflare.com` |
| **DNS** | Cloudflare | Zone in program account |
| **CDN / Edge** | Cloudflare Pages | Static HTML, no build step |
| **Source** | GitHub | `samsun076/debrief-site`, branch: `main` |
| **Build** | None | Static — serve root directory as-is |
| **Custom domain** | `debrief.run` + `www.debrief.run` → Pages project (CNAME → `debrief-site-94j.pages.dev`) |
| **HTTPS** | Cloudflare-managed, Google CA, auto-provisioned ✓ |

### seedmark.run _(wiring pending — same pattern)_

| Layer | Provider | Config |
|-------|----------|--------|
| **Registrar** | Dynadot | Nameservers → Cloudflare |
| **DNS** | Cloudflare | Zone in program account (same account as debrief) |
| **App hosting** | Fly.io | `seedmark` org, Docker container (future) |
| **Content site** | Cloudflare Pages | `samsun076/seedmark`, `site/` directory |
| **Topology** | Apex (`seedmark.run`) = content/Pages; `app.seedmark.run` = Fly (future) |

---

## Secrets Management (O5 — Doppler, Track A)

**Architecture:** Doppler free tier → per-project secrets → `doppler run --` runtime injection.
Machine/service tokens stored in **macOS Keychain** (per-machine, per-project scoped).

### Where secrets live

Secrets are stored in **two places** with distinct purposes:

| Store | Purpose | Scope | Used by |
|-------|---------|-------|---------|
| **Doppler** (`program-cf/prd`) | Local/agent CLI use | `doppler run --` injects at runtime | Developer machines, Claude Code sessions |
| **GitHub Secrets** (`samsun076/debrief-site`) | CI auto-deploy | `${{ secrets.* }}` in Actions workflows | GitHub Actions only |

The same `CLOUDFLARE_API_TOKEN` and `CLOUDFLARE_ACCOUNT_ID` values exist in both stores. This is intentional — GitHub Actions can't pull from Doppler without a Doppler token (which would just move the problem), and Doppler can't be used in CI without adding a dependency. Two values, not a maintenance burden; rotate in both places when rotating.

### Doppler project layout

| Doppler Project | Secrets | Used by |
|-----------------|---------|---------|
| `program-cf` | `CLOUDFLARE_API_TOKEN`, `CLOUDFLARE_ACCOUNT_ID`, `DYNADOT_API_KEY`, `DYNADOT_SECRET_KEY` | Both domains — shared Cloudflare account |

### GitHub repo secrets

| Repo | Secrets | Used by |
|------|---------|---------|
| `samsun076/debrief-site` | `CLOUDFLARE_API_TOKEN`, `CLOUDFLARE_ACCOUNT_ID` | `.github/workflows/deploy.yml` |

### Token scoping (D16 posture)

**Cloudflare deploy token** (`debrief-site-deploy`, ID: `43cc74aebf48630618a173decfcd059f`):
- Permission: `Cloudflare Pages Write` (account-scoped)
- Used by both Doppler and GitHub Secrets

Zone scope: all zones in the program account (covers both `debrief.run` and `seedmark.run`).

### Repo integration

Each repo commits a `.doppler.yaml` that auto-selects the right project/config:

```yaml
# .doppler.yaml (committed to git)
setup:
  project: program-cf
  config: prd
```

### Usage pattern

```bash
# All infrastructure commands run through Doppler
doppler run -- npx wrangler pages deploy .
doppler run -- npx wrangler dns ...
```

---

## Deployment Pipeline

```
Developer pushes to main
        │
        ▼
GitHub Actions (.github/workflows/deploy.yml)
        │
        ▼
cloudflare/wrangler-action@v3
  ├─ apiToken:   ${{ secrets.CLOUDFLARE_API_TOKEN }}
  └─ accountId:  ${{ secrets.CLOUDFLARE_ACCOUNT_ID }}
        │
        ▼
wrangler pages deploy . --project-name debrief-site
        │
        ▼
Cloudflare Pages serves static files
        │
        ▼
Live at debrief.run (+ www.debrief.run)
```

**No build step.** Cloudflare Pages serves the repo root as static HTML.
The `sketches/` directory is gitignored and never deployed.

### Token rotation for CI

When rotating the Cloudflare API token, update **both** stores:

1. Create new token in CF dashboard (or via API) with `Pages Write` permission
2. `doppler secrets set CLOUDFLARE_API_TOKEN=<new-token> --project program-cf --config prd`
3. `gh secret set CLOUDFLARE_API_TOKEN --repo samsun076/debrief-site --body "<new-token>"`
4. Delete old token in CF dashboard

---

## Security Posture

- **No global API keys** — scoped tokens only (D16)
- **No secrets in git** — `.doppler.yaml` is config, not credentials
- **Runtime injection only** — `doppler run --` injects env vars for the command's lifetime
- **Revocation scope** — lost laptop = revoke one Keychain-stored service token, rotate one CF token
- **Doppler is stood up** — interim `.env` posture superseded as of 2026-07-22

---

## Operational Runbook

### First-time setup (new machine)

```bash
# 1. Install CLIs
brew install dopplerhq/cli/doppler
npm install -g wrangler

# 2. Authenticate
doppler login          # browser auth
npx wrangler login     # browser auth (uses Doppler-injected token after setup)

# 3. Clone and go — .doppler.yaml auto-selects secrets
cd ~/Projects/debrief-site
doppler run -- npx wrangler pages deploy .
```

### Deploy manually (outside push-to-deploy)

```bash
cd ~/Projects/debrief-site
doppler run -- npx wrangler pages deploy . --project-name debrief-site
```

### Rotate Cloudflare token

1. Generate new token in CF dashboard (or via API) with `Pages Write` permission
2. `doppler secrets set CLOUDFLARE_API_TOKEN=<new-token> --project program-cf --config prd`
3. `gh secret set CLOUDFLARE_API_TOKEN --repo samsun076/debrief-site --body "<new-token>"`
4. Delete old token in CF dashboard

---

## Change Log

| Date | What |
|------|------|
| 2026-07-22 | Initial setup: CF account, Doppler, Pages project, DNS wiring, GitHub Actions auto-deploy |
