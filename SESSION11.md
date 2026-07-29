# Session 11 — Playtest polish & QoL (DM feedback FB01–FB07)

Six fixes from a play session (screenshots in `feedback/FB01–FB07.jpg`).
Mostly UI/QoL plus one content feature. **Engine changes allowed.**
Independent of Session 9 pt 3 (party.html) and Session 10 (spell-prep) — but
FB07 is already Session 10 (see cross-ref at the bottom).

Validator gate before every commit: static (516+) AND coverage sweep
(1,345 / 0) at http://localhost:8420/validate.html. **Front the validator
tab before running the sweep** (a hidden/background tab throttles its timer
loop and the sweep looks stalled — tooling quirk, not a failure).

---

## 1. Readability — larger sans-serif body by default (FB06)

**Decision:** change the DEFAULT. Body/UI text becomes a larger, cleaner
**sans-serif**; keep the decorative serif (Cinzel/Papyrus-style) only for
big display headings, the character name, and section titles.

- Introduce a `--font-body` (readable sans) vs `--font-display` (serif) and
  route CSS through them; bump the base `font-size` and `line-height` for
  body copy, card text, notes, table cells, form labels.
- Keep headings (`h1/h2`, character name, `SPELLS`/`CLASS ABILITIES`
  dividers) on the serif for flavor.
- Apply across **all themes** and both the live sheet and exports (the
  export bakes the same CSS, so this is automatic once the stylesheet
  changes).
- No web fonts that break offline — use a system sans stack (or a font
  already inlined). Verify contrast/legibility in each theme.

**Touch:** the `<style>` block (font variables + sizes). **Test:** eyeball
Spells/Combat/Notes at desktop widths in 2–3 themes.

## 2. Spell epithet edit — replace the confusing pop-up (FB01)

**Problem:** tapping a spell card's italic flavor line fires a raw browser
`prompt()` ("Epithet for 'Earth Tremor' …") that appears out of nowhere and
reads as nonsense to a player.

**Fix:** make epithet editing intentional and self-explanatory.
- Remove the whole-line tap trigger; add a small, quiet ✎ affordance on the
  epithet line instead (so it can't fire by accident).
- Replace `prompt()` with a labeled inline editor (or a small themed modal)
  that says what an epithet is: "the flavor subtitle on this card — leave
  blank for the theme default."
- Keep the underlying `S.spellEpithets` behavior.

**Touch:** `renderBaseCard`/`renderCustomCard` (epithet line markup), the
`data-epi` click branch (~line 4107), a small inline-editor helper.

## 3. Inventory item editing (FB02) — BOTH requested

- **Edit button on item cards:** add an ✎ Edit control on each inventory
  card to change qty and the item's fields (name/type/mech/value/desc) in
  place. (Qty already auto-bumps on duplicate adds — this is for arbitrary
  edits, e.g. correcting or annotating an existing stack.)
- **Auto-fill mechanics on add:** when a known item is chosen from the
  typeahead (e.g. Padded Armor), populate the **Mechanical info** field too
  (and any structured `weapon`/`armor` data), not just name/type/value/
  source. Today the structured data is cloned on Add but the visible Mech
  field stays a placeholder — fill it so the player sees it.

**Touch:** the Add-Item typeahead `onSelect` (prefill mech), item-card
render (Edit control), a small edit form/handler reusing the Add-Item
fields. **Watch:** keep `S.equipment` shape + wield/wear effects intact.

## 4. Encyclopedia — hide the empty term editor (FB03)

Only show the right-side Term / Definition / Places / Notes editor when a
homebrew term is selected or being created. When there are no terms and
none selected, hide that panel (the search + "New Term" controls stay).

**Touch:** the Notes tab render (`#loreEditor` visibility), toggle on
select/create/delete.

## 5. Arcane Ward — show only for Abjurers who have it (FB04)

The Arcane Ward block currently renders for everyone (a druid saw it). Gate
it to an **Abjuration-school wizard at the level it's gained** — hide the
whole block otherwise. (2014 rules: Arcane Ward at wizard/Abjuration
level 2; Tor's legacy sheet uses level 3 as a house rule — keep Tor working.
Today the code only checks `wizLvl>=3`, not the subclass, and never hides.)
Death saves stay visible for all characters.

**Touch:** the Combat-tab Arcane Ward container (add a display gate keyed on
Abjuration subclass + level; reuse the existing `wardMax` calc).

## 6. Guide background + custom-background creator (FB05) — BOTH requested

- **Custom-background creator:** an in-app form to define any background —
  name, 2 skill proficiencies, tools, bonus-language count, and a feature
  (name + description) — that becomes selectable like a seed background.
  Store as user data (e.g. `S.customBackgrounds`), merge into the
  Background dropdown and `applyBackgroundPackage`. Give it a neutral
  `BG_PERSONA` fallback so builder roll buttons don't break.
- **Guide background:** add the specific "Guide" background the player uses.
  **OPEN DETAIL — confirm at build time:** its source/skills. It's likely
  non-PHB (the player had to homebrew it on D&D Beyond); if there's no
  canonical 2014 source, enter it via the custom creator with the player's
  own skills/feature rather than inventing mechanics (accuracy rule).

**Touch:** `DATA:BACKGROUNDS`/dropdown build, a creator form + state, merge
into selection + persona + validation. **Watch:** the coverage sweep drives
seed backgrounds only, so custom (runtime) backgrounds won't be swept —
but ensure `applyBackgroundPackage` / persona lookups don't assume a seed
entry exists (guard for a background not in the seed array).

## Cross-ref — FB07 is Session 10

FB07 ("should I be able to pick every spell?" on the druid builder, with the
Reddit note that cantrips are permanent and leveled spells are prepared to a
limit) is exactly **Session 10 changes 1 & 2**: the builder enforces a fixed
cantrip count (permanent) and a limited prepared count for leveled spells;
whole-list preparers browse the full list and cast only prepared. No
separate work here — handled in SESSION10.md.

## Order & ship

Suggested: 4 → 5 → 1 → 2 → 3 → 6 (quick DOM gates first, styling, then the
bigger inventory/background work). Validate (516+ static, sweep 1,345/0) and
browser-verify after each. Commit per fix; `git rm SESSION11.md` in the
final commit.
