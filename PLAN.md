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

## Phase B — Data & UI (NEXT, Sonnet sessions — do not need Fable)

### B1. Encyclopedia upgrade (Notes tab)
One search box (the shared typeahead, already wired) over two sources:
- Built-in entries auto-generated from app data: all species, classes,
  subclasses, backgrounds, feats, spells, inventory items, plus a seeded
  glossary of ~40 core rules terms (conditions, actions, opportunity
  attacks, rests, concentration, cover...). Short original summaries with
  book+page citations, same style as spell cards. Read-only, but each
  carries an editable "Campaign notes" field that persists and exports
  with the character.
- User terms exactly as today (add/edit/delete), same results list,
  HOMEBREW tag vs book-source tag.
- Search matches name AND body text ("earth genasi" must return the
  species entry with traits and MPMM p.17). The current index only
  indexes names — extend it with a body-text field.
- Nice-to-have: entries that exist as cards elsewhere get a quick-jump
  link ("view in Spellbook/Inventory").

### B2. Inventory tab (rename from Equipment — "Inventory" everywhere)
- Keep card format, wax type tags, gold value, citation, Remove.
- Add-item search using the shared typeahead filtered to items (already
  wired); select → card added prefilled. Manual homebrew creation stays
  (HOMEBREW tag).
- Duplicate adds increment an editable qty on the existing card, never a
  second card.
- Seed the 2014 PHB item database per DATA_TEMPLATE.md: adventuring gear,
  weapons, armor, tools, packs, mounts/vehicles, trade goods. Magic items
  arrive with their DLC waves (a few DMG items already exist as
  engine-test entries).
- Wire WEAR/WIELD toggles and effects fields (engine already exists from
  Phase A) for seeded items.

### B3. Session 2 content as originally planned
All 2014 PHB species / classes / subclasses / feats / backgrounds, with
choice prompts (e.g. skill picks, language picks, fighting styles).
Prepared-caster logic for Cleric/Druid/Paladin/etc. (prepared list separate
from known spells, recalculated on ability/level change). Martial
quick-reference page (weapon properties reference for non-casters,
analogous to the spell compendium — fed by the wielded-weapon attack rows
from Phase A).

## Later sessions

### Session 3 — Multiclassing + seed party
2014 multiclassing rules (prerequisite ability scores, combined spell slot
table, proficiency limits on entry). Seed party as individual character
files:
- Halfling necromancy Wizard
- Aarakocra Tempest Cleric
- Firbolg Druid
- Centaur Fighter (homebrew "homunculus" flavor text only — standard
  centaur stats, no mechanical changes)

### Session 4 — DLC wave 1
Xanathar's Guide to Everything + Tasha's Cauldron of Everything content
(subclasses, spells, feats, the Tasha's customization options).

### Session 5 — DLC wave 2 + polish
Explorer's Guide to Wildemount, Strixhaven, Bigby Presents: Glory of the
Giants / Book of Many Things, Fizban's Treasury of Dragons content, plus
general polish pass.
