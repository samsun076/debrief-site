# debrief-site — agent notes

Public landing site for **Debrief** (`debrief.run`) — the product lives in `../runTrack`
(private; repo rename to `debrief` pending, runtrack#42).

**Program layer:** this repo is part of a multi-repo program. For program-level work, read
`../RunOrchestrator/MAP.md` first (state) + `../RunOrchestrator/DECISIONS.md` (decision spine —
the rebrand is D17). Rebrand rationale + authoritative state table:
`../runTrack/docs/decisions/2026-07-21-rebrand-debrief.md`.

**Ground rules (from the rebrand record):** static hand-built HTML, no framework, no build step.
Cloudflare Pages, push-to-deploy. Sketch before build; Dave picks by eye. Branch + PR per chunk;
Dave merges. One fact, one appearance; tint over chrome; no decoration that says nothing.
This repo never references the product repo except curated, approved exports. This repo is
**public** — nothing private, no credentials, no minors-adjacent content.
