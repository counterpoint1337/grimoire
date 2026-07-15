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
  ],
  choices: [                          // optional — prompted when the species is selected
    {type: "skills",   count: 2, prompt: "Skill Versatility"},          // toggles skill proficiency
    {type: "language", count: 1, prompt: "Extra language"},             // adds a language chip
    {type: "tool",     count: 1, from: ["Smith's Tools", "..."],        // adds a proficiency chip;
     prompt: "Dwarven tool proficiency"}                                //   from limits the options
  ]
}
```

Ability score increases are recorded as a trait line (text), not applied
automatically — scores stay manually set on the Stone Tablets.

## Class — between `DATA:CLASSES:START` / `DATA:CLASSES:END`

```js
{
  key: "unique-lowercase-key",
  name: "Display Name",
  source: "Book p.NNN",
  hitDie: 8,                          // 6 | 8 | 10 | 12
  primaryAbility: ["STR"],            // array of ability codes: STR DEX CON INT WIS CHA
  savingThrows: ["STR", "CON"],       // exactly 2 ability codes
  armorProfs: ["light", "shield"],    // subset of light/medium/heavy/shield; [] for none.
                                      //   Drives the NOT PROFICIENT warning on equipped armor.
  weaponProfs: ["simple", "longsword"], // "simple"/"martial" categories and/or specific weapon
                                      //   profKeys (see the Item template). Drives whether the
                                      //   proficiency bonus lands on auto-derived attack rows.
  spellcasting: null,                 // null for non-casters (Barbarian/Fighter/Monk/Rogue), or:
  // spellcasting: {ability:"WIS", type:"prepared"|"known", ritual:true|false, focus:"a holy symbol",
  //   slotsByLevel: SLOTS_FULL | SLOTS_HALF | SLOTS_PACT | custom table,
  //   AND exactly one of:
  //     preparedFormula: "mod+level"      // cleric/druid — recomputed live from the ability mod
  //     preparedFormula: "mod+halfLevel"  // paladin
  //     knownByLevel: KNOWN_BARD          // 21-entry array (index 0 unused), for known casters
  //     preparedByLevel: PREP_WIZARD      // fixed table (legacy — Tor's wizard only)
  //   pact: true                          // warlock — adds the short-rest note to the slot tracker
  // },
  // Shared tables already defined: SLOTS_FULL (bard/cleric/druid/sorc/wizard),
  // SLOTS_HALF (paladin/ranger; level 1 is []), SLOTS_PACT (warlock; zeros pad
  // the sub-pact levels and are skipped by the pip renderer), KNOWN_BARD/
  // KNOWN_SORCERER/KNOWN_RANGER/KNOWN_WARLOCK.
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

Flat array with an optional 5th class-tags element:

```js
["Spell Name", 1, "Evoc", "one-line lowercase summary, under ~50 chars", "clr drd wiz"]
```

- School abbreviation: one of `Abj Conj Div Ench Evoc Illu Necro Trans`
  (mapped to full names via `SCHOOLFULL`).
- Class tags (5th element): space-separated codes from
  `brd clr drd pal rgr sor wlk wiz` — the spell's 2014 class lists.
  **Untagged rows default to `wiz`** (the historical list is the wizard
  list). Rows must stay sorted by level (validator-enforced) and
  alphabetical within each level run. **Only tag classes you are certain
  of** — under-tagging is recoverable, invented tags are bugs.

## Class ability — between `DATA:ABILITIES:START` / `DATA:ABILITIES:END`

Ability cards for the per-class page (the renamed "Spellbook" tab —
"Attacks" for martials, "Spells" for casters). Rendered when the character
has the class at or above `level` (and the matching subclass, if set).

```js
{
  key: "unique-kebab-key",
  name: "Second Wind",
  className: "fighter",          // a CLASSES key (validator-enforced)
  subclassKey: "battle-master",  // optional — omit for base-class features
  level: 1,                      // class level it unlocks at (default 1)
  cost: "Bonus action · 1/short rest",  // optional action/resource line
  desc: "Your short original summary.",
  source: "PHB p.72"
}
```

## Inventory item — between `DATA:ITEMS:START` / `DATA:ITEMS:END`

Every field below the `desc` line is optional — plain gear (rope, rations)
needs only the top block. The validator enforces the shapes shown.

```js
{
  key: "unique-lowercase-key",        // stable forever; character files reference it as itemKey
  name: "Display Name",
  type: "weapon",                     // weapon | armor | tool | clothing | vehicle | wondrous | consumable | other
  slot: "wield",                      // "wield" (weapons/shields) | "wear" (armor/clothing/rings/cloaks) | null (no toggle)
  cost: "2 sp",                       // required — use "—" for priceless magic items
  weight: "4 lb",                     // required — use "—" if not applicable
  source: "PHB p.149",                // required — book + page
  mech: "Optional freeform mechanics line shown in the card's meta strip.",
  desc: "Your short original summary.",

  // WEAPONS ONLY — drives the auto-derived attack row on the Combat tab:
  weapon: {
    profKey: "quarterstaff",          // kebab-case identity matched against class weaponProfs
    category: "simple",               // "simple" | "martial" — also matched against weaponProfs
    dmg: "1d6",                       // damage die, digits'd'digits
    dmgType: "bludgeoning",           // one of the 13 damage types, lowercase
    versatile: "1d8",                 // optional — two-handed damage die
    finesse: false,                   // true → attack uses the better of Str/Dex
    ranged: false,                    // true → attack uses Dex
    twoHanded: false,                 // true → counts as 2 hands for the guardrail warning
    thrown: "20/60",                  // optional — range string
    props: ["versatile (1d8)"]        // display strings for the card and attack row; may be []
  },

  // ARMOR ONLY — drives the AC formula in the calc engine:
  armor: {kind:"heavy", baseAC:16, dexCap:0, stealthDisadv:true, strReq:13},
  //   kind: light | medium | heavy | shield
  //   dexCap: null = add full Dex (light) · 2 = capped (medium) · 0 = no Dex (heavy)
  //   shield instead uses: {kind:"shield", acBonus:2}
  //   stealthDisadv / strReq optional (strReq unmet → speed −10, per 2014 rules)

  // MAGIC EFFECTS — applied while the item is equipped, removed cleanly on unequip:
  effects: [
    {kind:"abilitySet",  ability:"CON", value:19},   // score becomes N if not already higher
    {kind:"abilityBonus",ability:"STR", value:1},    // +N to the score
    {kind:"acBonus",     value:1},                   // +N AC (stacks with armor formula)
    {kind:"saveBonus",   ability:"ALL", value:1},    // +N saves; ability code or "ALL"
    {kind:"speedBonus",  value:10},                  // +N ft walking speed
    {kind:"advantage",   on:"Dexterity (Stealth) checks"},  // display-only note
    {kind:"resistance",  to:"fire"}                  // display-only note
  ],
  attunement: true                    // shows the attunement seal; equipped attunement
                                      //   items count toward the 3-item cap warning
}
```

## Rules-glossary entry — between `DATA:GLOSSARY:START` / `DATA:GLOSSARY:END`

```js
{
  key: "unique-lowercase-key",
  term: "Opportunity Attack",
  source: "PHB p.195",                // book + page, required
  def: "Your short original summary — 1-3 sentences, never verbatim book text."
}
```

These feed the shared search index (tagged RULE) and, in Phase B, the Notes
tab encyclopedia. The Phase B seeding target is ~40 core terms: all
conditions, the standard actions, rests, concentration, cover, advantage/
disadvantage, etc.

## Class meta — between `DATA:CLASSMETA:START` / `DATA:CLASSMETA:END`

**Every class MUST have a `CLASS_META` entry keyed by its class key** — the
coverage sweep fails without it. Shape:

```js
classkey: {
  prereq: [{STR:13},{DEX:13}],        // 2014 multiclass prereq alternatives (any one suffices)
  mcProfs: {armor:["light"], weapons:["simple"], text:"human-readable summary"},
  skills: {count:2, from:[...skill names...]},  // or from:"any" (bard)
  quick: {statOrder:["STR","CON",...], skills:[...]}, // PHB quick-build suggestion
  kit: [{key:"itemKey", qty:2, eq:true}, ...],  // starting equipment (ITEMS keys; eq = starts equipped)
  gold: 125                            // average starting-gold alternative
}
```

## Species ASI — between `DATA:SPECIESASI:START` / `DATA:SPECIESASI:END`

**Every species MUST have a `SPECIES_ASI` entry** (sweep-enforced):
`{fixed:{CON:2,WIS:1}}` and/or `{choose:[2,1]}` (bonuses the player assigns).

## Background persona — between `DATA:BGPERSONA:START` / `DATA:BGPERSONA:END`

**Every background MUST have a `BG_PERSONA` entry** (sweep-enforced):
`{traits:[2+ strings], ideals:[...], bonds:[...], flaws:[...]}` — short
original suggestions for the builder's roll buttons.

## Theme voice — between `DATA:VOICE:START` / `DATA:VOICE:END`

New themes need a `VOICE.<themekey>` object with **2-3 variants for all 13
events** (saved/restored/hitdie/longrest/shortrest/spellAdd/spellRemove/
itemAdd/levelup/deathClear/portrait/lootEmpty/hpBar — sweep-enforced) and a
`SCHOOL_EPI.<themekey>` map (one default epithet per spell school). Voice is
evocative but character-neutral: never reference a specific character's
abilities.

## After you add data

1. Open `validate.html` in a browser.
2. Load `index.html` (file picker, or same-origin fetch if you're running a
   local server).
3. Fix anything it flags — required fields, bad types, unknown school
   abbreviations, duplicate keys, dangling references.
4. Load `index.html` itself in a browser and click through the tab(s) your
   new data affects to eyeball it before committing.
