# Sol3D

(Formerly SeRecord. The repo name and internal storage keys keep the old name so existing data keeps working.)

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
| `3D_INV_v1` | filament stock: `{ purchases: [{ id, date, brand, type, color, hex, weight, spools, amount, basis, taxMode, total, each }], used: { "brand\|type\|weight": n }, brands, types }`. Stock is grouped into buckets by brand + material + spool size; on hand = spools bought − used. `total`/`each` are final costs incl. 13% tax. v2.1 `items` are converted to purchases once (originals kept in `legacyItems`). |
| `SeRecord_meta` | UI prefs + last backup time (safe to lose) |
| `SeRecord_preRestore` | copy of data from before the last JSON restore |

A backup file is `{ config, sales }` plus optional extra fields (`inventory` since v2.1). Restoring a backup without `inventory` keeps the current stock. Rules for future changes:
- Never rename the two `*_v21` keys or remove fields. Only add new optional fields.
- Older sales may lack `manualDate`, so always read dates through `dateKey()`.
- Anything read from storage or a backup goes through `normalizeConfig()` / `normalizeSale()`.

## Updating the live app
Push to `main` and GitHub Pages redeploys. The service worker is network-first, so phones get the new version on the next launch while online.
