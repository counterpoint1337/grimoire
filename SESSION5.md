# Session 5 — DLC Wave 2 (Wildemount / Strixhaven / Fizban's / Book of Many
# Things / MPMM species) + polish. Final planned data session.

Pure data-entry session. The engine is finished; work only inside the
`DATA:` marker blocks. Read `PLAN.md`, `BUILD_STATUS.md`,
`DATA_TEMPLATE.md` first. Check `feedback/` for a `BUGS.md`.

## LESSON FROM SESSION 4 — read before writing a single entry

Session 4's cleanup pass found that **10 of 15 magic items and 11 subclass
descriptions had invented mechanics** written from memory, plus half-feats
missing their ability increases. The rules now:

1. **Never invent a mechanic.** If you are not certain of an entry's
   numbers/mechanics, write the flavor and citation WITHOUT mechanical
   specifics. A desc like "Grants minor watery boons while attuned (see
   book)" is correct; a confabulated "+2 to swim checks" is a bug.
2. **Structured `effects` only where you are confident** the item maps to
   the supported kinds. When unsure, use prose `mech`/`desc`.
3. **Half-feats:** double-check every XGE/TCE/FTD-style feat for its +1
   ability increase (`abilityChoiceFrom`) — this was missed 3 times.
4. **Self-review pass is mandatory:** after data entry, re-read every new
   entry asking "do I actually know this, or did I pattern-fill?" Flag
   low-confidence entries with a `desc` that stays vague rather than
   specific, and list them in the commit message.
5. The validator gained two guards from your last session's bugs (effects
   require a wield/wear slot; COMP must stay level-sorted). It now runs
   400 static checks — all must pass, plus the coverage sweep at 0
   failures, before every commit.

## Task 1 — MPMM species (~10 entries) — HIGHEST PRIORITY

The table's planned party needs **Aarakocra, Firbolg, and Centaur** — the
party literally cannot be created until these exist. Add them plus a
sensible MPMM set: Aarakocra, Centaur, Firbolg, Goliath, Kenku, Tabaxi,
Tortle, and Genasi (Air), Genasi (Fire), Genasi (Water) to match the
existing Genasi (Earth).

- Follow the Genasi (Earth) entry as the pattern (it is already
  MPMM-sourced): traits as short original summaries, `grants` for innate
  spells, `languages`, MPMM page cites.
- Every entry MUST get a `DATA:SPECIESASI` entry — MPMM uses flexible
  ASIs: `{choose:[2,1]}`.
- Flight (Aarakocra), Powerful Build, swim/climb speeds etc. are trait
  text (the engine only auto-applies `speed`, languages, and grants).
- The centaur "homunculus" homebrew is per-character flavor a player types
  on their own sheet — do NOT put it in species data.

## Task 2 — Subclasses (~4 entries)

- **Wizard (EGW):** Chronurgy Magic, Graviturgy Magic (chooseAtLevel 2)
- **Ranger (FTD):** Drakewarden · **Monk (FTD):** Way of the Ascendant
  Dragon (chooseAtLevel 3)
One-line original descs; note "(track manually)" only where a subclass
grants resources the engine doesn't model.

## Task 3 — Backgrounds (~7 entries)

- **SCC:** Lorehold Student, Prismari Student, Quandrix Student,
  Silverquill Student, Witherbloom Student (skills/tools/languages per
  book; the campus flavor goes in the `feature`).
- **Book of Many Things:** Rewarded, Ruined.
Every background MUST get a `DATA:BGPERSONA` entry (2 short originals per
category) — the sweep enforces this.

## Task 4 — Feats (~8 entries)

- **SCC:** Strixhaven Initiate, Strixhaven Mascot
- **FTD:** Gift of the Chromatic Dragon, Gift of the Metallic Dragon,
  Gift of the Gem Dragon
- **BOMT:** Cartomancer
- **EGW:** Hollow One is a supernatural gift, not a feat — skip it.
Remember rule 3 above: check which of these carry +1 ability increases
(the FTD Gems/Metallic/Chromatic trio does not; verify each).

## Task 5 — Spells to COMP (~10 entries)

Only wizard-list spells missing from COMP, inserted **in their level run
alphabetically** (the validator now fails out-of-order entries):
- FTD: Ashardalon's Stride, Nathair's Mischief, Raulothim's Psychic Lance,
  Summon Draconic Spirit (druid/sorc/wiz — verify wizard access),
  Draconic Transformation.
- EGW dunamancy: check which of Immovable Object, Magnify Gravity, Pulse
  Wave, Gravity Sinkhole, Temporal Shunt, Gravity Fissure, Ravenous Void,
  Time Ravage, Dark Star, Tether Essence are already present; add the
  missing ones with "(EGW)" tags.

## Task 6 — Polish pass

- Update BUILD_STATUS.md coverage table and PLAN.md (mark Sessions 4-5
  done; move anything unbuilt into a "Future ideas" list: class-scoped
  spell compendiums, EK/AT subclass slots, feat/ASI guided level-up step,
  screenshot-capture investigation).
- Grep for doc rot: any remaining "Session 4"/"next session" phrasing in
  docs, stale counts, duplicated headings.
- Spot-check in the browser: create a character with a new species via
  the builder; select each new background; confirm new subclasses appear
  in the Level Up chooser at the right level; search two new entries in
  the Notes encyclopedia.

## Verification gate (before every commit)

1. `powershell -NoProfile -ExecutionPolicy Bypass -File .claude/serve.ps1`
   → http://localhost:8420
2. validate.html → static checks (400+) all pass → "Run coverage sweep"
   → 0 failures.
3. Fresh index.html load: no console errors.
4. Commit, push, `curl` the live Pages site for one new key.
5. Final commit: `git rm SESSION5.md` — PLAN.md carries the record.

## Scope guard

Engine files/functions are **read-only**. If a task seems to need engine
code, write it into PLAN.md's Future ideas instead and move on. Do not
reorder or reformat existing entries except where a validator check
requires an insertion point.
