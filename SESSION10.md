# Session 10 — Spell-prep polish (3 playtest changes)

Builds on Session 9's prepared-spell workflow + dice roller. **Engine
changes allowed.** Independent of the still-open Session 9 pt 3 (party.html)
— do either first.

Validator gate before every commit: static checks (516+) AND the coverage
sweep (0 failures) at http://localhost:8420/validate.html. The sweep drives
`buildSpellbook`/card rendering across 1,345 builds, so the Cast-gating and
card-composition changes below must keep it green. **Front the validator
tab before the sweep** (a hidden/background tab throttles its timer loop and
the sweep appears to stall — a tooling quirk, not a failure).

Rules baseline stays 2014. Accuracy rules (Session 4/8) apply to any new
numbers (cantrips-known counts below are 2014 PHB).

---

## Change 1 — Builder: block creation until spell/cantrip counts are legal

**Problem.** The builder's Spells step (`bwRenderSpellList`, step 6) lets you
pick unlimited spells, and `bwCreate()` only soft-confirms
("Create anyway?"). A player can finish a cleric/wizard with the wrong
number of cantrips/spells and hand the DM an illegal sheet.

**Wanted.** Keep multi-select (over-picking in the UI is fine), but do NOT
allow creation until the **exact** legal counts of cantrips and level-1
spells are chosen (wizard = spellbook picks; prepared classes = the spells
they prepare; known classes = spells known).

**Data to add.** There is no cantrips-known data in the app today. Add a
per-class level-1 cantrip count — cleanest as a general `cantripsByLevel`
(21-entry array) on each caster's `spellcasting`, or a lightweight
`CANTRIPS_L1` map if scope must stay tiny. 2014 level-1 cantrips known:
bard 2, cleric 3, druid 2, sorcerer 4, warlock 2, wizard 3, ranger 0,
paladin 0. (Wire the general table into the milestone "Cantrips" column too
if you add the full version.)

**Level-1 leveled-spell target per class (level 1):**
- Wizard: **6** spellbook spells (picks = spellbook). Preparing is separate
  and happens post-creation.
- Bard/Sorcerer/Warlock: `spellcasting.knownByLevel[1]` (bard 4, sorc 2,
  warlock 2).
- Cleric/Druid: **prepared = casting-mod + 1** (use `bwFinalScores()` for
  the mod). Picks = the prepared set.
- Paladin/Ranger: **0** at level 1 (spells start at level 2).

**Build steps.**
1. Split `BW.spellPicks` by level via `COMP[i][1]` (0 = cantrip, 1 =
   leveled). Add two live counters to the Spells step
   (`bwStepHtml` s===6 and `bwRenderSpellList`): "Cantrips X / need",
   "Level-1 Y / need" (label "prepared"/"known"/"in spellbook" by class).
2. In `bwIssues()` (returns `[stepIndex, message]`) add step-6 issues when
   `cantripCount !== need` or `leveledCount !== need` — distinct messages
   for over ("remove N") vs under ("choose N more").
3. **Hard gate** in `bwCreate()`: today all issues are one soft `confirm`.
   Split out a `bwHardIssues()` (the spell-count ones) and `alert()` +
   `return` if any are present — no "create anyway" for these. Other issues
   keep the existing soft-confirm.
4. On create, route picks correctly (ties into Change 2): prepared-class
   leveled picks → `S.prepared` (and add the cards); cantrips → the book;
   wizard leveled picks → spellbook (`S.custom`) as today.

**Touch:** `bwRenderSpellList`, `bwStepHtml` (step 6 copy + counters),
`bwIssues`, `bwCreate`, new cantrip-count data. **Edge:** need=0
(paladin/ranger) → show "no spells at level 1", no gate.

---

## Change 2 — Prepare-then-cast, generalized to ALL preparing casters

**Problem (see the cleric screenshot).** A cleric shows only the handful of
manually-added cards, and every card has a Cast button regardless of
prepared state. This isn't a cleric-only fix — **every class that prepares
spells should work the same way**: its full available pool is browsable,
and only **prepared** spells are castable. The only thing that differs by
class is *what the available pool is*.

**Classify casters** (add a `spellcasting.prepareFrom` flag, or derive from
`preparedFormula` vs `preparedByLevel`):
- **Whole-list preparers** — cleric, druid, paladin. Pool = their **entire
  class list**; prepare a subset; cast only prepared.
- **Spellbook preparer** — wizard. Same prepare-then-cast mechanic, but the
  pool = the **scribed spellbook** (`S.custom` + base), not the whole
  wizard list; prepare a subset of the book; cast only prepared.
- **Known casters** — bard/sorcerer/ranger/warlock. No preparation: their
  whole *known* list is directly castable. No prepared bar, no Cast gate —
  unchanged.

**One shared model for the two preparer groups.** Cards shown on the Spells
tab = cantrips + species/feat grants + **prepared** leveled spells (the
castable set); don't render the whole pool as cards. The difference is only
the "available pool" browser:
- Whole-list preparers → a **"Prepare from your <class> list"** browser
  (reuse `buildComp` scoped to the class + Session 9 Prepare toggles), or a
  jump to the class-filtered Level-Up compendium.
- Wizard → the browser IS the spellbook (scribed cards); the Level-Up
  compendium is where new spells get scribed into it, then prepared.

Drive this off the classification so a single code path serves cleric,
druid, paladin, and wizard — not a cleric special case.

**Cast gating (every preparer — cleric/druid/paladin/wizard).**
- The Cast button only appears/acts for: cantrips, ritual-tagged spells with
  a ritual feature, species/feat grants, and **prepared** leveled spells.
- Unprepared leveled cards → "Prepare to cast" instead of Cast; the cast
  click handler must refuse an unprepared leveled spell (guard in the
  `data-cast` branch, ~line 3266).
- Known casters are exempt from this gate (all known spells stay castable).
- Optional: make the top **Prepared bar** the cast surface — add a Cast
  affordance to each prepared chip (the "cast from the prepared bar" idea).

**Touch:** `spellcasting` configs (add `prepareFrom`), `renderBaseCard` /
`renderCustomCard` (Cast vs "Prepare to cast", gated by the classification),
`buildSpellbook` (card composition per caster type), the `data-cast`
handler (block unprepared for preparers), a Spells-tab prepare browser.
**Edge/out-of-scope:** always-prepared domain/Circle-of-the-Land spells —
note as future, don't build now.

---

## Change 3 — Casting: prominent, centered damage roll

**Problem.** Cast writes only a status line; damage isn't rolled on cast,
and the roll flash (`#rollFlash`) is a small bottom-right chip.

**Plan.**
- Add a **centered roll overlay** (`#rollCenter`): screen-centered, large,
  themed, auto-dismiss ~2.5s or tap-to-dismiss. New CSS + a
  `logRollCentered(html)` helper (parallel to `logRoll`; still push to
  `ROLL_LOG`).
- In the `data-cast` handler, after spending the slot, parse the spell's
  damage die (reuse `spellDmgBtn`'s `\d+d\d+` scan over `m`+`desc`) and, if
  present, roll it (`rollDice`) into the centered overlay; non-damage spells
  show a centered "Cast — <name> (<tier> level)" confirmation.
- Keep the corner flash + roll log for tap-to-roll; centered display is for
  casts. (Optional toggle to always center.)

**Touch:** CSS (`#rollCenter`), the cast handler (~3266–3290), reuse
`rollDice`/`dmgHtml`. **Edge:** upcast scaling — v1 rolls the base die; note
tier-scaled damage as a nice-to-have (the cast tier is in `data-castl`).

---

## Suggested order & ship

1. **Change 3** (self-contained, quick win).
2. **Change 1** (builder gate; needs the cantrip-count data).
3. **Change 2** (largest; consumes Change 1's routing and the caster
   classification).

Validate (516+ static, sweep 1,345/0) and browser-verify after each:
- builder refuses a cleric/wizard with wrong cantrip/level-1 counts;
- cleric Spells tab shows the whole list to prepare from and Cast works only
  on prepared spells;
- casting a damage spell shows the centered roll.

Commit per change; `git rm SESSION10.md` in the final commit.
