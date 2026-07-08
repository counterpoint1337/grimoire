# D&D 5e Grimoire — Project Plan

## Project

Data-driven D&D 5e (2014 rules) character sheet web app. Each character is a
single self-contained HTML file (no build step, no external assets, no
server) — open it in a browser and it works, offline, forever. Desert-themed
grimoire aesthetic is the flagship look, now one of several selectable
themes.

**Rules baseline:** 5e 2014 (PHB/XGE/TCE-era). 2024-book spells are allowed
if the table's DM approves them (Tor's sheet already mixes in a few). Book
content in the app is always a **short original summary** with a
book + page reference — never verbatim reprinted text.

Everything the engine can calculate, it calculates automatically — but every
calculated value stays manually overridable, because house rules and DM
rulings happen.

## Architecture decisions (Session 1)

- **No build step.** Data (species/classes/backgrounds/feats/spells) lives
  as plain JS object literals inline in the HTML `<script>`, between marker
  comments (`/* DATA:SPECIES:START */ ... /* DATA:SPECIES:END */`) so later
  sessions can find and extend the right block without reading the whole
  engine.
- **One engine, edited in place, exported per character.** `index.html` is
  both the live engine and Tor's current sheet (state baked in). To create
  a new character: open the file, edit fields/species/class/etc., then use
  **Export character file** to save a new, fully self-contained HTML copy
  with that state baked in. There's no separate "blank template" file —
  exporting from any character file *is* the template mechanism.
- **BUILD_STATUS.md** at repo root — human/agent-readable manifest of what
  data is implemented vs. stubbed (species/classes/subclasses/feats/spells
  counts, known issues), so a cheaper model can open it first and know
  exactly what's left before touching code.
- **DATA_TEMPLATE.md** at repo root — strict, copy-pasteable schema
  reference for each data type (species/class/subclass/background/feat/
  spell), with the exact object shape, required fields, and where in
  `index.html` to paste new entries.
- **validate.html** at repo root — a standalone, dependency-free browser
  page: open it, click Validate, it extracts the data blocks from
  `index.html` and checks required fields / types / cross-references
  (e.g. a class's spellcasting ability must be a real ability key). No
  Node.js required.

## Session 1 (today)

- [x] Read existing `tor_duneshifter_grimoire_4.html` to understand current
      engine.
- [ ] Git repo init, GitHub repo created, GitHub Pages enabled, pushed.
- [ ] Refactor engine to be data-driven: species / class / subclass /
      background / feat schemas, with Tor's own build as the seed data
      (proves the schema before Session 2 fills in the whole PHB).
- [ ] Theme system: desert, forest, pirate, tavern-town, norse-ice
      palettes, each with a simple CSS/SVG scene background; theme picker
      control.
- [ ] Full-body character art upload — auto-shrunk, peeking from the page
      edge like a game profile screen; works fine with no art uploaded.
- [ ] Header rework: portrait bigger and shifted further left; character
      name in a Papyrus-style display font.
- [ ] Spellbook: base + added spells render together in one grid; every
      card removable and re-addable regardless of origin.
- [ ] Feat picker on the Level Up tab that writes its benefits directly to
      the character sheet.
- [ ] Export buttons: "Export character file" (self-contained HTML, state
      baked in), JSON export/import, print-to-PDF stylesheet.
- [ ] TTS lobby password field stays local-only — excluded from every
      export (HTML, JSON, print).
- [ ] BUILD_STATUS.md + DATA_TEMPLATE.md + validate.html.

## Phase A — Architecture (DONE, Fable session, 2026-07-07)

Built ahead of the Phase B data flood so the expensive reasoning work is
finished before cheaper data-entry sessions start:

- **Shared typeahead index** (`SEARCH_INDEX` + `attachTypeahead()`): one
  in-memory index over species/classes/subclasses/backgrounds/feats/spells/
  items/rules/homebrew terms. Prefix-first then substring, alphabetical,
  case- and punctuation-insensitive, type tags on every suggestion, 12-row
  cap with "N more", arrow-key + Enter selection, Levenshtein "did you
  mean" fallback on zero matches. Linear scan — measured fine for 1000+
  entries. Live consumers: Add Equipment name field (items only, prefills
  the form from the ITEMS db) and the Notes tab search (full index).
- **Item effects engine**: WIELD/WEAR toggles on equipment cards; armor AC
  formulas (2014 Dex caps, shields, Str requirements), abilityBonus/
  abilitySet/acBonus/saveBonus/speedBonus/advantage/resistance effects;
  every modified number carries provenance text; warnings (never blocks)
  for double armor, >2 hands, >3 attunement, non-proficient armor.
- **Auto-derived attacks** on the Combat tab from wielded weapons
  (finesse/ranged ability selection, class weapon proficiencies), plus
  editable homebrew attack rows; old freeform attacks text migrates into a
  homebrew row.
- Validator + DATA_TEMPLATE.md extended for item and glossary entries.

## Phase B1 + B2 — Encyclopedia & Inventory (DONE, 2026-07-08)

- **B1 Encyclopedia (Notes tab):** one search over the shared index with
  body-text token matching ("earth genasi" finds Genasi (Earth) with its
  traits and MPMM p.17). Built-in entries render read-only from app data
  (species/class/subclass/background/feat/spell/item/rule) with citations,
  and every entry carries an editable per-entry campaign-notes field
  (`S.entryNotes`, persisted and exported). Homebrew terms keep the full
  add/edit/delete editor, tagged HOMEBREW in the same results list.
  Entries that exist as cards get quick-jump links ("View in Spellbook /
  Inventory" with scroll-and-flash).
- **B1 Glossary:** 45 core rules terms seeded — all 15 conditions, the 10
  standard actions, and 20 core mechanics (cover, crits, death saves,
  rests, concentration, attunement, etc.) with PHB page refs.
- **B2 Inventory:** renamed from Equipment everywhere (UI labels; internal
  ids and the `equipment` state key kept for save compatibility).
  Duplicate adds increment an editable qty on the existing card. Full 2014
  PHB item database seeded (~190 entries): all weapons with structured
  attack data, all armor with AC formulas, adventuring gear, tools/
  instruments/kits, packs, mounts/tack/vehicles, trade goods — everything
  wired to the Phase A wield/wear/effects engine. Magic items still arrive
  with their DLC waves (4 DMG engine-test items remain).

## Phase B3 — Session 2 content (DONE, 2026-07-08)

- **Engine:** full caster-type support — `spellcasting:null` martials (slot
  tracker shows a martial note, DC/attack show "—"), half casters (empty
  level-1 slot arrays), Warlock Pact Magic (zero-padded slot table, rows
  skipped, short-rest note), and dynamic prepared counts via
  `preparedFormula` ("mod+level" for cleric/druid, "mod+halfLevel" for
  paladin) recomputed live from the casting ability, or `knownByLevel`
  tables for known casters. Hit dice (display + rolls) follow the class.
  Tor's wizard keeps its legacy fixed `preparedByLevel` table untouched.
- **Species:** all 2014 PHB species with subraces flattened (15 entries incl.
  Genasi). Choice prompts (`choices` array: skills/language/tool picks)
  fire on selection, confirm-gated, and write straight to the skill list
  and chips. Fixed languages auto-add; innate cantrips land in the
  spellbook via grants.
- **Classes:** all 12 with proficiencies, saves, hit dice, spellcasting
  configs, 20-row milestone tables, and 40 subclasses. EK/Arcane Trickster
  third-caster slot tracking is deferred (noted on their subclass entries).
- **Backgrounds:** all 13, auto-applying skills/tools/bonus-language
  prompts on selection (confirm-gated).
- **Feats:** all ~42 PHB feats (half-feats apply their +1 via the existing
  ability-choice flow).
- **Martial quick reference (Reference tab):** searchable weapon table
  generated from the ITEMS db, weapon-properties glossary, fighting styles.
- Fighting styles are reference content, not a picker — a per-level choice
  system (styles, Expertise picks, maneuvers) is future scope.

## Later sessions

### Session 3 — Multiclassing + builder + generalization (DONE, 2026-07-08)
- **Multiclassing (2014):** `S.build.classes[]` with per-class levels and
  subclasses; add-a-class flow with prerequisite checks (warn + homebrew
  override, both directions); shared multiclass slot table (+ separate Pact
  Magic row); per-die hit-dice pools; multiclass proficiency grants (2014
  partial grants for non-first classes); per-class prepared/known counts;
  "Wizard 3 / Fighter 1" provenance in the header and milestones.
- **Guided character builder:** 9-step overlay (Class → Species → Scores →
  Background → Proficiencies → Equipment → Spells → Details → Review) with
  free navigation, warning badges, live preview panel, card-based selection,
  shared-typeahead species search, 4 ability-score methods (standard array,
  27-point point-buy with live budget, in-app 4d6-drop-lowest roller,
  manual) with species-bonus provenance ("17 = 15 + 2 half-orc"), per-class
  Quick Build and full Random Character, class equipment packages that land
  pre-equipped in Inventory OR starting gold, background persona roll
  tables, review validation, create + export. Built on the same chooser
  component the Level Up tab uses for subclass/multiclass picks.
- **Theme voice:** all flavor strings moved to a per-theme VOICE table
  (13 events × 5 themes × 2-3 variants, random pick, character-neutral);
  spell epithets are per-spell editable fields with theme/school defaults
  (Tor's originals persist as his edits); Reference tab quick-rules,
  labels, and Sources generate from the actual character and data.
- **Engine coverage proof:** validator "Run coverage sweep" drives the real
  engine through 756 builds (every species × class × subclass at levels
  1/5/11/20, all subclasses at 20, 8 multiclass pairs) asserting HP/DC/
  slots/counts/milestones/attacks — 0 failures. Runs against future DLC
  data automatically.
- **QOL:** Cast buttons spend slot pips (pact-aware); concentration banner
  (one spell at a time, tap to drop); tap-to-roll on saves/skills/
  initiative/attack rows; rest confirmation prompts.
- **Note:** party characters are NOT seeded — the engine supports every
  legal combination (sweep-proven) and characters are created through the
  builder. EK/Arcane Trickster subclass slots remain manual-tracking.

### Session 4 — DLC wave 1
Xanathar's Guide to Everything + Tasha's Cauldron of Everything content
(subclasses, spells, feats, the Tasha's customization options).

### Session 5 — DLC wave 2 + polish
Explorer's Guide to Wildemount, Strixhaven, Bigby Presents: Glory of the
Giants / Book of Many Things, Fizban's Treasury of Dragons content, plus
general polish pass.
