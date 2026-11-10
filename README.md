# SeRecord

Offline-first sales tracker for 3D print products. Built for iPhone (Safari / Home Screen app). It's plain HTML, CSS and JS with no build step, hosted on GitHub Pages.

## Files
- `index.html`: the whole app (UI + logic)
- `sw.js`: service worker (network-first, works offline)
- `manifest.json` + `icons/`: Home Screen app name and icon

## Data (do not break this)
All data lives in the device's `localStorage`:

| Key | Shape |
|---|---|
| `3D_CFG_v21` | `{ "Product": { "Variety": defaultPrice } }` |
| `3D_SLS_v21` | `[{ id, cat, var, qty, unit, total, manualDate: "YYYY-MM-DD", ts, rawTs }]` |
| `SeRecord_meta` | UI prefs + last backup time (safe to lose) |
| `SeRecord_preRestore` | copy of data from before the last JSON restore |

A backup file is `{ config, sales }` plus optional extra fields. Rules for future changes:
- Never rename the two `*_v21` keys or remove fields. Only add new optional fields.
- Older sales may lack `manualDate`, so always read dates through `dateKey()`.
- Anything read from storage or a backup goes through `normalizeConfig()` / `normalizeSale()`.

## Updating the live app
Push to `main` and GitHub Pages redeploys. The service worker is network-first, so phones get the new version on the next launch while online.
