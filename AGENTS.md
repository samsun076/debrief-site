# debrief-site — agent notes

Public site for **Debrief** (debrief.run): local-first desktop running app with an AI coach.
This repo is hand-built static HTML — no framework, no build step. Deploys to Cloudflare
Pages on push to `main` (wiring tracked in #1).

**Read first:** `docs/decisions/2026-07-21-rebrand-debrief.md` — the rebrand rationale,
product concept, and current-state table. Treat its table as project status.

## Ground rules

- Sketch before build: throwaway HTML mockups in `sketches/` (gitignored), 2–3 variants,
  Dave picks by eye. Taste-check ideas with Dave before spending research on them.
- Branch + PR per chunk; Dave merges. Never commit to `main` directly.
- One fact, one appearance. Tint over chrome. No decoration that says nothing.
- Never reference or import from the private product repo (`samsun076/runtrack`,
  becoming `debrief`) except curated exports Dave approves.
- Visual quality is non-negotiable; match the product's light, chart-forward, hero-tile idiom.

## Run Orchestrator (PM)

Dave runs a PM agent ("runorchestrator") across his projects. When project state changes
(decisions, issues opened/closed, milestones), keep it findable:

- Canonical status lives in the decision record's current-state table — update it when
  state changes rather than scattering status elsewhere.
- Track work as GitHub issues in this repo; cross-link to `runtrack` issues where work
  spans both.
- TODO(Dave): where does runorchestrator run, and how should agents hand it context —
  does it read this repo directly, or does it need updates pushed somewhere?
