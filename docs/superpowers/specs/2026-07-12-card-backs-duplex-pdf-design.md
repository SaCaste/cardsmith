# Card Backs + Duplex PDF Export — Design Spec

**Date:** 2026-07-12
**Status:** Approved by user (design conversation, this date)
**App:** CardSmith — single-file browser card maker (`index.html`, React 18 UMD class component, `h = React.createElement`, jsPDF, no build step, no test framework)

## Goal

Let the user design **one shared card back per deck** and export **double-sided PDF cut sheets** (interleaved duplex, long-edge flip) so a printed deck is actually playable.

## Decisions (from brainstorming)

| Question | Decision |
|---|---|
| Back model | **One back per deck** (no per-card overrides) |
| Back editing | **Full existing editor** via a Front/Back flip toggle — not a simplified designer |
| Duplex layout | **Interleaved**: fronts on odd pages, x-mirrored backs on even pages (long-edge flip) |
| Mixed card sizes | Back designed once; **scaled proportionally (cover fit, centered)** to each card at export |
| Architecture | **Approach A**: separate `state.back` object + `state.side` toggle (not a flagged card inside `cards[]`, not a per-card backs map) |

## 1. Data model

- `state.back` — a card-shaped object `{ w, h, bg, border, elements }`. No `name`, `qty`, or card `id` (elements keep their own ids as usual).
- Created **lazily on first flip**, sized to the dimensions of the card currently on canvas.
- `state.side: 'front' | 'back'` — which side the canvas shows. Initial value `'front'`.
- **Default back design** (created on first flip, and for v1 project files): not blank white — a solid background in the current accent tone with one centered icon (the existing `star` icon from the library), so an undesigned back still prints looking intentional.
- **Undo history**: snapshots grow from capturing `cards` to capturing `{ cards, back }` so undo/redo works seamlessly across sides. Restoring a snapshot restores both.

## 2. Editing — flip toggle

- A **Front/Back segmented toggle** near the canvas header.
- Flipping:
  - clears element selection (`selEl`),
  - commits any in-progress inline text edit (same commit path as clicking away),
  - creates `state.back` with the default design if it does not exist yet.
- `curCard()` returns `state.back` when `side === 'back'`. Because selection, drag/resize/rotate, inline text editing (double-click / Enter / F2), the icon library picker, element property panels, and z-order all flow through `curCard()` + `updateSelEl`/`addEl`/delete, they work on the back **without per-feature changes**. Any write path that indexes into `state.cards` directly must route through the same side check.
- While on the back: deck strip and card-only controls (qty, name, duplicate card, card template picker) are hidden or dimmed.
- CSV Batch and the Series Generator iterate `state.cards`, which never contains the back — they remain front-only **by construction**; no filtering code needed anywhere.

## 3. Rendering the back at each card's size (export)

- For a card of size `cardW × cardH` and a back of size `backW × backH`:
  - `s = max(cardW / backW, cardH / backH)` (**cover** fit),
  - render via `drawCard(back, ppm × s)` to an offscreen canvas,
  - center-crop that canvas onto a `(cardW + 2B) × (cardH + 2B)` target canvas (`B` = existing 3 mm bleed).
- Uniform-size decks (the common case) give `s = 1` — pixel-exact, no resampling.
- Bleed is preserved: cover fit guarantees the target canvas (including bleed area) is fully covered.

## 4. Duplex PDF export

- Export panel gains a **“Double-sided (card backs)” checkbox** applying to both `PDF · Letter` and `PDF · A4`.
- `exportPDF` changes:
  - the existing row-packing loop records each placed front's `(x, y, w, h)` for the current page;
  - when a fronts page is full (and at the end), if duplex is on, emit a **backs page**: for each recorded front, place the scaled back at `x' = pageW − x − w`, same `y` — correct mirroring for a **long-edge flip**;
  - cut marks are drawn on backs pages with the identical existing logic.
- Checkbox **off** → output identical to today's export (regression guarantee).
- `ensureImages()` must also preload image elements of `state.back` before PDF/PNG export.
- Free bonus (no extra code): with the canvas flipped to Back, the existing **PNG card** button exports the back, because it uses `curCard()`.

## 5. Save / Load

- `saveJSON`: bump to `version: 2`, include `back` alongside `cards`, `theme`, `accent`, `assets`.
- `loadJSON`: accept v1 files (no `back` field) by generating the default back. Existing project files keep working unchanged.

## 6. Edge cases

- Flip with zero cards: allowed — the back is deck-level. Lazy creation sizes it to the default card size in that case.
- Deleting all cards keeps the back.
- The back never appears in deck counts, CSV batch, series generation, PNG-all, or the fronts of the PDF.
- Rotated elements, gradients, images, borders, corner radius on the back: same `drawCard` path as fronts — nothing new.

## 7. Verification (browser smoke checklist — no test framework in this project)

1. Flip → default back appears; add/edit text, icon, image; flip back — front intact; flip again — back intact.
2. Undo/redo crosses sides correctly (edit front, edit back, undo twice restores both in order).
3. Duplex PDF, uniform deck: print or overlay pages 1–2 — card outlines align when mirrored (long-edge flip check).
4. Duplex PDF, mixed sizes (e.g. Poker + Mini in one deck): every back covers its card fully, centered, no white slivers inside cut marks.
5. Duplex checkbox off → export identical to current behavior.
6. Load a v1 project JSON → loads without errors, default back present.
7. PNG card while flipped to Back exports the back design.

## Out of scope

- Per-card back overrides (explicitly rejected).
- Fold-and-glue and backs-only-sheet layouts (may be future work).
- Snapping & alignment guides — next feature, separate spec.
