# Decision record: RunTrack → Debrief (rebrand + product reframing)

- **Date:** 2026-07-21
- **Status:** Decided. Domain registered. Rename sweep pending in product repo.
- **Projects affected:** `debrief-site` (this repo, public), `runtrack` (product repo, private — to be renamed `debrief`)
- **Tracking:** [debrief-site#1](https://github.com/samsun076/debrief-site/issues/1) (infrastructure), [runtrack#42](https://github.com/samsun076/runtrack/issues/42) (rename sweep)

## What happened

The plan was a landing page for **RunTrack**, with a routine name/domain availability check as task 1.
The check found the name unusable, four naming rounds followed, and the product's commercial
framing sharpened along the way. Net result: the product is now **Debrief** at **debrief.run**.

## Why RunTrack died

Deep research (2026-07-21) found at least five live products on the name or concept:

| Collision | Why it mattered |
|---|---|
| **RunTracker Fitness** (iOS/Play, active) | Near-identical name AND the identical privacy pitch: "your data belongs to you, never leaves your device" — our lead differentiator, already claimed |
| **"RunTrack"** web app (runtrackapp.com, reg. 2026-07-02) | Live, polished browser run-tracker under the exact name |
| **RunTrack Pro** (runtrack.app, reg. 2026-01) | Owns the .app domain in-category |
| **runtrack.run** (since 2018) | Active site + privacy-focused tracker app in development |
| App Store clutter | RunTrack GPS and others block a clean listing |

The namespace was also being squatted in real time — getruntrack.com registered **three days**
before the check. "Run track" is descriptive, weak as a mark, and buried under generic
"run tracker" SEO (Runkeeper, MapMyRun).

## The naming rounds (candidates and kill reasons)

1. **Postrun** — killed: live "PostRun" (postrun.io, Strava→Instagram tool, same audience,
   opposite ethos) + postrun.ai SaaS owns GitHub/npm namespace.
2. **RunLore** — killed: iOS app "Runlore" shipped **2026-07-12** (nine days before the check)
   as a private-by-default running journal — our positioning, near verbatim.
3. **Quiet / quiet.run** — killed: domain squatted; iOS "Quiet Run" (May 2026) has our exact
   positioning ("Space, not pressure… run, and reflect"); "QuietMiles" (Feb 2026) same concept.
4. **Miles / miles.run** — killed: domain held by a third party through 2031; "Miles – Running
   Tracker" (Miles Vinson, since 2024, 4.9★) owns the indie-minimalist lane under his own first name.
5. **Threshold** — killed decisively: threshold.run is a **live competitor** (course-aware race
   coaching, watch auto-ingest, adaptive plans); "Threshold: Running Coach" iOS (May 2026) has
   plans + coach chat. Three fitness founders took the word within twelve months.
6. **Shakeout** — viable with caveats (category empty, but running-media mindshare belongs to the
   "morning shakeout" newsletter; shakeout.run needs acquiring). Held as runner-up, not chosen.
7. **Debrief** — chosen. See below.

## The market signal (important context beyond naming)

The thesis — *anti-Strava, privacy-first, reflection-over-recording* — was independently invented
**at least five times in ~18 months** (RunTracker Fitness, runtrack.run, Runlore, Quiet Run,
QuietMiles), all as small iOS *recorders*. Nobody has the desktop / BYO-AI / data-sovereign
architecture. Implications: (a) the concept is being validated in real time, (b) plain-language
descriptive names get claimed monthly — distinctive naming and speed both matter, (c) the moat is
the architecture, not the category.

## Why Debrief won

- **Zero collisions in the running/fitness space** — no app, tool, or trademark named Debrief there.
  Remaining collisions are dead (YC W21 startup), other-space (Royal Navy maritime-analysis OSS owns
  the GitHub org), or traction-less (small AI notetakers).
- **debrief.run was hand-registerable** — the only good name in four rounds whose domain wasn't
  squatted, held, or a live competitor. Registered 2026-07-21 at Dynadot (with seedmark.run).
- **The name IS the product's core interaction** — the post-run debrief with your coach. A competing
  running app (Kotcha) already calls its post-run conversation a "debrief": the word is becoming
  category vocabulary, which is what you want a name to be.
- Known costs, accepted: bare-word "Debrief" SEO is unwinnable (thedebrief.org, maritime tool);
  bare npm/PyPI names taken (scoped names fine); "debrief.run" is effectively the brand string.

## Product reframing (happened during the naming work)

Debrief is not just a reflection journal. Current concept:

- **Desktop app (Electron, local-first)** — analysis and review of running with an AI coach the
  user brings (BYO Claude/GPT API key). Data stays local.
- **No manual entry, ever** — watch data auto-ingests; the AI writes the first draft of every
  reflection; the runner reacts instead of filling in blank boxes.
- **The app publishes to the runner's own site** — weekly / monthly / per-activity views: next
  week's plan with per-run intent and goals, then post-run debrief findings. Publishing is
  **rendering, not sharing** — nothing social, no feed. The site is an output artifact
  (static files → Cloudflare Pages), not a service.
- Commercial differentiator vs. incumbents (Strava Athlete Intelligence, Garmin Connect+, WHOOP):
  they are cloud subscriptions analyzing data they hold; Debrief inverts that.

## Current state & next actions

| Item | State | Owner |
|---|---|---|
| debrief.run + seedmark.run registered (Dynadot) | ✅ done 2026-07-21 | — |
| Site repo public, hello-world on `main` | ✅ done | — |
| Cloudflare zone + Dynadot NS + Pages connect | ⏳ blocked on dashboard steps | Dave → then agent ([#1](https://github.com/samsun076/debrief-site/issues/1)) |
| Landing page sketch round A (2–3 directions, AI-coach copy) | queued | agent, Dave picks |
| Product repo rename sweep (incl. Electron userData migration trap) | filed, not started | [runtrack#42](https://github.com/samsun076/runtrack/issues/42) |
| USPTO TESS knockout search for DEBRIEF | open — gate before heavy public investment | Dave/agent |
| seedmark.run disposition (park vs. Cloudflare) | open | Dave |

## Ground rules carried forward

Static hand-built HTML, no framework, no build step. Cloudflare Pages, push-to-deploy. Sketch
before build; Dave picks by eye. Branch + PR per chunk; Dave merges. One fact, one appearance;
tint over chrome; no decoration that says nothing. The site repo never references the product
repo except curated, approved exports.
