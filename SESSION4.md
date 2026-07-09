# Session 4 — DLC Wave 1: Xanathar's Guide + Tasha's Cauldron (data only)

You are executing a **pure data-entry session**. The engine is finished and
sweep-tested (756 builds, 0 failures). Your job is to extend the data
blocks in `index.html` following `DATA_TEMPLATE.md` exactly, validate, and
ship. **Do not modify any engine code** — nothing outside the
`/* DATA:X:START */ ... /* DATA:X:END */` marker blocks except where this
brief explicitly says so.

Read first: `PLAN.md`, `BUILD_STATUS.md`, `DATA_TEMPLATE.md`. Check
`feedback/` for a `BUGS.md` (it has been promised repeatedly and has never
existed; if it appears, fix its contents as task zero).

## Ground rules

- 5e **2014** rules era. All body text = **short original summaries with
  book + page citations. Never verbatim book text.**
- Match the existing entries' style, length, and field usage precisely —
  open the current data blocks and imitate them.
- Keys are kebab-case, unique, and permanent (character files reference
  them). Subclass keys need only be unique within their class.
- After each major block: run the validator (see "Verification" below).

## Task 1 — XGE + TCE subclasses (~57 entries)

Append to each class's `subclasses` array inside `DATA:CLASSES`. One-line
original `desc` per entry, `chooseAtLevel` matching the class's existing
pattern (cleric/sorcerer/warlock 1, wizard 2, everyone else 3). Cite pages
in the desc only if natural — the class `source` field stays as-is.

- **Barbarian** — XGE: Ancestral Guardian, Storm Herald, Zealot · TCE:
  Beast, Wild Magic
- **Bard** — XGE: Glamour, Swords, Whispers · TCE: Creation, Eloquence
- **Cleric** — XGE: Forge, Grave · TCE: Order, Peace, Twilight
- **Druid** — XGE: Dreams, Shepherd · TCE: Spores, Stars, Wildfire
- **Fighter** — XGE: Arcane Archer, Cavalier, Samurai · TCE: Psi Warrior,
  Rune Knight
- **Monk** — XGE: Drunken Master, Kensei, Sun Soul · TCE: Mercy,
  Astral Self
- **Paladin** — XGE: Conquest, Redemption · TCE: Glory, Watchers
- **Ranger** — XGE: Gloom Stalker, Horizon Walker, Monster Slayer · TCE:
  Fey Wanderer, Swarmkeeper
- **Rogue** — XGE: Inquisitive, Mastermind, Scout, Swashbuckler · TCE:
  Phantom, Soulknife
- **Sorcerer** — XGE: Divine Soul, Shadow Magic, Storm Sorcery · TCE:
  Aberrant Mind, Clockwork Soul
- **Warlock** — XGE: Celestial, Hexblade · TCE: Fathomless, The Genie
- **Wizard** — XGE: War Magic · TCE: Bladesinging, Order of Scribes

Note for third-caster or spell-granting subclasses: milestone-text only,
same as Eldritch Knight's existing entry — add the "(track slots
manually)" note where applicable.

## Task 2 — XGE + TCE feats (~30 entries)

Append to `DATA:FEATS`. Use the existing feat fields: `textOnly`,
`abilityChoiceFrom` (half-feats), `grants` (spell-granting feats — grant
the spell as a card like Telekinetic does). Telekinetic already exists —
do not duplicate it.

- **TCE:** Artificer Initiate, Chef, Crusher, Eldritch Adept, Fey Touched,
  Fighting Initiate, Gunner, Metamagic Adept, Piercer, Poisoner, Shadow
  Touched, Skill Expert, Slasher, Telepathic
- **XGE racial feats:** Bountiful Luck, Dragon Fear, Dragon Hide, Drow
  High Magic, Dwarven Fortitude, Elven Accuracy, Fade Away, Fey
  Teleportation, Flames of Phlegethos, Infernal Constitution, Keen Mind
  is PHB (skip), Orcish Fury, Prodigy, Second Chance, Squat Nimbleness,
  Wood Elf Magic. Note the species prerequisite in the `desc` ("Prerequisite:
  halfling.").

## Task 3 — Custom Lineage (TCE p.8) as a species entry

One new `DATA:SPECIES` entry (`custom-lineage`): Medium/Small (note the
choice in a trait), speed 30, one trait for the +2 ASI, darkvision-or-skill
choice as a trait note, one feat at 1st level (trait note pointing at the
Feats section), one extra language. Add matching entries to
`DATA:SPECIESASI` (`{choose:[2]}`) and species `choices` (language). The
coverage sweep will fail if `SPECIES_ASI` is missing — that is your signal
it's wired right.

## Task 4 — Spells to the compendium (~40 entries)

`DATA:COMPENDIUM` (`COMP`) is the **wizard/arcane list**. Add the XGE + TCE
wizard-list spells that are missing (check `inBook`-style by searching the
file for the name first — many XGE spells are already present). Use the
existing 4-element row format and ~50-char summaries with "(XGE)"/"(TCE)"
suffixes like the existing rows. Do NOT add non-wizard-list spells —
class-scoped lists are Session 5+.

## Task 5 — Common magic items (~15 entries)

Append to `DATA:ITEMS` before the closing marker, after the existing DMG
magic items: XGE common magic items that suit any table (e.g. Cast-Off
Armor, Cloak of Billowing, Clothes of Mending, Dread Helm, Ear Horn of
Hearing, Enduring Spellbook, Hat of Vermin, Heward's Handy Spice Pouch,
Instrument of Illusions, Mystery Key, Perfume of Bewitching, Pole of
Collapsing, Ruby of the War Mage, Staff of Adornment, Wand of Smiles).
Type `wondrous` (or fitting), `slot` where wearable, `attunement` only
where the book requires it, structured `effects` only where they map to
the supported kinds — otherwise flavor `mech`/`desc` text is correct.

## Verification (mandatory before every commit)

1. Start the local server: it's configured in `.claude/launch.json`
   (`powershell -NoProfile -ExecutionPolicy Bypass -File .claude/serve.ps1`
   serves the folder on http://localhost:8420).
2. Open `http://localhost:8420/validate.html` → "Try loading ./index.html"
   → **all static checks must pass** (355+ and growing with your data).
3. Click **"Run coverage sweep"** → must report **0 failures**. The sweep
   automatically exercises every subclass you added at level 20 and the
   new species at 4 level bands.
4. Load `http://localhost:8420/index.html` fresh: no console errors; spot
   check one new subclass via Level Up → + to its pick level; one new feat
   via the Feats section; Custom Lineage via the New Character builder.
5. Commit with a descriptive message, push, verify the live site picked it
   up (`curl` the Pages URL and grep for one of your new keys).

## Scope guard

- Engine files/functions: **read-only.** If something seems to require an
  engine change, stop and note it in BUILD_STATUS.md's "Known cuts"
  instead of changing code.
- Do not rename keys, reorder existing entries, or reformat untouched
  lines (keeps diffs reviewable).
- Update `BUILD_STATUS.md` data-coverage table and `PLAN.md` (mark Session
  4 done, Session 5 next) at the end.
- When done, delete this file (`git rm SESSION4.md`) in the final commit —
  PLAN.md carries the record.
