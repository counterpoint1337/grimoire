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

## Later sessions

### Session 2 — Full 2014 PHB content
All 2014 PHB species / classes / subclasses / feats / backgrounds, with
choice prompts (e.g. skill picks, language picks, fighting styles).
Prepared-caster logic for Cleric/Druid/Paladin/etc. (prepared list separate
from known spells, recalculated on ability/level change). Martial
quick-reference page (weapon properties, maneuvers-free reference for
non-casters, analogous to the spell compendium).

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
