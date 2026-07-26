# Build Status

Read this before starting a data-entry session. It tells you exactly what's
implemented, what's stubbed, and what's known to be off. Written for a
cheaper model to pick up work without re-deriving context.

Last updated: Session 8 (class spell/ability data flood — pure data entry).
**Next: Session 9 — the revisit round (dice rolling, book-accuracy audit,
party.html DM roster).**

## Data (Session 8 — done): class spell & ability flood

Pure data-entry session, no engine changes. Validated (516/516 static) and
sweep-tested (1,345 builds, 0 failures).

- **Compendium (+143 → 469):** every existing wizard-list row that is
  genuinely shared now carries its 2014 class tags (5th element), and the
  missing non-wizard PHB lists were added — the full cleric, druid, bard,
  paladin, ranger, sorcerer, and warlock spell lists plus their cantrips
  (Guidance, Sacred Flame, Vicious Mockery, Eldritch Blast, Thorn Whip,
  Cure Wounds, Healing Word, Spirit Guardians, Hunter's Mark, Hex, Divine
  Smite-adjacent smites, etc.), inserted alphabetically within each level
  run. Wizard-only spells stay untagged (untagged = wiz). Every non-wizard
  caster now clears the builder's ≥10-row threshold, so the "still being
  seeded" note auto-hides (cleric 24 / druid 38 / bard 34 / paladin 15 /
  ranger 16 / warlock 29 / sorcerer 55 rows visible at builder level 1).
- **Class abilities (ABILITIES +81 → 94):** all 16 Battle Master maneuvers
  (+ a Superiority Dice explainer), all 8 sorcerer Metamagic options, the
  32 PHB Eldritch Invocations (level = warlock prerequisite; pact
  prerequisites noted in the desc), and the per-class core actives — Bardic
  Inspiration / Song of Rest / Countercharm, Channel Divinity: Turn Undead
  + Divine Intervention, Wild Shape, Lay on Hands / Divine Sense / Divine
  Smite / Cleansing Touch, Favored Enemy / Natural Explorer / Primeval
  Awareness, Arcane Recovery, Font of Magic, Deflect Missiles / Slow Fall /
  Ki-Empowered Strikes, Evasion (monk + rogue), Reliable Talent, Feral
  Instinct, Brutal Critical — plus Tempest Cleric's Wrath of the Storm and
  Destructive Wrath. Verified in the live engine: a Battle Master fighter's
  Attacks page renders all maneuvers; a Tempest cleric shows its Channel
  Divinity and Wrath of the Storm cards (Divine Intervention correctly
  gated to level 10).
- **Accuracy-rule practice:** class tags were added only where the 2014
  list membership is certain — under-tagged rather than guessing. Genuinely
  uncertain sourcebook lists were left untagged (defaults to wiz), never
  invented. Low-confidence skips: Warding Wind's bard entry (dropped),
  several EGW/FTD/TCE spells with hard-to-verify secondary classes
  (Nathair's Mischief, Immovable Object, Wristpocket, Kinetic Jaunt, the
  TCE summon-X 4th-level spells) left wizard-only.

## Engine (Session 7 — done): Class ability pages + lifecycle

- **Per-class page.** The Spellbook tab renames itself via
  `classPageName()`: wizard-primary "Spellbook", other caster-primary
  "Spells", pure martial "Attacks", martial-primary + caster "Spells &
  Attacks". Caster sections (`#spellSection`) hide for martials. New
  sections on the page: **Class Abilities** (`DATA:ABILITIES` cards
  filtered by class/level/subclass — 13 seed entries prove the schema;
  Session 8 floods it) and a read-only **Wielded Attacks** recap
  (`buildAttacksInto` gained target/readonly params). Abilities are
  indexed (`ability` type) and render in the encyclopedia.
- **Class-scoped compendium.** COMP rows carry an optional 5th element:
  space-separated class codes (`brd clr drd pal rgr sor wlk wiz`);
  untagged = `wiz` (the historical list IS the wizard list). New
  `#compClass` filter: "My class lists" (default) / per-caster-class /
  "All classes"; martial characters get a pointer to the All filter.
  13 confidently-known spells retagged as proof; Session 8 does the rest.
  The builder's Spells step filters by the chosen class and shows a
  "still being seeded" note while a class's list is sparse (<10 rows).
- **Lifecycle prompts.** Fresh visit with no save and no baked character:
  a welcome chooser offers New Character (builder) / Import (JSON file
  picker) / Browse the sample sheet (skipped in the sweep iframe). Close
  guard: `sessionDirty` (set on any save-queueing edit, cleared by either
  export) arms a `beforeunload` warning — browsers only allow their
  generic leave dialog, so the "export?" nudge is the dialog plus the
  footer buttons; a custom close popup is not possible on the web
  platform.
- Validator: ABILITIES block checks (keys, className/subclassKey
  referential integrity, levels), COMP 5-element + class-code validation,
  slice extended to `DATA:ABILITIES:END`. 435/435 static; sweep 1,345
  builds / 0 failures.

## Engine (Session 6 — done): The Goblin Market

- Builder Equipment step, gold mode: "Visit Grubbins' Goblin Market"
  opens a second overlay (`.overlay.mkt`, z-index above the builder).
  Stock = every ITEMS entry with a parseable price (`parseCostCp` handles
  cp/sp/ep/gp/pp incl. comma thousands; "—" items and the tortle-shell
  helper are excluded). Searchable list with type + stat snippet + price.
- Cart lives on the builder draft (`BW.cart` = [{key,qty}]), so Back
  preserves it and the Equipment step shows a summary rulebox. Cart
  controls are key-based (not index-based). `marketAdd` blocks any add
  that would exceed the class's starting gold; remaining-balance display
  turns amber under 20% and the goblin comments.
- Grubbins: inline-SVG stall + speech bubble (`GOBLIN` line pools —
  welcome uses species/class, species-specific quips, kit-driven
  suggestions via CLASS_META, low-funds/broke/no-results reactions).
- On create (gold mode): affordable cart lines become Inventory items
  (qty preserved, unequipped); exact change lands as gp/sp/cp coins.
  Live preview gained a "Gear" row; Review warns if a class switch made
  the cart exceed the new budget.
- Bug fixed en route: Quick Build no longer resets equipMode to "kit"
  when the player already chose gold and has a cart.
- Validated: 421/421 static, sweep 1,345 builds / 0 failures (no data
  changes this session — pure engine/UI).

## Data (Session 5 — done)

Pure data-entry session. Validated (421/421 static) and sweep-tested
(1,345 builds, 0 failures).

- **Species (+10 → 26):** the MPMM set — Aarakocra, Centaur, Firbolg,
  Goliath, Kenku, Tabaxi, Tortle, and Genasi (Air/Fire/Water) — all with
  flexible `{choose:[2,1]}` ASIs, choice prompts (Centaur's Natural
  Affinity skill, Kenku Recall's two skills, extra languages), and innate
  cantrip grants (drow-pattern). **The planned party is now buildable:**
  aarakocra tempest cleric, firbolg druid, centaur fighter all verified in
  the engine. Tortle natural armor is modeled as a wearable "Tortle Shell"
  ITEMS entry (AC 17, no Dex) — its card notes to ignore the proficiency
  tag, since kind must be one of the four armor classes and none is
  warning-free for every class.
- **Subclasses (+4 → 73):** Chronurgy + Graviturgy Magic (EGW wizard, at
  2), Drakewarden (FTD ranger), Way of the Ascendant Dragon (FTD monk) —
  resource/companion tracking noted as manual, EK-pattern.
- **Backgrounds (+5 → 18):** the five Strixhaven college backgrounds with
  their skill pairs, two bonus languages, and features pointing at the
  Strixhaven Initiate feat; each with BG_PERSONA pools.
- **Feats (+5 → 77):** Strixhaven Initiate, Strixhaven Mascot (grants a
  mascot-flavored Find Familiar card), Gift of the Chromatic / Metallic
  (grants Cure Wounds card) / Gem (half-feat, Int/Wis/Cha) Dragon.
- **Compendium (+17 → 326):** the EGW dunamancy wizard list (Fortune's
  Favor, Immovable Object, Wristpocket, Pulse Wave, Gravity Sinkhole,
  Temporal Shunt, Gravity Fissure, Tether Essence, Dark Star, Reality
  Break, Ravenous Void, Time Ravage) and FTD wizard-list spells
  (Nathair's Mischief, Ashardalon's Stride, Raulothim's Psychic Lance,
  Summon Draconic Spirit, Draconic Transformation), inserted in
  alphabetical level order.
- **Accuracy-rule skips (deliberate):** Book of Many Things backgrounds
  (Rewarded, Ruined) and the Cartomancer feat were NOT added — their
  mechanical specifics couldn't be stated confidently, and per the
  Session 4 lesson, flavor-guesses don't ship. Add them from the book
  when someone has it open.

## Data (Session 4 — done)

Pure data-entry session, no engine changes. All entries validated (400/400
static checks) and sweep-tested (861 builds, 0 failures).

- **Subclasses (+29 → 69 total):** every 2014 class gained its XGE + TCE
  subclasses (Barbarian: Ancestral Guardian/Storm Herald/Zealot/Beast/Wild
  Magic; Bard: Glamour/Swords/Whispers/Creation/Eloquence; Cleric: Forge/
  Grave/Order/Peace/Twilight; Druid: Dreams/Shepherd/Spores/Stars/Wildfire;
  Fighter: Arcane Archer/Cavalier/Samurai/Psi Warrior/Rune Knight; Monk:
  Drunken Master/Kensei/Sun Soul/Mercy/Astral Self; Paladin: Conquest/
  Redemption/Glory/Watchers; Ranger: Gloom Stalker/Horizon Walker/Monster
  Slayer/Fey Wanderer/Swarmkeeper; Rogue: Inquisitive/Mastermind/Scout/
  Swashbuckler/Phantom/Soulknife; Sorcerer: Divine Soul/Shadow Magic/Storm
  Sorcery/Aberrant Mind/Clockwork Soul; Warlock: Celestial/Hexblade/
  Fathomless/Genie; Wizard: War Magic/Bladesinging/Order of Scribes). Third-
  caster/resource subclasses (Psi Warrior) noted as manual-tracking like
  Eldritch Knight.
- **Feats (+30 → 72 total):** all 14 TCE general feats (Artificer Initiate,
  Chef, Crusher, Eldritch Adept, Fey Touched, Fighting Initiate, Gunner,
  Metamagic Adept, Piercer, Poisoner, Shadow Touched, Skill Expert, Slasher,
  Telepathic) and 15 XGE species feats (Keen Mind skipped — already PHB).
  Spell-granting feats (Fey Touched, Shadow Touched, Telepathic, Drow High
  Magic, Fey Teleportation, Wood Elf Magic) grant a spell card via `grants`
  like Telekinetic.
- **Custom Lineage (TCE p.8):** new species entry + `SPECIES_ASI`
  (`choose:[2]`) + language choice. Size/darkvision-or-skill/feat-at-1st are
  trait notes (manual, same pattern as other ability-score trait text).
- **Compendium (+5 → 309 total):** most XGE/TCE wizard-list spells were
  already seeded in Session 3; verified and added the genuinely missing
  ones — Life Transference, Investiture of Flame/Ice/Stone/Wind.
- **Items (+15 → 223 total):** XGE common magic items suited to any table
  (Cast-Off Armor, Cloak of Billowing, Clothes of Mending, Dread Helm, Ear
  Horn of Hearing, Enduring Spellbook, Hat of Vermin, Heward's Handy Spice
  Pouch, Instrument of Illusions, Mystery Key, Perfume of Bewitching, Pole
  of Collapsing, Ruby of the War Mage (attunement), Staff of Adornment,
  Wand of Smiles). Flavor `mech`/`desc` text where no supported effect kind
  applies; only Ruby of the War Mage needs attunement per the book.
- **Known cuts:** class-scoped spell lists (non-wizard) are still Session
  5+ scope; Psi Warrior's Psionic Energy dice, like EK/Arcane Trickster
  slots, are reference-only (track manually) — no new engine mechanic was
  added for third-caster or subclass resource tracking this session.

## Engine (Session 3 — done)

- **Multiclassing.** `S.build.classes = [{key, level, subclassKey}]` is
  canonical (`classes[0]` = first class, full proficiencies; later classes
  get `CLASS_META.mcProfs` 2014 grants — proficiency checks union across
  classes at runtime, no state mutation). Shared multiclass slot table via
  `multiCasterLevel()` (full + half÷2; single non-warlock caster keeps its
  own table so half-caster level-1 emptiness and Tor's wizard table are
  exact). Warlock Pact Magic renders as its own short-rest pip row
  (`S.pactUsed`). Hit dice pool per die size (`S.hdSpentBy`), spent/restored
  per pool. Add-a-class flow checks 2014 prerequisites both directions and
  confirm-overrides for homebrew. Migration: old single-class saves
  (classKey/subclassKey + fields.level) convert automatically.
- **Guided builder** (`openBuilder()` / `BW` draft state): 9 steps, free
  navigation with per-step warning badges, live preview, quick build +
  random, 4 score methods, species-bonus provenance, kit-or-gold equipment,
  persona roll tables (`BG_PERSONA`), review validation, create + export.
  The `showChooser()` card overlay is shared with Level Up (subclass +
  multiclass picks) — one selection system.
- **Theme voice** (`VOICE`, `voice(event)`): 13 events × 5 themes × 2-3
  variants; every hardcoded Tor/desert flavor string now routes through it.
  Spell epithets: `S.spellEpithets[name]` (tap the card's flavor line to
  edit); defaults from `SCHOOL_EPI[theme][school]`; Tor's originals were
  migrated into his state as edits (`epithetsMigrated` flag).
- **Reference tab**: `buildRefRules()` (title uses the character's name;
  ritual/Arcane Ward/concentration boxes are feature-conditional; rest
  boxes reflect actual hit-die pools; survival box is theme-keyed via
  `THEME_SURVIVAL`) and `buildSources()` (generated from source strings
  actually present in the data).
- **QOL**: Cast buttons (slot/pact spend + ORD-level status), concentration
  banner (one spell, tap to drop, cleared on long rest), tap-to-roll
  (saves/skills/initiative/attacks → d20 + mod flash), rest confirmations.
- **Coverage sweep** (validate.html → "Run coverage sweep"): hidden-iframe
  drive of the real engine — 756 builds, asserts no throws + HP/DC/slots/
  prepared/milestones/attack sections + data-completeness preflights
  (CLASS_META/SPECIES_ASI/BG_PERSONA/VOICE/SCHOOL_EPI coverage). Needs the
  page served (same-origin iframe), not file://.
- **Known cuts:** EK/Arcane Trickster subclass slots still manual; feat/ASI
  level-ups point to the Feats section rather than a dedicated step; the
  builder's Spells step uses the arcane/wizard compendium until class lists
  arrive with DLC waves; species ASIs apply in the builder but remain
  manual on the Stone Tablets for existing sheets.

## Engine + data (Phase B3 — done)

- **Caster types.** `spellsReadyCount(cl,lvl,m)` resolves prepared/known
  counts: fixed `preparedByLevel` (Tor's wizard only) → `preparedFormula`
  ("mod+level" / "mod+halfLevel", live from the effective casting-ability
  mod) → `knownByLevel` (21-entry arrays). Non-casters return "—"; levels
  with no slots return 0. `buildPips` handles null spellcasting, empty
  tables (paladin 1), and skips zero rows (pact tables); `maxSlotLevel`
  counts only non-zero slot levels. DC/spell-attack quickstats show "—"
  for martials. Hit-die display and rolls use `cl.hitDie`.
- **Choice prompts.** `applyChoices()` runs `choices` arrays
  (skills/language/tool, optional `from` whitelist) via prompt(), writing
  to `S.skillP`, `S.languages`, `S.proficiencies`. Species selection is
  confirm-gated (`applySpeciesPackage`: languages + grants + choices);
  background selection likewise (`applyBackgroundPackage`: skills + tools
  + bonus-language prompts).
- **Data:** 15 species (subraces flattened), 12 classes / 40 subclasses
  with 20-row milestones each, 13 backgrounds, ~42 feats. Validator
  extended for spellcasting variants and species choices — 355/355.
- **Martial quick reference** on the Reference tab: `buildWeaponTable()`
  renders every ITEMS weapon (searchable by name/type/property), plus
  weapon-properties and fighting-styles ruleboxes.
- **Known cuts:** Eldritch Knight / Arcane Trickster third-caster slots
  aren't tracked (noted on their subclass entries — track pips manually);
  fighting styles / Expertise / maneuvers have no picker (reference text
  only); species ASIs stay manual on the Stone Tablets by design; the
  spell compendium is still the wizard list (class-scoped lists are a
  DLC-wave task); subclass features beyond milestone text (e.g. Draconic
  Bloodline's 13+Dex AC) are not yet mechanical.

## Engine + data (Phase B1/B2 — done)

- **Encyclopedia (Notes tab).** `showEncyc(type,name)` renders read-only
  entries generated live from app data via `buildEncycBody()` — no
  duplicated content to keep in sync. Per-entry campaign notes live in
  `S.entryNotes` keyed `"<type>:<name>"` and ride exports automatically.
  The results list (`buildLoreList`) shows homebrew terms when the search
  box is empty and full-index results (30-cap, tagged) when typing;
  homebrew rows open the existing editor, everything else opens the
  encyclopedia panel. Quick-jump buttons (`data-jumpspell` /
  `data-jumpinv`) scroll-and-flash the matching card.
- **Body-text search.** Index entries carry a `b` (body) norm field;
  `searchIndex` adds a third result tier — token matching where every
  query word must appear in name+body ("earth genasi" → Genasi (Earth)).
  Fuzzy fallback still only fires when all three tiers are empty.
- **Inventory.** All UI labels renamed from Equipment (internal ids and
  `S.equipment` unchanged for save compat). Items have an editable `qty`
  (card input, default 1); adding an item whose name you already carry
  increments qty instead of duplicating. `migrateLegacyFields` converts
  the old "Dagger (×2)" seed into Dagger qty 2.
- **Data floods:** ITEMS ~190 entries (all PHB weapons/armor with
  structured combat data, gear, tools, packs, mounts/vehicles, trade
  goods); GLOSSARY 45 terms. Validator passes 281/281. The blowgun's flat
  "1" damage relaxed the validator's die regex to `\d+(d\d+)?`; the net
  intentionally has no `weapon` block (special rules in `mech`, no attack
  row).

## Engine (Phase A — done)

### Shared typeahead index (A1)
- `SEARCH_INDEX` — one flat array of `{name,type,source,desc,norm}` built by
  `rebuildIndex()` from every data source: SPECIES, CLASSES (+ subclasses),
  BACKGROUNDS, FEATS, SPELLS + COMP (deduped by name), ITEMS + user
  equipment (deduped), GLOSSARY, and `S.lore` (homebrew). `norm` is
  lowercased with punctuation stripped, so "tashas" matches "Tasha's...".
- `searchIndex(query, typesArrayOrNull)` returns `{pre, sub, fuzzy}` —
  prefix matches, substring matches (both alphabetical), and a Levenshtein
  "did you mean" list that only populates when the first two are empty.
  Linear scan; measured negligible at ~330 entries and stays fine at 1000+.
- `attachTypeahead(inputEl, {types, onSelect})` — dropdown with type tags
  (`TA_LABEL` map), 12-row cap + "N more", arrow keys/Enter/Escape/click,
  hides on blur. Live consumers: **Add Equipment name field** (types:
  ["item"], selecting prefills the form and clones structured data on Add)
  and **Notes tab search** (full index; homebrew terms open in the editor,
  other types show an index-summary preview box — B1 replaces that preview
  with full encyclopedia entries).
- `rebuildIndex()` runs in `rebuildAll()`, inside the `queueSave()`
  debounce (covers term renames), and explicitly after lore/equipment
  add/remove.

### Item effects engine (A2)
- Equipment entries now carry: `slot` ("wield"/"wear"/null), `equipped`,
  `attunement`, and structured `weapon` / `armor` / `effects` fields —
  exact shapes in DATA_TEMPLATE.md, enforced by validate.html.
- `computeEffects()` is the calculation core: walks equipped items and
  returns effective ability scores, AC, speed, save bonuses — each with
  provenance strings — plus guardrail warnings, display notes
  (advantage/resistance/stealth-disadvantage), attunement count, and hands
  in use. Unequip = the item drops out of the walk, so removal is always
  residue-free by construction.
- `recalc()` consumes it: **all** downstream numbers (saves, skills,
  passives, HP max, initiative, spell DC, hit-die healing) use effective
  scores. AC/Speed quickstats and ability tablets render provenance text;
  save rows carry it as a title tooltip.
- 2014 armor rules: light = full Dex, medium = Dex cap 2, heavy = no Dex,
  shield +2 (wielded, costs a hand); Str-requirement unmet → speed −10
  with provenance; multiple armors → warning, best one counts.
- Guardrails (warn, never block): 2+ suits of armor, >2 hands of
  weapons/shields, >3 equipped attunement items, armor/shield without
  proficiency (also a NOT PROFICIENT tag on the card). Warnings render on
  both the Combat attacks box and the Equipment tab.
- Class schema gained `armorProfs` (light/medium/heavy/shield) and
  `weaponProfs` (profKeys and/or "simple"/"martial") — wizard seeded per
  2014 PHB.

### Auto-derived attacks (A3)
- Every wielded weapon renders an attack row on the Combat tab: to-hit =
  ability mod (Dex if ranged; better of Str/Dex if finesse; else Str) +
  proficiency if the class is proficient (`weaponProficient()`); damage =
  die + same mod + type; versatile die shown with the mod applied.
  Non-proficient weapons show "no prof bonus" instead of silently adding it.
- Editable homebrew attack rows (`S.customAttacks`: name/hit/dmg/note) with
  add/remove. The old freeform attacks textarea is gone; migration moves
  any saved `fields.attacks` text into one homebrew row.
- This section is the seed of Phase B's martial quick-reference page.

### Data blocks added
- `DATA:ITEMS` — 15 engine-test entries (Tor's gear, leather/scale/chain
  armor, shield, light crossbow, potion, and 4 DMG attunement items chosen
  to exercise abilitySet/acBonus/saveBonus/advantage). The full 2014 PHB
  item flood is Phase B work (B2).
- `DATA:GLOSSARY` — 3 shape-example entries (Concentration, Opportunity
  Attack, Short Rest). Phase B seeds the ~40-term core glossary (B1).
- validate.html now checks both new types (and the class prof arrays) and
  reads to the `DATA:GLOSSARY:END` marker; currently 46/46 checks pass.

### Migration (`migrateLegacyFields`)
Old saves/exports get: gold → cur_gp, langs/tools strings → chip arrays,
`fields.attacks` → homebrew attack row, and flat equipment entries enriched
with structured data by name (Staff/Dagger/Robe/Spellbook/Scholar's Pack);
enriched weapons + clothing auto-equip to match old behavior. The JSON
import path now runs migration too (it previously didn't — latent bug fixed).

### Notes for the Phase B session
- Do NOT rebuild any of the above. B1/B2 are data + consumer work: extend
  `rebuildIndex()` with a body-text field for full-text search, render full
  encyclopedia entries instead of the `lorePreview` stub, rename Equipment
  → Inventory, add qty-increment on duplicate adds, and flood ITEMS /
  GLOSSARY / species / classes / feats / backgrounds per DATA_TEMPLATE.md.
- Add new items ONLY between the DATA:ITEMS markers; run validate.html
  (46+ checks) before every commit.
- The typeahead component is generic — `attachTypeahead(input, {types,
  onSelect})` — reuse it, don't fork it.

## Engine (Session 1.5 — playtest fixes, done)

- Loot add-row moved into a `<tfoot>` inside `table.loot` so its inputs are
  pixel-aligned with the Item/Qty/Note headers (previously a separate flex
  div with no relation to the table's column widths). Qty input gets a
  tinted background to visually differentiate it from Item/Note.
- Currency is 5 coin fields (`data-k="cur_pp/gp/ep/sp/cp"`) instead of one
  gold field. Inputs are sanitized on every keystroke (non-digit characters
  stripped, so decimals and letters can't survive) via a shared `.coinInput`
  class hooked into the global input listener. `migrateLegacyFields()`
  moves any pre-existing `S.fields.gold` into `cur_gp` on load so
  playtesters' saved gold isn't lost.
- New **Equipment** tab: cards styled identically to spellbook cards
  (`class="spell equip"`), with a circular "wax seal" type tag
  (`.waxseal.<type>`) in the top-right corner instead of a spell badge/tag.
  `S.equipment` is an array of `{name,type,mech,desc,source,value}`. Seeded
  with Tor's gear, migrated out of the old free-text Equipment textarea
  (which has been removed from the Character tab).
- New **Notes** tab: campaign encyclopedia. `S.lore` is an array of
  `{term,def,places,notes}`; a search box filters a clickable term list
  (`loreSel` tracks the selected index, not the term string, so renaming a
  term can't break selection); the detail panel's 4 fields write directly
  into `S.lore[loreSel]`. Plus a freeform `data-k="campaignNotes"` textarea.
- Languages and Proficiencies (renamed from "Tools") converted from plain
  text fields to chip lists (`S.languages`, `S.proficiencies`, both flat
  string arrays). Languages adds from a grouped `<select>`
  (`LANGUAGES = {Standard:[...], Exotic:[...]}`); Proficiencies adds from a
  searchable `<input list="profDatalist">` sourced from
  `TOOL_PROFICIENCIES`. Both also accept freeform custom entries through
  the same add button. `migrateLegacyFields()` parses any old
  comma-separated `S.fields.langs`/`S.fields.tools` into these arrays on
  first load after the update.
- All four new/changed state shapes (`equipment`, `lore`, `languages`,
  `proficiencies`) are wired into `mergeState()` and `rebuildAll()`, and
  ride along automatically in JSON/HTML export — no export-path changes
  were needed since both export functions serialize the whole `S` object.

## Engine (Session 1 — done)

- Data-driven schemas for species / class / background / feat, each a plain
  JS array of object literals inside `index.html`, wrapped in
  `/* DATA:X:START */ ... /* DATA:X:END */` marker comments.
- Identity dropdowns (Class/Subclass/Background/Species) are populated from
  those arrays — adding an entry to an array makes it selectable with no
  other code changes.
- Spellbook is one unified, removable/re-addable grid (base spells + species
  grants + feat grants + manually added spells all render together).
- Feat picker (Level Up tab) applies a feat's `grants` (spells), ability
  bumps, save proficiency, and HP-per-level bonuses directly to the sheet,
  and logs a line to the Feats & ASI log.
- Theme system: 5 palettes (desert/forest/pirate/tavern/norse) via CSS
  variables on `[data-theme]`, plus a small inline-SVG scene background per
  theme. Swapping themes only requires new CSS variable blocks + an SCENES
  entry — no other code changes.
- Full-body character art upload, auto-shrunk (max height 700px, PNG to
  keep transparency), peeking from the header's bottom-right edge; degrades
  cleanly to a small "+ Art" button when absent.
- Export: "Export character file" bakes current state into a fresh,
  self-contained HTML copy (own localStorage key, so multiple exported
  characters don't collide); JSON export/import; print stylesheet.
- TTS lobby password field is excluded from every export path (verified: it
  never appears in exported HTML or JSON, only in local `localStorage`).
- Storage: uses `localStorage` (falls back to `window.storage` if that
  global exists, for compatibility with the environment the original file
  was authored in). Each character file carries its own storage key so
  sibling character files on the same origin (e.g. GitHub Pages) don't
  clobber each other.

## Data coverage (as of Session 8 — class spell/ability lists complete)

| Type | Implemented |
|---|---|
| Species | 26 (all PHB + Custom Lineage + 10 MPMM incl. all four Genasi) |
| Classes | 12 (all 2014) |
| Subclasses | 73 (all PHB + XGE + TCE + EGW wizard schools + FTD) |
| Backgrounds | 18 (all PHB + 5 Strixhaven) |
| Feats | 77 (all PHB + TCE + XGE species feats + SCC + FTD Gifts) |
| Spells (seed spellbook) | 9 (Tor's) |
| Spell compendium (browse/add) | 469 (all-class 2014 lists: full PHB per class + XGE + TCE + EGW + FTD, class-tagged) |
| Class ability cards (ABILITIES) | 94 (maneuvers, metamagic, invocations, per-class core actives, tempest signatures) |
| Inventory items (ITEMS db) | ~224 (full 2014 PHB gear + DMG/XGE magic items + Tortle Shell helper) |
| Rules glossary (GLOSSARY) | 45 (conditions, actions, core mechanics) |

## Standing quirks / deliberate cuts (current)

- **Abjurer subclass timing (Tor only).** Tor's sheet keeps Arcane Ward at
  wizard level 3 (his original hand-built behavior, and his fixed
  `preparedByLevel` table); the data's real subclass entries choose at 2.
  House-rule status: intentional, per the table.
- **Feat mechanical support is partial by design.** Feats mutate the sheet
  via `grants` / `abilityChoiceFrom` / `hpBonusPerLevel`; everything else
  is reference text plus a Feats & ASI log line.
- **Upcast tier pips** exist only on the hand-authored seed `SPELLS`;
  compendium/grant cards render at base level.
- **BOMT content pending book verification** (Rewarded, Ruined,
  Cartomancer) — see Session 5 notes above.

## Files

- `index.html` — the engine + Tor Duneshifter's character, in one file.
- `BUILD_STATUS.md` — this file.
- `DATA_TEMPLATE.md` — exact schema + paste-in instructions for new data.
- `validate.html` — open in a browser, load `index.html`, click Validate.
- `PLAN.md` — full multi-session roadmap.
