# Playbook: Script + Objection Handler (in Hunt)

Design approved 2026-07-07. Ships in v1.2.6.

## Purpose
A carry-in-pocket cold-call playbook Corbin reads one-handed during live blocks and consults in call reviews: his gauntlet script and a growing objection/stall wall. Content is still evolving (especially the 30-second pitch), so it must be editable on the phone with no deploy.

## Placement
Inside the **Hunt** tab (where he already is during a block), via a segmented toggle at the top: `[ Tallies | Script | Objections ]`. Bottom tabs stay 5. Default view = Tallies. Toggle state is a session var (`huntView`), not persisted — always opens on Tallies.

## Script (editable, `fx-script`)
Mirrors the 11 Gauntlet `STEPS`, so what he reads == what he scores on the Card. Each step = a label + an editable `<textarea>` of his words. Stored as an array of 11 strings, seeded from his brain-dump (opener, the "they're [feeling] because [problem]" x3 pitch scaffold, push-aways, the emotional-grinder question sequence + past/present/future cost, vampire invite, last chance, referral line). Freeform text so he can rewrite/experiment.

## Objections (editable list, `fx-objections`)
Ben's sticky-note wall: an array of `{trigger, response}` entries, each rendered as two textareas (what they say / your response — answer with a question) plus a Delete. An "Add objection" button appends a blank entry so he can capture a new stall the moment it happens. Seeded with his starter client-question list (how do you make money, is it safe/regulated, why haven't I heard of you, strategies, documents, hiccups, timelines, is it easy).

## Data model
New localStorage keys `fx-script` (string[11]) and `fx-objections` (`{trigger,response}[]`). Independent of day/card/meta — fully backward compatible. Render guards missing indices (`script[i]||""`).

## Edit behavior
Textarea edits save on `onchange` (blur) via `persistScript` / `persistObjections` without re-render (keeps focus), matching the app's existing input pattern. Add/delete/toggle call `render()`. Delete asks `confirm()` to avoid losing notes on a mis-tap.

## Non-goals (v1)
Multiple saved pitch variants, a rate/cost calculator, XP for reading the script. Revisit only on lived friction.
