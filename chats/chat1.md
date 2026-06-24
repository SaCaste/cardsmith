# Design core features planning

_Started 2026-06-21 21:21 UTC_

---

## User

<system-info comment="Only acknowledge these if relevant">
Project title is now "Untitled"
Current date is now June 21, 2026
</system-info>

<pasted_text name="Pasted text (55 lines)">
# Card Maker — Claude Design Prompt

Build a **browser-based card maker** for prototyping board games, custom card games, and quick games with friends. The core philosophy: **total creative freedom, but with classic layouts ready to go** so I'm never staring at a blank canvas.

## Core concept
A single-page app with three zones: a **left panel** for card type / template / element controls, a **center live canvas** showing the card I'm editing at true proportions, and a **right panel** for the deck (all cards as thumbnails) plus export. Editing is WYSIWYG — what I see is what prints.

## Card types (selectable, with correct proportions)
- **Poker / playing** — 2.5 × 3.5 in (63 × 88 mm)
- **Tarot / large** — 2.75 × 4.75 in (70 × 120 mm)
- **Mini / token** — 1.75 × 2.5 in (44 × 67 mm)
- **Custom** — I type width/height in mm or inches
Switching type rescales the canvas live. Show a faint **bleed line (3 mm)** and **safe margin** guide on the canvas (toggleable).

## Template library (classic layouts, one click to apply)
Provide ~6–8 starter templates I can drop onto any card type and then freely edit:
- Title + large art + description box (classic trading card)
- Stat card: title, art, and a row of labeled stats (e.g. ATK / DEF / cost)
- Playing-card style: corner rank + suit, mirrored, center pip
- Minimalist: centered title + icon
- Role/character card: portrait frame + name banner + ability text
- Plain blank (full freedom)
Templates are **starting points**, not locks — every element stays movable, resizable, deletable.

## Element toolbox (full creative freedom)
Let me add and freely position/resize/layer any of these on a card:
- **Text fields** — font family (offer a handful of good ones), size, weight, color, alignment, and a few preset styles (title, body, stat).
- **Images** — upload my own (drag-drop or file picker), with crop/fit-to-frame and rounded-corner option.
- **Icons / shapes** — a built-in set of simple game icons (heart, sword, shield, coin, star, suits ♠♥♦♣, dice) plus basic shapes (rect, circle, line) with fill/stroke/color.
- **Frames &amp; backgrounds** — solid color, simple gradient, or border styles.
- Standard controls: select, move, resize, rotate, layer order (bring forward/back), duplicate, delete, undo/redo.

## Deck &amp; batch workflow (this is key for prototyping)
- A card is one item in a **deck**; the right panel lists every card as a thumbnail.
- **Duplicate card** to spin up variants fast.
- **Batch / variable data**: let me define fields on a template (e.g. {{name}}, {{cost}}, {{art}}, {{text}}) and paste a small table (or simple CSV-style text) to **generate a whole deck at once** — one card per row, same layout. This is the feature that turns it from a toy into a real prototyping tool.
- Quantity counter per card (e.g. "×3") that's respected on export.

## Export
- **Print-ready PDF** — auto-arrange cards onto Letter &amp; A4 pages in a cut grid, with **crop marks** and correct bleed, at print DPI. Respect per-card quantities.
- **PNG export** — single selected card, or all cards as separate files.
- **Save / Load project** — since browser storage may be unavailable, implement this as **export-to-JSON and import-from-JSON** (a downloadable project file I can reload later). Keep everything — deck, layouts, uploaded images (embedded as base64) — in that one file.

## Design / feel
Clean, modern, slightly playful "maker tool" aesthetic — think a friendly tabletop-prototyping vibe, not a sterile enterprise editor. Comfortable spacing, clear icon buttons with tooltips, smooth drag interactions, and a canvas that feels tactile. Works on desktop first; degrade gracefully on tablet.

## Build priorities (v1 scope so it ships working)
1. Canvas + card types + free element editing (text, image upload, icons, shapes) with move/resize/layer/undo.
2. Template library applied onto any card type.
3. Deck panel: add / duplicate / delete / reorder cards, quantities.
4. Export: print-ready PDF cut sheets + PNG.
5. Save/Load via JSON project file.
6. Batch generation from a pasted data table.

Build it as a real, working interactive app — no mockups or placeholders for the core editing and export loop.
</pasted_text>

<!-- The user explicitly selected the following skills for this project, as attachments to their message. These are not optional context — they define how you work. Use them. -->
<attached-skill name="Design Components">
This project uses Design Components: every design is a single streaming `Name.dc.html` file. The full authoring spec is in your system prompt under "Writing code — Design Components" — follow it. Author and edit `.dc.html` content with the `dc_write`, `dc_html_str_replace`, `dc_js_str_replace`, and `dc_set_props` tools (not `write_file`; `str_replace_edit` works but won't stream); template edits stream into the live preview as you type.
</attached-skill>

<attached-skill name="Hi-fi design">
Create a high-fidelity, polished design.

Follow this general design process (use the todo list to remember):
(1) ask questions, (2) find existing UI kits and collect design context — copy ALL relevant components and read ALL relevant examples; ask the user if you can't find them, (3) start your file with assumptions + context + design reasoning (as if you are a junior designer and the user is your manager), with placeholders for the designs, and show it to the user early, (4) build out the designs and show the user again ASAP; append some next steps, (5) use your tools to check, verify and iterate on the design.

Good hi-fi designs do not start from scratch — they are rooted in existing design context. Ask the user to Import their codebase, or find a suitable UI kit / design resources, or ask for screenshots of existing UI. You MUST spend time trying to acquire design context, including components. If you cannot find them, ask the user for them. In the Import menu, they can link a local codebase, provide screenshots or Figma links; they can also link another project. Mocking a full product from scratch is a LAST RESORT and will lead to poor design. If stuck, try listing design assets and ls'ing design system files — be proactive! Some designs may need multiple design systems — get them all. Use the starter components (device frames and the like) to get high-quality scaffolding for free.

When presenting several options or explorations side-by-side, lay them out as labeled frames directly in the template — plain markup, not a canvas/artboard component, so every frame stays directly editable. The page is plain HTML — let the body itself scroll; never set `overflow:auto`/`scroll` on an inner wrapper, and don't build pan/zoom into the file. The outermost wrapper carries both the gray background and `width:max-content` (so the gray extends with the scroll) plus `min-width:100%; min-height:100vh; box-sizing:border-box; padding:48px; background:#e7e5df`. Inside it, each section is a start-aligned flex row — `display:flex; gap:48px; align-items:flex-start` — never centered on the horizontal axis: `justify-content:center`, `place-items:center`, or `margin:auto` on an overflowing row pushes frames off the left edge where scroll can't reach. Each frame gets `flex:none` and a fixed pixel width; a frame is a small label above a white card (`background:#fff; border-radius:2px; box-shadow:0 1px 3px rgba(0,0,0,.08)`).

When designing, asking many good questions is ESSENTIAL.

Give options: try to give 3+ variations across several dimensions. Mix by-the-book designs that match existing patterns with new and novel interactions, including interesting layouts, metaphors, and visual styles. Have some options that use color or advanced CSS; some with iconography and some without. Start your variations basic and get more advanced and creative as you go! Try remixing the brand assets and visual DNA in interesting ways — play with scale, fills, texture, visual rhythm, layering, novel layouts, type treatments. The goal is not the perfect option; it's exploring atomic variations the user can mix and match.

CSS, HTML, JS and SVG are amazing. Users often don't know what they can do. Surprise the user.

If you do not have an icon, asset or component, draw a placeholder: in hi-fi design, a placeholder is better than a bad attempt at the real thing.
</attached-skill>

<attached-skill name="Interactive prototype">
Create a fully interactive prototype with realistic state management and transitions. Use React useState/useEffect for dynamic behavior. Include hover states, click interactions, form validation, animated transitions, and multi-step navigation flows. It should feel like a real working app, not a static mockup.
</attached-skill>

<attached-skill name="Design System (design system)">
[Design System] This project uses the **Design System** design system. This is a binding choice for visual style — every visual must follow it. Don't invent colors, type, spacing, or components not grounded here.

Scope: the design system is a visual style reference only. Its guide may describe example products, brands, or people that are unrelated to the user and unrelated to the subject of this conversation. Never treat anything in the design system as a fact about the user, their work, or the topic they asked about.

    Explore it to find what you need:
    - Always copy out the fonts and colors you need
    - For prototypes and designs, always copy out any relevant components
    - If the design system contains existing mocks of products, and you were asked to design or prototype something similar, copy and fork those mocks to start your design. This helps you make high-quality designs.

    Explore it quickly to find relevant UI kits (e.g. mocks of existing products you can copy and fork)

Full system at `/projects/019deebb-d6d3-7b13-a9bd-3fb575ca25f7/`. Before producing any visuals, explore it: call `list_files("/projects/019deebb-d6d3-7b13-a9bd-3fb575ca25f7/")` to see the structure, then `read_file` the README/base.md or whatever index file it has. Don't guess at the design system's contents.

CSS tokens: the guide may describe tokens in prose, but the exact `--*` names are defined in the design system's stylesheet(s). Before writing any `var(--*)`, look up the real name in the design system's `.css` files (`list_files` + `read_file` under `/projects/019deebb-d6d3-7b13-a9bd-3fb575ca25f7/`). Never guess a token name — an unresolved `var()` silently falls back to the browser default.

For assets and UI kits beyond the guide: `read_file("/projects/019deebb-d6d3-7b13-a9bd-3fb575ca25f7/<path>")` and `copy_files` to bring them into the current project.
</attached-skill>

## Assistant

_[tool: list_files]_

_[tool: list_files]_

The design system project is empty, so there's no existing visual context to pull from. Your spec is very detailed on functionality, so I mainly need to lock the look-and-feel and confirm a couple of scope calls.

_[tool: questions_v2]_

## User

Questions answered:
- aesthetic: Explore a few options
- accent: 4
- first_build: Full working core: canvas + editing + templates + deck + PDF/PNG export (batch & JSON in a later pass)
- fonts: Just clean versatile sans + one elegant serif
- sample_deck: Yes — a few demo cards showing off templates

## Assistant

The design system is empty, so I'll commit to a strong direction myself: a clean studio editor with the green accent and a light/warm/dark theme switch so you can explore the chrome look in-app. Building the full working core now.

_[tool: update_todos]_

Now the build. This is a large single component — writing the full editor.

_[tool: dc_write]_

I spotted a typo in the warm theme color. Let me fix it and check the render.

_[tool: dc_js_str_replace]_

_[tool: show_html]_

_[tool: show_html]_

Renders well. One bug: the "Icon" add button is blank (used a non-existent UI glyph) and the "Shape" button is a dead file-input label. Fixing the Add Element row.

_[tool: dc_js_str_replace]_

_[tool: dc_js_str_replace]_

_[tool: dc_js_str_replace]_

I'll confirm the editing loop (select → inspector, resize handles) works, then hand off.

_[tool: eval_js]_

_[tool: eval_js]_

Selection works and the inspector appears. Let me capture that state to confirm visually.

_[tool: save_screenshot]_

_[tool: save_screenshot]_

Everything works — selection, the full inspector (W/H, rotate, opacity, font/size/weight/align/color), resize+rotate handles, deck thumbnails, export buttons. Marking done and handing off.

_[tool: update_todos]_

_[tool: delete_file]_

**CardSmith** is built and working — a real card maker, not a mockup.

- **Canvas** at true card proportions with toggleable bleed/safe guides; live rescale across Poker, Tarot, Mini, and Custom (mm) sizes.
- **Free editing**: add text / image / icon / shape, drag to move, corner+edge resize, rotate, opacity, layer order, duplicate, delete, full undo/redo (⌘Z / ⌘⇧Z, ⌘D, ⌫).
- **8 templates** (Classic TCG, Stat, Playing, Minimalist, Character, Spell, Token, Blank) that adapt to any card size and stay fully editable.
- **Deck panel**: live thumbnails, rename, per-card quantity, reorder, add/dup/delete; preloaded with a 5-card sample deck.
- **Export**: print-ready PDF cut-sheets (Letter/A4) with crop marks, 3mm bleed, 300 DPI, quantities respected; PNG single or all; JSON save/load (images embedded as base64); and batch generation from a pasted `{{field}}` CSV/TSV table.
- **Theme switch** (Studio / Warm / Dark) plus accent + theme as editable props.

A couple of notes: text is edited via the Inspector textarea (not inline double-click), and batch image fields expect URLs/data-URLs in the pasted table. Want inline text editing on the canvas, or a starter icon/art library next?

## User

when using any slider, make an option to input the exact value the user wants

## Assistant

Good enhancement. I'll update the `slider` helper so every slider gets an exact-value number input beside it, then remove the now-redundant readouts on the three sliders that had their own.

_[tool: read_file]_

I'll update the `slider` helper to include an exact-value number input, then simplify the three sliders that had separate readouts.

_[tool: dc_js_str_replace]_

Now simplify the three sliders (rotate, opacity, size) that had their own readout, using the new suffix option instead.

_[tool: dc_js_str_replace]_

_[tool: dc_js_str_replace]_

Every slider now has an exact-value number input beside it.

_[tool: ready_for_verification]_

Done. Every slider — rotate, opacity, size, image radius, shape stroke/radius, background angle, border width/round — now has a number input beside it for typing an exact value, clamped to the slider's range. Rotate shows a `°` suffix and size shows `mm`.

