---
title: foundation pitch canonical propagation
type: reference
source: relocated från memory/foundation_pitch_canonical_propagation.md (2026-06-25 memory-consolidation)
last_updated: 2026-06-25
---

# Pitch shared assets — canonical source + propagation script

**Status:** foundation 2026-05-23 — etablerat efter Lucas-val av option C (foundation-tänk över symlink/lazy-fix).

## Single source of truth

`operational/projekt/villa-sjovik/pitchar/_canonical/` är canonical pitch-template.

Alla shared assets ska ha sin master-version där:
- `logo3d.js` (Three.js multi-color render-pipeline med Z-fighting-fix)
- `generate-3d-logo.mjs` (CLI för logo-prep, om delad)
- HTML-template-block om delade (canonical pitch-mall)

Nya pitchar bygger via pitch-builder-agent som **kopierar** från `_canonical/`. GitHub Pages stödjer inte symlinks, så symlink-approach är inte ett alternativ — kopiering är canonical.

## Propagation till existing pitchar

`system/scripts/tools/propagate-pitch-asset.mjs` — propagerar uppdateringar selektivt.

```bash
# Visa vad som skulle hända, inga ändringar
propagate-pitch-asset.mjs --asset logo3d.js --to all --dry-run

# Uppdatera bara specifika pitchar (force krävs eftersom de är modifierade)
propagate-pitch-asset.mjs --asset logo3d.js --to bergdalahyttan,vigneron --force

# Uppdatera alla utom frozen (signed/rejected/lost-deals)
propagate-pitch-asset.mjs --asset logo3d.js --to all --force --skip-frozen
```

**Default-beteende (utan `--force`):** Skippar pitchar med modifierad fil. Detta respekterar `feedback_skickade_pitchar_frozen.md` — vi backportar INTE till skickade pitchar.

**Med `--force`:** Skriver över oavsett modifikation.

**Med `--skip-frozen`:** Läser `deals.json` och skippar pitchar med status `signed | rejected | lost | no_response`.

## När använda

| Scenario | Action |
|---|---|
| Buggfix i nya pitchar | Edit `_canonical/<asset>` — nya pitchar inheritar automatiskt via pitch-builder |
| Buggfix tillbaks i pågående pitchar (status `pitched`) | `propagate-pitch-asset.mjs --to <pitch1,pitch2> --force` |
| Vill backporta till alla utom frozen | `propagate-pitch-asset.mjs --to all --force --skip-frozen` |
| Aldrig: backporta till signed/rejected | `feedback_skickade_pitchar_frozen.md` säger inte |

## Status per 2026-05-23

Z-fighting-fix (per-path z-offset + renderOrder + polygonOffset) är:
- ✅ Applied i `_canonical/logo3d.js`
- ✅ Applied i `einhell/logo3d.js` (där buggen upptäcktes av Lucas)
- ⊘ Inte applied i 28 existing pitchar (de är frozen eller pre-Z-fighting-bug)

Multifärgs-loggor (SVG med flera `fill`-färger per path) kommer få Z-fighting fritt från och med näst nya pitch.

## Föregångare

- `feedback_skickade_pitchar_frozen.md` — pitchar är frozen efter skick
- `feedback_pitch_template_latest_is_canonical.md` — senaste pitch är canonical-design
- `feedback_optimize_for_trajectory.md` — foundation byggs ENGÅNG, optimize för trajectory

Foundation-changeen 2026-05-23 är `_canonical/` som single source of truth + propagation-script — undviker att samma fix måste appliceras 30+ ggr manuellt i framtiden.
