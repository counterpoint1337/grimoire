# Session 8 — Class spell & ability data flood (pure data entry)

The Session 7 engine is live: the compendium filters by class tags, the
per-class page renders ABILITIES cards, and the builder's Spells step
filters to the chosen class. Your job is to flood the data those systems
read. **No engine changes** — work only inside the `DATA:` marker blocks.

Read first: `PLAN.md`, `BUILD_STATUS.md`, `DATA_TEMPLATE.md` (the
Compendium class-tags and Class ability sections are new). Check
`feedback/` for a `BUGS.md`.

## ACCURACY RULES (hard requirements — Session 4's lesson)

1. **Never invent a mechanic or a class tag.** Tag a spell for a class
   only when you are certain it's on that class's 2014 list. Untagged =
   wizard-only; under-tagging is recoverable, invented tags are bugs.
2. For abilities, when unsure of a number (uses, die size), write the
   effect without the number rather than guessing.
3. Mandatory self-review pass: re-read every entry asking "do I know
   this, or did I pattern-fill?" List low-confidence skips in the commit
   message.
4. Validator gate before every commit: static checks (435+) AND the
   coverage sweep (0 failures) at http://localhost:8420/validate.html
   (server: `powershell -NoProfile -ExecutionPolicy Bypass -File
   .claude/serve.ps1`).

## Task 1 — Retag the existing ~321 COMP rows

Add the 5th class-tags element to every row you can tag confidently
("brd clr drd pal rgr sor wlk wiz"; see DATA_TEMPLATE.md). Every existing
row is on the wizard list, so every tag string you write must include
`wiz`. XGE/TCE/EGW/FTD spells have their own class lists — check each.
Leave a row untagged rather than guessing (defaults to wiz).

## Task 2 — Add the missing non-wizard 2014 PHB spells

The other classes' PHB lists, inserted **alphabetically within their
level runs** (validator enforces level order), tagged with their classes
and NOT `wiz` unless genuinely shared. Priorities, in order:

1. **Cleric list** (the party has a tempest cleric): Bless, Command, Cure
   Wounds, Guiding Bolt, Healing Word, Sanctuary, Shield of Faith,
   Spiritual Weapon, Prayer of Healing, Lesser/Greater Restoration,
   Revivify, Spirit Guardians, Death Ward, Guardian of Faith, Flame
   Strike, Raise Dead, Heal, Harm... the full PHB cleric list.
2. **Druid list** (firbolg druid): Entangle, Goodberry, Faerie Fire,
   Thunderwave (already present — tag it), Moonbeam, Barkskin, Call
   Lightning, Conjure Animals, Grasping Vine, Wall of Thorns... full list.
3. **Bard/Paladin/Ranger/Sorcerer/Warlock** lists (many overlap spells
   already present — tag those; add the missing like Divine Favor,
   Hunter's Mark, Hex, Eldritch Blast, Compelled Duel, Ensnaring Strike).
4. Cantrips for all classes (Guidance, Druidcraft, Sacred Flame,
   Thorn Whip, Vicious Mockery, Eldritch Blast...).

Keep summaries in the existing style: lowercase, ~50 chars, "(XGE)"-style
book tags where non-PHB.

## Task 3 — Flood DATA:ABILITIES

Per the schema in DATA_TEMPLATE.md, using the 13 seed entries as style
guides. Target sets (2014 PHB):

- **Battle Master maneuvers** (all 16, subclassKey "battle-master").
- **Eldritch Invocations** (warlock, the PHB set — level = warlock level
  prerequisite, or omit level for level-2 baseline picks; note pact
  prerequisites in the desc).
- **Metamagic options** (sorcerer, all 8, level 3).
- **Per-class core actives** the page should show: Bardic Inspiration,
  Song of Rest, Countercharm; Channel Divinity — Turn Undead, Divine
  Intervention; Wild Shape; Lay on Hands, Divine Sense, Divine Smite,
  Cleansing Touch; Favored Enemy, Natural Explorer, Primeval Awareness;
  Arcane Recovery; Font of Magic; Ki-Empowered Strikes, Deflect Missiles,
  Slow Fall; Evasion (monk + rogue), Uncanny Dodge (already seeded),
  Reliable Talent; Feral Instinct, Brutal Critical...
- Subclass signatures where confident (Tempest Cleric's Wrath of the
  Storm and Destructive Wrath — the party cleric will want these).

## Task 4 — Docs + ship

- Update BUILD_STATUS.md (counts, Session 8 section) and PLAN.md (mark
  Session 8 done; Session 9 next).
- Fresh-load browser spot checks: a cleric character's Spells page and
  "mine" compendium scope; a battle-master fighter's Attacks page shows
  maneuvers; the builder cleric Spells step now offers a real list (the
  "still being seeded" note auto-hides once ≥10 rows match).
- Commit, push, verify the live Pages site, and `git rm SESSION8.md` in
  the final commit.

## Scope guard

Engine files/functions are read-only. If something needs code, write it
into PLAN.md "Future ideas" and move on. Do not reorder existing entries
except where alphabetical insertion requires it.
