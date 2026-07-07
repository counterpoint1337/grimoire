# Data Entry Template

Strict shapes for the data types in `index.html`. Copy the relevant block
below, fill in the blanks, and paste it as a new array entry between the
matching `/* DATA:X:START */` and `/* DATA:X:END */` markers in
`index.html`. Then open `validate.html`, load `index.html`, and click
Validate before committing.

**Book content rule: everything you write is a short original summary in
your own words, with a book + page reference. Never paste text out of a
sourcebook.**

## Species — between `DATA:SPECIES:START` / `DATA:SPECIES:END`

```js
{
  key: "unique-lowercase-key",        // required, unique, kebab-case, never changes once used (character files reference it)
  name: "Display Name",               // required, e.g. "Dwarf (Hill)"
  source: "Book p.NNN",               // required — book + page
  size: "Medium",                     // "Small" | "Medium"
  speed: 30,                          // walking speed in feet
  traits: [                           // required array, may be empty
    {name: "Trait Name", desc: "Your short original summary of what it does."}
  ],
  languages: ["Common"],              // fixed languages granted, array of strings
  grants: [                           // optional — mechanical grants (e.g. an innate spell)
    {type: "spell", name: "Spell Name", level: 0, school: "Evocation", tag: "racial",
     desc: "Your short original summary.", source: "Book p.NNN"}
  ]
}
```

## Class — between `DATA:CLASSES:START` / `DATA:CLASSES:END`

```js
{
  key: "unique-lowercase-key",
  name: "Display Name",
  source: "Book p.NNN",
  hitDie: 8,                          // 6 | 8 | 10 | 12
  primaryAbility: ["STR"],            // array of ability codes: STR DEX CON INT WIS CHA
  savingThrows: ["STR", "CON"],       // exactly 2 ability codes
  spellcasting: null,                 // null for non-casters, or:
  // spellcasting: {ability:"INT", type:"prepared"|"known", ritual:true|false, focus:"an arcane focus",
  //   slotsByLevel: SOME_SLOTS_TABLE_VAR, preparedByLevel: SOME_PREP_TABLE_VAR},
  milestones: [                       // one entry per character level 1-20, [level, "short description"]
    [1, "Feature name(s) gained at this level"]
  ],
  subclasses: [
    {key: "unique-key", name: "Display Name", chooseAtLevel: 3, desc: "Your short original summary."}
  ]
}
```

If the class is a spellcaster, define its slot table and prepared-count
table as top-level vars first (see `SLOTS_WIZARD` / `PREP_WIZARD` for the
shape: an object keyed `1`-`20`, each value an array of slot counts per
spell level for slots, or a plain number per level for prepared count),
then reference those var names inside `spellcasting`.

## Background — between `DATA:BACKGROUNDS:START` / `DATA:BACKGROUNDS:END`

```js
{
  key: "unique-lowercase-key",
  name: "Display Name",
  source: "Book p.NNN",
  skillProf: ["Skill One", "Skill Two"],  // exactly the skills this background grants
  toolProf: [],                            // array of tool proficiency names, may be empty
  languageCount: 0,                        // number of bonus languages of the player's choice
  feature: {name: "Feature Name", desc: "Your short original summary."}
}
```

## Feat — between `DATA:FEATS:START` / `DATA:FEATS:END`

```js
{
  key: "unique-lowercase-key",
  name: "Display Name",
  source: "Book p.NNN",
  desc: "Your short original summary of the whole feat.",

  // Pick whichever of these apply — all optional, combine freely:
  grants: [                                // spells the feat grants outright
    {type: "spell", name: "Spell Name", level: 0, school: "Evocation", tag: "feat",
     desc: "Your short original summary.", source: "Book p.NNN"}
  ],
  abilityChoice: true,                     // feat grants +1 to ANY ability (max 20), prompts the player
  abilityChoiceFrom: ["INT","WIS","CHA"],  // instead of abilityChoice, restrict to specific abilities
  grantsSaveProf: true,                    // if present with an ability choice, also grants save proficiency in it
  hpBonusPerLevel: 2,                      // flat HP added per character level (e.g. Tough)
  textOnly: true                           // feat has no sheet-mutating effect yet; only logs to the Feats & ASI log
}
```

A feat can combine `grants` with `abilityChoice`/`abilityChoiceFrom` (e.g.
Telekinetic grants a spell AND an ability choice) — the engine applies both.

## Spell (seed spellbook) — between `DATA:SPELLS:START` / `DATA:SPELLS:END`

Only add here if the spell is something the class learns by leveling up
(not a species or feat grant — those go in that species'/feat's `grants`
array instead, see above).

```js
{n:"Spell Name", l:1, s:"School — flavor subtitle", m:"<b>Cast</b> 1 action · <b>Range</b> 30 ft · <b>V,S</b> · Instant",
 d:"Your short original summary of the effect.", src:"Book p.NNN"}
```

For a spell that scales with slot level, make `d` a function and set `up:true`:

```js
{n:"Spell Name", l:1, s:"School — flavor subtitle", m:"...", up:true,
 d:function(t){return "Your summary using "+t+" as the cast-at slot level.";}, src:"Book p.NNN"}
```

## Compendium entry (browsable "add spell" list) — between `DATA:COMPENDIUM:START` / `DATA:COMPENDIUM:END`

Flat 4-item array, matches the existing `COMP` rows:

```js
["Spell Name", 1, "Evoc", "one-line lowercase summary, under ~50 chars"]
```

School abbreviation must be one of: `Abj Conj Div Ench Evoc Illu Necro Trans`
(these map to full names via the `SCHOOLFULL` object already in `index.html`).

## After you add data

1. Open `validate.html` in a browser.
2. Load `index.html` (file picker, or same-origin fetch if you're running a
   local server).
3. Fix anything it flags — required fields, bad types, unknown school
   abbreviations, duplicate keys, dangling references.
4. Load `index.html` itself in a browser and click through the tab(s) your
   new data affects to eyeball it before committing.
