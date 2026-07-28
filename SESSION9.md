# Session 9 — Revisit round: dice roller, accuracy audit, party.html

The Round-1 roadmap is content-complete (Sessions 1–8). This is the
promised revisit round, scheduled after the DM signs off on the Session
6–8 design. **Engine changes are allowed this session** (unlike the
Session 4/5/8 data floods) — but keep each task self-contained and
re-validate after every change.

Read first: `PLAN.md`, `BUILD_STATUS.md`, `DATA_TEMPLATE.md`. Check
`feedback/` for a `BUGS.md` or DM notes before starting.

## Validator gate (every commit)

Static checks (516+) AND the coverage sweep (0 failures) at
http://localhost:8420/validate.html — server:
`powershell -NoProfile -ExecutionPolicy Bypass -File .claude/serve.ps1`.
Any new engine code must keep the sweep at 0 failures. Any rules content
follows the Session 4/8 accuracy rules (never invent a mechanic or number;
list low-confidence skips in the commit message).

## Task 1 — In-app dice roller

Today the sheet only does tap-to-roll "d20 + mod" flashes on saves/skills/
initiative/attack rows (Session 3); the DM says real rolls still happen
off-site. Build a proper roller that keeps a character self-contained:

- A shared `rollDice(expr)` helper (`"2d6+3"`, `"1d20"`, keep/drop, adv/dis)
  returning `{total, dice:[...], expr}` — pure, unit-checkable.
- Wire the existing roll affordances through it and show the individual
  dice, not just the sum. Attack rows roll to-hit AND damage (crit doubles
  damage dice). Spell cards get a roll button for their damage die where
  one is present. Hit-dice healing and death saves roll too.
- Advantage/disadvantage toggle (or modifier key) on d20 rolls.
- A small **roll log** panel (last ~20 rolls, expression + breakdown +
  result), cleared per session, not exported. Reuse the card/pip styling;
  no new dependencies (inline only, offline-forever constraint holds).
- Cast buttons already spend slots (Session 3) — have them optionally emit
  the damage roll for the spell.

Scope guard: no networked/shared rolling, no 3D dice — this is a local,
offline roller the whole table can read off one screen.

## Task 2 — Targeted book-accuracy audit

Re-verify the data entered across Sessions 2–8 against the 2014 books,
producing `feedback/BUGS.md` (a checklist the next session can burn down).
Priorities:

- The Session 8 compendium class tags — re-check the deliberately
  under-tagged / skipped rows (Warding Wind bard, Nathair's Mischief,
  Immovable Object, Wristpocket, Kinetic Jaunt, the TCE 4th-level summon-X
  spells) and confirm no invented tags slipped in.
- BOMT content still parked (Rewarded/Ruined backgrounds, Cartomancer
  feat) — enter from the book if it's now available, else leave the note.
- Subclass features that are milestone-text only (e.g. Draconic
  Bloodline's 13+Dex AC, Psi Warrior / EK / Arcane Trickster resource
  tracking) — list which are worth making mechanical (see future ideas).
- Spot-check a handful of ABILITIES numbers (uses/dice) against the PHB.

Deliverable is the audit file + any low-risk fixes; big mechanical gaps
become future-ideas entries, not scope creep here.

## Task 3 — party.html — DM roster page

A standalone, dependency-free page (same no-build ethos as `index.html`)
that reads exported character files and shows a party-at-a-glance roster
for the DM.

- Input: a multi-file picker. Accept both export formats — `exportJson()`
  `.json` files (parse directly) and `exportCharacterHtml()` `.html` files
  (extract the `var BAKED_STATE={…}; // EXPORT_BAKED_STATE_MARKER` blob by
  regex). The `tts` field is already scrubbed from both.
- Render one roster card per character: name, species, the multiclass
  string from `build.classes[]`, level, HP, AC, passive Perception, key
  saves, spell save DC / attack, and current conditions/notes.
- Keep it read-only (a DM view, not an editor). Persist the last-loaded set
  in its own `localStorage` key so a refresh keeps the party.
- Reuse the grimoire theme CSS variables so it matches the sheets.
- Note: party.html can't `import` index.html's derivation functions — show
  the stored/derived values already baked into the state; only recompute
  what's cheap and unambiguous. Don't fork the whole engine.

## Task 4 — Docs + ship

- Update `BUILD_STATUS.md` (Session 9 section) and `PLAN.md` (mark Session
  9 done; note what moved to future ideas).
- Fresh-load spot checks: roll a spell's damage and an attack from a real
  character; load two exported files into party.html and confirm the
  roster renders.
- Commit, push, verify the live Pages site, and `git rm SESSION9.md` in the
  final commit.

## Scope guard

Three independent deliverables — if the dice roller balloons, ship it
alone and split the audit / party.html into a Session 10. Anything that
needs more than a session goes to PLAN.md "future ideas," including the
**spell-detail enrichment** (full casting time / range / components /
duration / description on every compendium spell) — that is its own data
flood, not part of this session.
