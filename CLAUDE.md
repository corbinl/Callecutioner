# CLAUDE.md — Callecutioner

Personal sales quest log for Corbin. One user, one iPhone, used one-handed during live outbound cold-calling blocks. This file is the project's memory. Read it fully before changing anything.

## What this is

A gamified behavioural report card that fuses two methodologies:
1. **Benjamin Dennehy's sales system** (Corbin is in his 1-year Coaching Club). Disqualification-first selling. Core rule: you cannot control outcomes, only thoughts, actions, behaviours. XP is awarded ONLY for behaviours, NEVER for outcomes (no XP for revenue, deals closed, etc.). This rule is inviolable.
2. **Jut Meininger's "How to Run Your Own Life"** (Transactional Analysis). Key vocabulary used throughout: the tape (old Parent/Adapted Child recordings), the five drivers, permissions, the Martian view (facts without interpretation), the Powerless Cycle, Natural Child vs Adapted Child. Do NOT use "rackets" or "trading stamps" — those are other TA authors, not Meininger.

The daily loop: **Dawn** (5-min ritual before first dial) → **Hunt** (live tap tallies during the block) → **Card** (90-sec report card after real conversations) → **Dusk** (5-min close, banks XP, extends streak) → **Ledger** (analytics).

## Stack + files

Single-file vanilla JS PWA. No frameworks, no dependencies, no build step. Everything must keep working offline.

- `index.html` — the entire app (CSS + HTML + JS in one file)
- `sw.js` — service worker, cache-first
- `manifest.json`, `icon-180.png`, `icon-512.png`, `README.md` — rarely change

Deployed via GitHub Pages from repo root: https://corbinl.github.io/Callecutioner/

## Deploy rules (every index.html change)

1. Bump the service worker cache name in `sw.js`: `fx-v4` → `fx-v5` → etc. If you skip this, phones keep serving the old cached version forever.
2. Bump the version stamp in index.html (search for "Callecutioner v1.2.1", bottom of Ledger's Your Data card). Corbin verifies updates landed by checking this stamp.
3. Commit + push. Pages rebuilds in ~1 min. On the phone: open app, fully close, reopen (first open fetches, second runs).
4. Verify JS syntax before committing: extract the `<script>` body and run `node --check` on it.

## Data model (localStorage, all keys prefixed `fx-`)

- `fx-meta`: `{bankedXP, streak, lastClosed}` — lastClosed is a YYYY-MM-DD string; streak increments only if yesterday was closed.
- `fx-day-YYYY-MM-DD`: one object per day: `{dawn:{gratitudes[3], thought, driver, justif, martian, permission, ncStates[], quests[3], firstDial, complete}, tallies:{dials,dm,pitch,grinder,pin,meeting,nos,noBonuses}, cards:[], dusk:{questsDone[3], behaviourRank, feel, cycle, cycleBroke, hungryFor, reclaim, correlation, closed, lowDay}}`
- Card shape: `{type, company, steps[11], pointers[], enthusiasm, sins:{crabwalk,begged,iFocused}, boss, bossResult, driver, bait, ben, fact, oneThing, time}`. Cold-call cards use 11 steps (incl. referral); meetings use the 10 meeting-anatomy steps. Render sizes the steps array by type (`setCardType`) and guards missing indices (`card.steps[i]||0`), so legacy 10-step cards stay valid and just show "not reached" for step 11. `driver` on a card is now "where I ran the call from": one of the 5 drivers (hooked = Adapted Child) OR a healthy place (Natural Child / Nurturing Parent / Adult). Cards without `type` are legacy = "Cold call".
- `fx-carddraft`: in-progress card. `fx-tab`: last open tab.
- `fx-script`: string[11] parallel to the Gauntlet STEPS — Corbin's editable cold-call script (Playbook). `fx-objections`: `{trigger,response}[]` — real objections/pushbacks + comeback. `fx-questions`: `{trigger,response}[]` — client-questions that surface as stalls + his answer ("They ask me"). `fx-discovery`: `{trigger,response}[]` — his provocative/discovery questions to ASK prospects + optional "what it surfaces" note ("I ask them"); both share the Questions tab as two sections. Seeded from DEFAULT_SCRIPT / DEFAULT_OBJECTIONS / DEFAULT_QUESTIONS / DEFAULT_DISCOVERY, edited in-app (no deploy needed), live in the Hunt tab behind a `[Tallies | Script | Objections | Questions]` toggle (`huntView`). A one-time load migration relocates client-questions mis-seeded into `fx-objections` by v1.2.6 over to `fx-questions`.
- NEVER break backward compatibility with existing stored days. Merge-with-defaults on load (see top of script).

## Methodology constants (source of truth)

**Cold call Gauntlet (11 steps, Dennehy, from Corbin's notebook):** Pattern interrupt → 30-second pitch (3 problems) → Push-away #1 → Is it important? → Push-away #2 → Emotional grinder → Crabwalk check → Conversational questioning → Vampire invite → Last chance to bail → Ask for a referral. Steps 1-10 verified from the notebook; step 11 (referral) added by Corbin. The in-app "What each step means" glossary hints (STEP_HINTS / MEETING_HINTS) are plain-language paraphrase, not verified Dennehy wording; Corbin can correct any.

**Meeting anatomy (10 steps):** Omega → Mock presentation → Future state → Pain discovery questions → Five pointers → Selling the proposal → The 3.5 alternatives → Next steps → Pulling the pin → Ending the meeting. ⚠️ ORDER IS A RECONSTRUCTION from notebook fragments, not verified against Dennehy's teaching. Corbin will confirm/correct after a Coaching Club session. When he does, update MEETING_STEPS.

**Five pointers (meeting qualification gates):** Problems we fix / They accept it's a problem / They convinced ME (direction matters: prospect convinces the seller) / Can afford it / How they decide.

**Bosses (why FX deals die, from Corbin's notebook):** Loyalty Wraith ("loyal to our bank"), Priority Phantom ("not a priority"), Price Ogre ("you're more expensive"), Document Dodger (won't send docs), The Ghost (booked then vanished).

**Three sins:** crabwalk, begging, "I"-focused.

**Five drivers + permissions (Meininger):** Hurry Up→take my time · Be Perfect→make mistakes · Please Me→rely on myself (this one is "THE BEGGING + CRABWALK ENGINE") · Try Hard→finish + succeed · Be Strong→feel + be open.

**Natural Child productive state (all seven at once):** Relaxed, Comfortable, Alert, Eager, Energized, Excited, Powerful.

**XP legend:** dial 1 · DM convo 10 · pitch 10 · grinder 15 · pin pulled 15 · meeting-no-beg 20 · each NO 1 · 10 NOs in a row 30 · card filed 5 · full anatomy on a card 25 · boss grinded 20 · Dawn complete 10 · quest done 15 · all-3-quests bonus 25 · powerless cycle broken 20.

**Ranks:** Squire of the Spread 0 · Apprentice Headsman 500 · Pin Puller 1,500 · Monster Hunter 3,000 · Vampire Slayer 5,000 · Grinder of Souls 8,000 · The Executioner's Hand 12,000 · THE FOREXECUTIONER 20,000.

## Product rules (design intent — protect these)

- A card CANNOT be filed without "The One Thing" (one behaviour for the very next dial). This gate is the point of the card.
- The Tape Check must NEVER gate dialing. The microcopy "Tape unclear? Write 'unclear', grant a permission, dial" stays.
- Tape Check prompts for the because-clause, not the feeling ("I don't want to dial" is a feeling report; the tape is the sentence after it).
- Island score is displayed as a fixed 9, not editable. Roles can fail; the person can't.
- Low-day close exists so a bad day can be closed in one tap and keep the streak. "A closed bad day beats an unclosed good one. The streak measures showing up to the mirror, not performance." Corbin has MDD + ADHD (medicated, doing well) — this feature is load-bearing. Never add friction to it.
- Never add XP for outcomes. Never add features that reward talking to Claude/the app over dialing.

## Change policy

Corbin's documented pattern (his words, self-acknowledged): building excellent systems as productive avoidance of dialing. Standing agreement: batch updates every ~2 weeks max; resist feature creep; new features require lived friction from actual use, not anticipated friction. If he asks for a feature mid-outbound-block, the right answer is "noted for the batch, back on the phones." Current gate: 3+ cards filed in the first 5 real days, or the conversation is about avoidance, not tooling.

## Design tokens

bg #17151A · surface #201D24 · surface2 #2A2630 · line #3A3542 · ink #EDE6DA (aged parchment, not white) · muted #8E8797 · crimson #C22A3A (actions/XP only) · crimsonDim #7E1F2B · gold #D9A441 (rank/streak) · green #5FA36A. Display font: Futura-CondensedExtraBold stack (iOS-native poster feel, no webfonts). Dark, "executioner's ledger" aesthetic. Copy voice: Corbin's — punchy, no corporate language, NO em dashes ever, no "not X but Y" constructions.

## Current state

v1.2.4 deployed. Feature-complete for cold calls + meetings. Recent clarity passes: guided Tape Check with driver tells + binary-quest coaching + numbered Dusk quests (v1.2.2); Card clarity pass — mark legend, "what each step means" glossary, boss-fight split into two questions, referral step 11, "where I ran the call from" field (v1.2.3); spacing fix under labels/subs (v1.2.4); on-card driver guide — a "What do these mean?" expander under the card's driver field showing the full table (command it obeys / how it sounds on a dial / antidote) via DRIVER_GUIDE (v1.2.5); Playbook in the Hunt tab — editable Script (mirrors the 11 gauntlet steps) + Objection wall ({trigger,response} list), behind a Tallies/Script/Objections toggle, seeded and stored in fx-script / fx-objections (v1.2.6); split the wall into separate Objections (real pushbacks, seeded with his 7 + classics minus "too expensive") and Questions (client-questions) tabs, one-time fx-objections→fx-questions migration (v1.2.7); added "I ask them" discovery-questions section to the Questions tab (fx-discovery), seeded with FX provocative questions (v1.2.8); put the two Questions sections behind a "They ask me / I ask them" sub-toggle (`qView`) so only one shows at a time (v1.2.9); app-wide field auto-grow — all free-text `<input>`s converted to `<textarea>` (values moved to content), a global `autoGrow()` sizes every textarea to its content on render + on input, so long text never clips (only the short `firstDial` stays an input) (v1.2.10); pinned an assertive Ben-voice gatekeeper comeback ("What's this regarding?") to the top of the Objections wall, with a one-time `fx-obj-gk`-flagged append migration so existing users get it once (v1.2.11). Also added `docs/method-and-psychology.md` (full Dennehy × Meininger method + app integration). Known open items: (1) meeting step order + gauntlet glossary hints pending Ben verification, (2) possible future: low-day Dusk variant, day-detail polish, mirror the on-card driver guide into Dawn's tape-check driver field. All wait for lived friction.
