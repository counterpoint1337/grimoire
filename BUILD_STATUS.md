# Build Status

Read this before starting a data-entry session. It tells you exactly what's
implemented, what's stubbed, and what's known to be off. Written for a
cheaper model to pick up work without re-deriving context.

Last updated: Session 1 (engine + theme + export infrastructure).

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
| Species | 1 (Genasi, Earth) | 9 |
| Classes | 1 (Wizard) | 12 |
| Subclasses | 1 (School of Abjuration) | ~40 |
| Backgrounds | 1 (Sage) | 13 |
| Feats | 6 (Magic Initiate (Sage), Resilient, War Caster, Telekinetic, Alert, Tough) | ~40 |
| Spells (seed spellbook) | 8 | — |
| Spell compendium (browse/add) | ~230 (wizard-only, unchanged from original) | full PHB list is class-scoped |

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
- **Blade Ward removed.** The original sheet had Blade Ward tagged
  "racial" on Tor's Earth Genasi. Real Earth Genasi (Monsters of the
  Multiverse p.17) do not grant Blade Ward — this looks like a homebrew
  addition or a mistake in the original file. It was dropped during the
  refactor rather than carried forward as species data. If it was
  intentional, re-add it as a feat or a house-rule note.
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
