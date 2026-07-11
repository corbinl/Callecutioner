# The Forge — design record (v1.3.0, 2026-07-11)

Why: the app nailed psychology (drivers, sins, bosses) but had no channel for Benjamin's
process/methodology mastery. Corbin's north star for this build: **every call improves you** —
the trader's-report-card loop: dial → dissect → implement one more thing → next dial.

## Content pipeline

Source of truth: `C:\Users\corbi\Downloads\Sales & Learning\VIP Coaching Club\` (session docs,
Master Index, Halfway Point, Mastery Practice Plan FX Edition, Mastery Worksheets Q&A, Scorecard.pdf,
23 raw transcripts). Extraction scripts (python docx/pdf parsing + a node assembler) live in the
LR session scratchpad; they produce `content.js` = one global `FORGE_CONTENT`:

| key | from | count |
|---|---|---|
| `fundamentals` | Halfway Point Part 1 (the Six, each w/ minimum standard + mastered-when) | 6 |
| `sessions` | Session 01–19 + Bonus A/B/C docs (threads: setup / verbatim / breakdown / question bank, fundamentals-tagged) | 22 (S18 stub — no doc on disk) |
| `homework` | Master Index §2 (FX-personalized, quality bars kept) | 108 |
| `highestLeverage` | Halfway Part 2 short list | 20 |
| `drills` | Master Index §3 catalog | 110 (rendered later; catalog shipped in content) |
| `qa` | Mastery Worksheets (recall/application/self) | 66 |
| `wall` | Practice Plan §1 (FX objections × response-questions, session-tagged) | 10 objections / 43 lines |
| `quickref` | transcript verbatim harvest (openers, gatekeeper, email, pricing, UFC, tonality) | 8 situations |
| `rubric` | SYNTHESIZED: S13 five self-inflicted mistakes (no call scorecard exists in the corpus — Scorecard.pdf is the TA P.A.C. self-test) | 5 checks |
| `pac` | Scorecard.pdf scoring rules (the 61 questions are NOT in the corpus) | scoring only |

Rules: Ben verbatim stays verbatim; paraphrase is marked; recall answers not groundable in the
session docs carry `verified:false` → "AI-drafted — verify" badge (Answer Key was AI-generated,
unreviewed by Corbin; 17/22 grounded). Canon still pending Ben: meeting-step order, gauntlet
naming, five-pointers ↔ five-buyer-motivators mapping.

## App design decisions

- 6th bottom tab (amends the 5-tab rule — standalone everyday area beats a buried header button).
- Separate mastery XP (`fx-mastery`, ⚔, own ladder Blunt Iron → The Forgemaster). Never touches
  `dayXP()`/`fx-meta`. **Governor: half rate on zero-dial days** — the documented failure mode is
  building/studying as productive avoidance of dialing; Ben's own doctrine is belief follows behavior.
- XP only for completed reps (drill/spar/order/quest/dissection/internalized One Thing) — never reading.
- Leitner (5 boxes, 0/1/2/4/7/14-day intervals) keyed to day-date strings, deterministic.
- The dissection loop closes where behaviour happens: Card rubric → weakest fundamental →
  Drill prioritizes it; One Thing → pinned in Hunt until ⚒ internalized → logged in Progress.
- Floor protocol (Practice Plan §5) hangs off low-day close: "read the Wall" = the kept promise.
  Zero added friction to the low-day close itself.

## Rebuild content.js

1. Re-run extraction scripts against the corpus (see LR scratchpad `forge/` or regenerate:
   docx → paragraphs via zipfile/document.xml, worksheets PDF via Read, VTT quotes by hand).
2. `node assemble_content.mjs` → content.js, then `node --check content.js`.
3. Bump sw cache + version stamp, deploy, then `npm run sync:callex` in LR.
