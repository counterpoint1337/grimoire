# Data-accuracy audit — burn-down list

Started: Session 9 pt 2 (2026-07-28). A cross-source veracity pass over the
data entered in Sessions 2–8, with strict **2014 vs 2024 edition
separation**. Check items off as they're verified/fixed; add new ones as
found. Fixes that are low-risk get applied immediately (logged under
"Fixed this pass"); anything needing a book in hand stays open.

## Method & sources

- **Edition rule:** the app is 2014-baseline. Class spell lists changed a
  lot in the 2024 PHB, and D&D Beyond now shows 2024 lists by default —
  those are **not** authoritative here. Primary source used:
  `dnd5e.wikidot.com` (2014, and it flags Tasha's-*optional* additions as
  "(Optional)" per spell). Cross-checked against web search aggregating
  kassoon / farreachco / thievesguild for disputed entries.
- **Tagging convention audited against:** each spell's **native** class
  list (PHB, or the spell's own source book for XGE/TCE/EGW/FTD spells).
  Tasha's *optional expanded* class-list additions are deliberately NOT
  tagged. The tricky case: a PHB spell that Tasha's optionally adds a class
  to (e.g. Flesh to Stone + druid) must NOT get that class; but a
  TCE-*origin* spell whose whole list wikidot marks "(Optional)"
  (e.g. Summon Fey) keeps its full native list — that list is the spell's
  actual list, just non-core content the app opts into.

## Fixed this pass (verified against sources)

- [x] **Flesh to Stone** — was `drd wlk wiz`; druid is a Tasha's *optional*
      add to a PHB-native Warlock/Wizard spell (I'd added druid but not
      sorcerer — an inconsistency confirming it was a slip, not a
      deliberate optional-list choice). Corrected to `wlk wiz`. Confirmed
      by wikidot ("(Optional)" on druid & sorcerer) and a second-source
      search.
- [x] **Warding Wind** — was `drd sor wiz`; bard is native (I'd dropped it
      out of caution in Session 8). Corrected to `brd drd sor wiz`
      (wikidot: Bard/Druid/Sorcerer/Wizard, none optional).
- [x] **Mirage Arcane** — was `drd wiz`; bard is native. Corrected to
      `brd drd wiz` (wikidot: Bard/Druid/Wizard, none optional).

## Fixed — untagged under-tag sweep (2026-07-30)

The compendium engine defaults an **untagged** COMP row to wizard-only
(`compClassCodes: c[4]||"wiz"`), so 58 rows carried no class tags. That's
correct for wizard-exclusive spells but silently hides any that also belong
to another class — a whole category the earlier tag-driven audit couldn't
see. Every untagged row with a non-PHB source (TCE/EGW/FTD/SCC/XGE) was
verified against dnd5e.wikidot.com (2014 + the spell's own book). Six were
genuine under-tags (tags added); the rest are legitimately wizard-only and
left untagged (the intended default — not churned).

- [x] **Summon Elemental** (TCE) — untagged→wiz; native list Druid/Ranger/
      Wizard (all "(Optional)" = TCE opt-in, same pattern as Summon Fey).
      Tagged `drd rgr wiz`.
- [x] **Summon Aberration** (TCE) — untagged→wiz; native Warlock/Wizard.
      Tagged `wlk wiz`.
- [x] **Summon Draconic Spirit** (FTD) — was `drd wiz`; native Druid/
      Sorcerer/Wizard. Added sorcerer → `drd sor wiz`.
- [x] **Kinetic Jaunt** (SCC) — untagged→wiz; native Artificer/Bard/
      Sorcerer/Wizard (Artificer n/a in app). Tagged `brd sor wiz`.
- [x] **Nathair's Mischief** (FTD) — untagged→wiz; native Bard/Sorcerer/
      Wizard. Tagged `brd sor wiz`.
- [x] **Danse Macabre** (XGE) — untagged→wiz; native Warlock/Wizard.
      Tagged `wlk wiz`.
- [x] **True Strike** — the row was the **2024** rewrite (Int-based radiant
      weapon-attack cantrip), inconsistent with the 2014 baseline. Reverted
      to the 2014 Divination cantrip (advantage on your next attack) and
      tagged `brd sor wlk wiz` (DM decision, 2026-07-30). **Elementalism**
      kept as-is — it's in Tor's Magic Initiate (Sage) kit.
- Verified wizard-only, left untagged (correct default): Summon Construct
      (Artificer/Wizard), Magnify Gravity, Immovable Object, Fortune's
      Favor, Wristpocket, Gift of Alacrity, Pulse Wave, Gravity Sinkhole,
      Gravity Fissure, Dark Star, Ravenous Void, Time Ravage, Reality Break,
      Tether Essence, Tiny Servant, Wall of Sand, Illusory Dragon, Mighty
      Fortress, Melf's Acid Arrow, **Fire Shield** (its Druid/Sorcerer are
      Tasha's-"(Optional)" — correctly excluded, same rule as Flesh to Stone).

## Verified correct (no change needed)

- [x] **Cleric list** (109 tags) — every tagged name confirmed present on
      the 2014 cleric list; self-audit level-by-level found no over-tags.
      Party-relevant (tempest cleric).
- [x] **Druid list** (144) — all names confirmed real druid spells; only
      Flesh to Stone was a non-native over-tag (now fixed). Party-relevant
      (firbolg druid).
- [x] **Paladin (46) & Ranger (53)** — fully self-audited level-by-level
      against the 2014 lists (incl. XGE/TCE/FTD ranger additions like
      Zephyr Strike, Summon Fey, Ashardalon's Stride); complete & correct.
- [x] Spot-checked per-spell native lists: Cause Fear (wlk wiz ✓),
      Dominate Beast (drd sor ✓, ranger is optional — correctly omitted),
      Gate (clr sor wiz ✓, warlock optional — omitted), Insect Plague
      (clr drd sor ✓), Wall of Water (drd sor wiz ✓), Skywrite (brd drd
      wiz ✓), Summon Fey (drd rgr wlk wiz ✓ — TCE-origin).
- [x] **ABILITIES numbers** spot-checked against PHB: Bardic Inspiration
      (Cha-mod uses, d6→d12), Battle Master (4 dice, d8→d10→d12), Lay on
      Hands (5 × level pool), Divine Sense (1 + Cha mod), Divine
      Intervention (d100 ≤ level), Wrath of the Storm (2d8, 1 + Wis mod),
      Deflect Missiles (1d10 + Dex + level), Slow Fall (5 × level),
      Eldritch Invocation level/pact prereqs — all accurate.

## Open items (need a book in hand or a fuller pass)

- [ ] **Under-tag sweep (recoverable, not bugs).** The audit prioritised
      *over*-tags (invented class membership — the cardinal error) and
      found only Flesh to Stone. Two slices of the symmetric *under*-tag
      pass are now done: (a) the **untagged** rows that silently default to
      wizard — fully swept 2026-07-30, six real under-tags fixed (see
      "Fixed — untagged under-tag sweep"); (b) Summon Draconic Spirit's
      missing sorcerer, caught incidentally. **Still open:** a per-spell
      *missing-native-class* pass over the ~400 rows that already carry
      tags — wikidot's class-LIST pages inline the Tasha's-optional spells
      without per-row flags, so it needs per-spell checks. Bard/sorcerer/
      warlock full lists were self-audited (party doesn't use them) but not
      per-spell source-verified end to end.
- [ ] **BOMT still parked** (Book of Many Things): Rewarded / Ruined
      backgrounds + Cartomancer feat — mechanics couldn't be stated
      confidently in Sessions 4–5; enter from the book when available.
- [x] **True Strike / Elementalism** — resolved 2026-07-30. True Strike
      reverted to its 2014 form (`brd sor wlk wiz`, advantage on next
      attack). Elementalism kept (it's in Tor's Magic Initiate kit); its
      summary still notes "(2024)".

## Subclass mechanical gaps (candidates for a future engine session, not bugs)

- [ ] EK / Arcane Trickster / Psi Warrior third-caster & psionic-die
      tracking is reference-text only (noted on those subclasses).
- [ ] Subclass numeric features beyond milestone text (e.g. Draconic
      Bloodline 13 + Dex AC) aren't mechanical.
  These are already logged in PLAN.md "future ideas"; listed here so the
  audit is complete.
