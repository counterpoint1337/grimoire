# Build Status

Read this before starting a data-entry session. It tells you exactly what's
implemented, what's stubbed, and what's known to be off. Written for a
cheaper model to pick up work without re-deriving context.

Last updated: Session 3 (multiclassing, guided builder, theme-voice
generalization, coverage sweep). **The architecture is complete. Remaining
work is Sessions 4-5 (see PLAN.md): DLC data waves — Xanathar's + Tasha's,
then Wildemount/Strixhaven/Book of Many Things/Fizban's + polish. Both are
template-driven data entry per DATA_TEMPLATE.md, Sonnet-suitable, and the
validator's coverage sweep tests new data automatically.**

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

## Data coverage (as of Session 1)

| Type | Implemented | Total (2014 PHB) |
|---|---|---|
| Species | 15 (all PHB w/ subraces flattened + Genasi Earth) | done |
| Classes | 12 (all) | done |
| Subclasses | 40 (all PHB) | done |
| Backgrounds | 13 (all) | done |
| Feats | ~42 (all PHB + Telekinetic + Magic Initiate (Sage)) | done |
| Spells (seed spellbook) | 9 | — |
| Spell compendium (browse/add) | ~230 (wizard-only, unchanged from original) | full PHB list is class-scoped |
| Inventory items (ITEMS db) | ~190 (full 2014 PHB gear/weapons/armor/tools/packs/mounts/trade goods + 4 DMG test items) | magic items arrive with DLC waves |
| Rules glossary (GLOSSARY) | 45 (conditions, actions, core mechanics) | done for core; DLC terms later |

Session 2 fills in the rest of the 2014 PHB for all five types above, adds
choice-prompt UI for things like skill/language picks and fighting styles,
prepared-caster logic for non-wizard casters, and a martial quick-reference
page.

## Known issues / deliberate scope cuts

- **Abjurer subclass timing.** This data models Tor's sheet as choosing
  Abjuration Wizard (and gaining Arcane Ward) at **level 3**. The real 2014
  PHB Wizard chooses an Arcane Tradition at **level 2**. This was already
  the case in the original hand-built sheet; Session 1 preserved the exact
  numeric behavior rather than silently changing Tor's math. Session 2
  should either correct this to level 2 when building the real Wizard class
  data, or confirm it's an intentional table house rule before changing it.
- **Feat mechanical support is partial.** Only feats with `grants` (spells),
  `abilityChoice`/`abilityChoiceFrom` (+1 ability, optional save
  proficiency), or `hpBonusPerLevel` actually mutate the sheet. Feats
  without one of those (War Caster, Alert) just log a line to the Feats &
  ASI textarea — they don't yet grant their full mechanical text anywhere
  else on the sheet. Full feat mechanics (e.g. War Caster's reaction-cast
  rule) are a display/reference concern for Session 2, not a calculation.
- **Background schema is metadata-only.** `BACKGROUNDS` entries carry
  `skillProf`/`feature` data but selecting a background does not yet
  auto-toggle skill proficiencies or auto-fill the features textarea — that
  auto-apply behavior, plus the "choice prompts" it implies (e.g. picking
  which 2 skills a class-choice grants), is explicitly Session 2 scope.
- **Upcast tier pips only exist on the hand-authored `SPELLS` seed array**,
  not on spells added via the compendium or via species/feat grants (those
  render with a flat description at their base level). This matches the
  original file's behavior for compendium-added spells; extending upcast
  support everywhere is a nice-to-have for a later session, not required by
  the brief.
- **Multiclassing is not implemented.** `S.build` assumes a single class/
  subclass. Session 3 scope.

## Files

- `index.html` — the engine + Tor Duneshifter's character, in one file.
- `BUILD_STATUS.md` — this file.
- `DATA_TEMPLATE.md` — exact schema + paste-in instructions for new data.
- `validate.html` — open in a browser, load `index.html`, click Validate.
- `PLAN.md` — full multi-session roadmap.
