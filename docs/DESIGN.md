# Design Document

**Product:** VIX Strategy
**Version:** 1.2.1
**Last Updated:** 2026-08-25

Single source of truth for the visual system. Every token listed here is defined in
`assets/css/styles.css` under `:root` unless the row says otherwise. There is no
preprocessor, no design-token build step, and no second stylesheet: the CSS file is the
implementation and this document describes it.

Prose in this document follows the project writing style recorded in
[PRD.md](PRD.md#writing-style). No em dashes.

---

## Design Philosophy

VIX Strategy uses a dark financial terminal aesthetic to signal precision and data
authority. Every visual decision reinforces that this is a live data tool, not a
marketing page. Clarity and legibility take strict priority over decoration. Motion is
used only to signal live data state, never decoratively.

Three rules follow from that philosophy and are worth stating separately, because they
are what a contributor actually has to obey:

1. **Monospace means data.** Anything that is a number, a ticker, a label, a badge, a
   button, or a nav item is set in `--font-mono`. Anything that is a sentence is set in
   `--font-body`. There is no third case.
2. **Green means live and correct.** `--accent-green` (`#00ff88`) is reserved for
   current state: the active nav link, the live VIX number, the active tier row, focus
   rings, and primary calls to action. It is never used decoratively, because a green
   element that means nothing weakens every element that does.
3. **The page is dark and stays dark.** There is no light mode and no theme toggle.
   This is a deliberate product decision, recorded under Explicitly Deferred in
   [PRD.md](PRD.md#explicitly-deferred), not an omission.

---

## Color Palette

All colors are defined as CSS custom properties in `assets/css/styles.css`.

### Backgrounds

| Token | Hex | Use |
|-------|-----|-----|
| `--bg-primary` | `#0a0e1a` | Page background |
| `--bg-secondary` | `#0d1220` | Alternate sections (`.section-alt`), VIX feed header, footer |
| `--bg-card` | `#111827` | Cards, table rows, accordion body, form inputs |
| `--bg-card-hover` | `#1a2235` | Card, table row, legend item, and accordion summary hover state |

### Text

| Token | Hex | Use |
|-------|-----|-----|
| `--text-primary` | `#ffffff` | Headings, key data values, table cell text |
| `--text-secondary` | `#9ca3af` | Body copy, descriptions, section leads, nav links at rest |
| `--text-muted` | `#6b7280` | Timestamps, sublabels, fine print, table notes, footer copyright |

### Accent Colors

| Token | Hex | Use |
|-------|-----|-----|
| `--accent-green` | `#00ff88` | Primary brand accent; active states, CTAs, live VIX value, active nav link, input focus ring, SPY ticker |
| `--accent-amber` | `#f59e0b` | Warnings, CACHED and LOADING badges, risk section heading, loading status text, QQQ ticker |
| `--accent-blue` | `#3b82f6` | BIL ticker color; informational |
| `--accent-red` | `#ef4444` | STALE and ERROR badges, error status text, form error status, TQQQ ticker |
| `--accent-orange` | `#f97316` | Tier 4 (High Fear) indicator only |

### Borders

| Token | Value | Use |
|-------|-------|-----|
| `--border` | `rgba(255,255,255,0.08)` | Default border on cards, sections, table wrappers, inputs, legend items |
| `--border-accent` | `rgba(0,255,136,0.3)` | Green-tinted border on the active nav link and the hero gauge |

### Ticker Colors

These must match the `color` field of each entry in the `TICKERS` object in
`assets/js/strategy.js` and the `color` field of each entry in `CATEGORIES` in
`assets/js/custom.js`. All three locations currently agree.

| Token | Hex | Ticker | Custom-page category |
|-------|-----|--------|----------------------|
| `--color-bil` | `#3b82f6` | BIL | Risk Off |
| `--color-spy` | `#00ff88` | SPY | Diversify |
| `--color-qqq` | `#f59e0b` | QQQ | Risk On |
| `--color-tqqq` | `#ef4444` | TQQQ | Full Risk |

> **Known duplication.** The same four hex values are written out in three places: the
> CSS tokens above, `TICKERS` in `strategy.js`, and `CATEGORIES` in `custom.js`. The JS
> copies exist because chart segments, legend swatches, and inline `style` attributes
> are set from JavaScript, which cannot read a CSS custom property without a
> `getComputedStyle` call. Changing a ticker color means editing all three. Nothing
> enforces that they stay in sync.

### Tier Colors

| Token | Hex | Tier | Notes |
|-------|-----|------|-------|
| `--tier1-color` | `#3b82f6` | Tier 1, Low Fear | Same blue as BIL |
| `--tier2-color` | `#10b981` | Tier 2, Moderate Fear | Emerald, used nowhere else |
| `--tier3-color` | `#f59e0b` | Tier 3, Elevated Fear | Same amber as QQQ |
| `--tier4-color` | `#f97316` | Tier 4, High Fear | Only use of the `--accent-orange` value |
| `--tier5-color` | `#ef4444` | Tier 5, Extreme Fear | Same red as TQQQ |

The ramp is deliberate: cool blue at complacency, warming through green and amber to
orange and red at crisis. Tier 2 is the only green in the ramp and is intentionally
`#10b981` rather than `--accent-green`, so that "moderate fear" never reads as the
brand's live-data green.

### Colors Hardcoded Outside the Token System

These are real values in the codebase that bypass `:root`. They are listed so a
contributor changing the palette knows where else to look.

| Value | Location | Purpose |
|-------|----------|---------|
| `#00ff88` | `assets/js/chart.js`, `centerTextPlugin` | VIX number drawn in the donut hole |
| `#6b7280` | `assets/js/chart.js`, `centerTextPlugin` | "VIX" sublabel under that number |
| `rgba(0,255,136,0.5)` | `assets/js/chart.js` | Canvas shadow glow on the center number |
| `#0a0e1a` | `assets/js/chart.js`, `borderColor` | Gap between donut segments, matches page background |
| `#111827`, `#9ca3af`, `#ffffff`, `rgba(255,255,255,0.1)` | `assets/js/chart.js`, tooltip config | Chart.js tooltip surface, title, body, border |
| `#00e87a` | `.btn-primary:hover` in `styles.css` | Brighter green on hover; no token exists for it |
| `rgba(10,14,26,0.96)` | `.site-header` | Translucent header over `--bg-primary` |
| `#0d1220`, `#0a1628` | `.hero-section` gradient | Hero background gradient stops |
| `#ffffff`, `#b0b8c8` | `.hero-headline` gradient | Gradient text fill on the hero headline |
| `#000` | `.btn-primary` color | Black text on the green button |
| `#00ff88` | `assets/img/logo.svg`, both `fill` and `stroke` | Brand mark |

Canvas drawing cannot use CSS custom properties, so the `chart.js` values are
unavoidable without a `getComputedStyle` lookup at init time. The rest are avoidable and
are noted as minor debt.

---

## Typography

### Font Families

| Token | Stack | Use |
|-------|-------|-----|
| `--font-body` | `-apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif` | Body paragraphs, descriptions, section leads, headings h1 to h3 |
| `--font-mono` | `'Courier New', 'Lucida Console', monospace` | All numeric data, labels, nav, badges, buttons, table headers, form inputs, tier banners |

No web font is loaded. Both stacks resolve to fonts already present on the user's
system, which is why the site makes zero font requests and shows no flash of unstyled
text. Adding a web font would be a meaningful change to the loading profile and should
be weighed against that.

### Type Scale

| Role | CSS size | Computed | Weight | Font | Line height | Notes |
|------|----------|----------|--------|------|-------------|-------|
| Hero headline (`.hero-headline`) | `clamp(2.5rem, 6vw, 4.25rem)` | 40 to 68px | 900 | Body | 1.05 | Gradient text fill, `index.html` only |
| H1 | `clamp(2rem, 5vw, 3.5rem)` | 32 to 56px | 700 | Body | 1.15 | Element default. The only h1 in the codebase is `.hero-headline`, which overrides the size |
| H2, section title | `clamp(1.5rem, 3vw, 2.25rem)` | 24 to 36px | 700 | Body | 1.15 | Green 48x3px underbar via `::after`, suppressed inside `.section-header` |
| H3 | `1.25rem` | 20px | 700 | Body | 1.15 | Card titles |
| H4 (element default) | `1rem` | 16px | 700 | Body | 1.15 | Bare `h4` rule in the base typography block |
| H4 inside accordion (`.accordion-content h4`) | `0.78rem` | 12.5px | 700 | Mono | 1.15 | Uppercase, 0.1em tracking, `--accent-green`. This is the only h4 that actually appears in the markup |
| Body | `1rem` | 16px | 400 | Body | 1.6 | Set on `body` |
| Section lead (`.section-lead`) | `1.1rem` | 17.6px | 400 | Body | 1.6 | Color `--text-secondary`, max-width 720px |
| Hero subheadline | `1.1rem` | 17.6px | 400 | Body | 1.75 | Max-width 560px |
| VIX large number (`.vix-number`) | `clamp(4rem, 10vw, 6rem)` | 64 to 96px | 700 | Mono | 1 | `strategy.html` and `custom.html`; pinned to `3.5rem` under 480px |
| Hero gauge value (`.gauge-value`) | `3.25rem` | 52px | 700 | Mono | 1 | `index.html` only |
| Card body copy | `0.9375rem` | 15px | 400 | Body | 1.6 | Color `--text-secondary` |
| Table body and data values | `0.9375rem` | 15px | 400, or 600 mono for `.data-value` | Mixed | 1.6 | Drops to `0.875rem` under 768px |
| Table headers | `0.72rem` | 11.5px | 700 | Mono | 1.6 | Uppercase, 0.1em tracking, `--text-secondary` |
| Tier label cell (`.tier-label`) | `0.875rem` | 14px | 400 | Mono | 1.6 | |
| Fear sublabel (`.fear-label`) | `0.72rem` | 11.5px | 400 | Body | 1.6 | Block element under the tier range |
| Nav links | `0.8rem` | 12.8px | 600 | Mono | 1.6 | 0.04em tracking; `0.72rem` under 768px |
| Nav title | `1.05rem` | 16.8px | 700 | Mono | 1.6 | 0.05em tracking; `0.9rem` under 768px |
| Buttons | `0.85rem` | 13.6px | 700 primary, 600 secondary | Mono | 1.6 | 0.05em tracking; `.btn-large` raises to `0.95rem` |
| Badges | `0.68rem` | 10.9px | 700 | Mono | 1.6 | Uppercase, 0.1em tracking |
| Tier banner | `clamp(0.8rem, 2vw, 1rem)` | 12.8 to 16px | 700 | Mono | 1.6 | 0.04em tracking; `0.78rem` under 480px |
| Ticker badge (`.ticker-badge`) | `0.8rem` | 12.8px | 700 | Mono | 1.6 | Border uses `currentColor` |
| Legend symbol and percentage | `1rem` | 16px | 700 and 400 | Mono | 1.6 | |
| Form label (`.customize-field label`) | `0.8rem` | 12.8px | 700 | Mono | 1.6 | Colored per category |
| Form field description | `0.78rem` | 12.5px | 400 | Body | 1.6 | `min-height: 2.2em` so inputs align across the grid |
| Form input (`.customize-input`) | `1rem` | 16px | 700 | Mono | 1.6 | Uppercase transform, 0.04em tracking. 16px is deliberate: iOS Safari zooms the viewport on focus for any input below 16px |
| Form status (`.customize-status`) | `0.85rem` | 13.6px | 400 | Body | 1.6 | Hidden via `:empty` when there is no message |
| Table note (`.table-note`) | `0.85rem` | 13.6px | 400 | Body | 1.6 | `--text-muted` |
| Disclaimer text (`.disclaimer-text`) | `0.85rem` | 13.6px | 400 | Body | 1.6 | `--text-muted` on a red-tinted panel |
| Footer disclaimer | `0.8rem` | 12.8px | 400 | Body | 1.6 | Max-width 620px, centered |
| Footer copyright | `0.72rem` | 11.5px | 400 | Mono | 1.6 | Uses `!important` to beat the footer paragraph rule |
| Gauge sublabels | `0.62rem` to `0.65rem` | roughly 10px | 400 | Mono | 1.6 | Uppercase, 0.12em to 0.18em tracking |
| Insight stat value | `1.875rem` | 30px | 700 | Mono | 1.15 | |
| Insight quote | `1.05rem` | 16.8px | 400 italic | Body | 1.6 | Green 3px left border |
| Loading status text (`.vix-status-text`) | `clamp(1.1rem, 3vw, 1.75rem)` | 17.6 to 28px | 700 | Mono | 1 | Replaces the large VIX number while a fetch is in flight |
| Loading status text, hero (`.gauge-status-text`) | `1rem` | 16px | 700 | Mono | 1.4 | Same role in the hero gauge; also disables `gauge-pulse` |

---

## Spacing System

Base unit: **4px** (0.25rem). All layout spacing uses one of these seven tokens. Where a
value falls between tokens the CSS uses a raw rem value; those exceptions are listed
below the table.

| Token | Value | Pixels |
|-------|-------|--------|
| `--sp-xs` | `0.25rem` | 4px |
| `--sp-sm` | `0.5rem` | 8px |
| `--sp-md` | `1rem` | 16px |
| `--sp-lg` | `1.5rem` | 24px |
| `--sp-xl` | `2rem` | 32px |
| `--sp-2xl` | `3rem` | 48px |
| `--sp-3xl` | `4rem` | 64px |

**Documented exceptions.** Component-internal padding that needs optical rather than
grid alignment uses raw values: buttons (`0.6rem 1.2rem`, large `0.875rem 1.75rem`), nav
links (`0.45rem 0.875rem`, `0.35rem 0.55rem` on mobile), badges (`0.2rem 0.6rem`),
ticker badges (`0.15rem 0.45rem`), and form inputs (`0.65rem 0.875rem`). These are
intentional and should not be "corrected" onto the scale; they are tuned to the
monospace cap height.

### Layout Widths

| Value | Where | Purpose |
|-------|-------|---------|
| `1100px` | `.container`, `.nav-container` | Maximum content width, centered |
| `720px` | `.section-lead` | Maximum measure for lead paragraphs |
| `700px` | `.hero-content` | Hero column |
| `620px` | `.footer-disclaimer p` | Maximum measure for footer fine print |
| `560px` | `.hero-subheadline` | Maximum measure for the hero subheadline |
| `375px` | `body { min-width }` | Hard floor. The layout is not designed below this |

Measure caps exist so that body copy never exceeds roughly 75 characters per line at the
1100px container width.

### Border Radius

| Token | Value | Use |
|-------|-------|-----|
| `--r-sm` | `4px` | Buttons, badges, nav links, ticker badges |
| `--r-md` | `8px` | Accordion content, legend items, form inputs, blockquote, disclaimer box, tier banner |
| `--r-lg` | `12px` | Cards, table wrappers, hero gauge, accordion shell |

One documented exception: `.legend-swatch` uses a raw `3px` radius, because 4px on a
14px square reads as a circle.

### Global Transition

`--transition: all 0.2s ease`, applied to hover states on buttons, cards, table rows,
nav links, legend items, form inputs, and the tier banner. Using `all` is convenient but
animates every property including layout ones. It is acceptable here only because none
of these elements change size on hover except `.btn-primary`, which moves 1px on the
transform axis.

---

## Breakpoints

Four states. The implementation is desktop-first (`max-width` queries), even though the
content order is mobile-sensible.

| Breakpoint | Condition | Layout changes |
|-----------|-----------|----------------|
| Wide (default) | Above 900px | Chart grid: a fixed 360px chart column plus a 1fr legend column. Card grids auto-fit at a 270px minimum (problem and why grids), 220px (customize grid), 180px (insight grid) |
| Tablet | 900px and below | Chart grid collapses to a single column; the chart is constrained to 320px wide and 320px tall and centered |
| Mobile | 768px and below | Nav padding reduced to `--sp-sm --sp-md`; nav title drops to 0.9rem and nav links to 0.72rem; hero minimum height switches from `90vh` to `100svh`; hero gauge wrapper stacks vertically and left-aligns; problem, why, and insight grids force a single column; VIX feed stacks vertically and left-aligns; section padding drops from `--sp-3xl` to `--sp-2xl` and chart section to `--sp-xl`; table cell padding drops to `--sp-sm --sp-md` and font to 0.875rem |
| Small mobile | 480px and below | Container side padding drops from `--sp-lg` to `--sp-md`; the large VIX number is pinned at 3.5rem instead of the clamp; card padding drops to `--sp-lg`; tier banner shrinks to 0.78rem with `--sp-sm --sp-md` padding |

`100svh` on the hero at the mobile breakpoint is deliberate: `100vh` on mobile Safari
includes the area under the collapsing browser chrome, which pushed the hero call to
action below the fold.

The auto-fit grids mean several components reflow before any media query fires. The
customize grid, for example, goes from four columns to two to one purely on available
width. Do not add breakpoints for those; the grid already handles it.

---

## Component Patterns

### Buttons

**Primary (`.btn-primary`):** Solid `--accent-green` background, `#000` text, monospace,
0.05em tracking, `--r-sm`. Used for the single main call to action in a region. Hover:
brighter green (`#00e87a`), a 1px upward `translateY`, and a green glow shadow. Never
place two primary buttons in the same view.

**Secondary (`.btn-secondary`):** Transparent background, `--accent-green` border and
text. Used for utility actions, currently the Refresh button and Reset to Defaults.
Hover: a faint green background tint, no lift.

**Large variant (`.btn-large`):** Applied alongside `.btn-primary` for hero and footer
calls to action. Larger padding and font only, no other change.

Both button classes are styled to work as either a `<button>` or an `<a>`.
`display: inline-block` and an explicit `cursor: pointer` are present for that reason.

### Navigation Links (`.nav-link`)

Pill-shaped and monospace. Default: `--text-secondary`, transparent 1px border (present
at rest so the element does not shift on hover). Hover: `--text-primary`, `--border`
becomes visible, faint white background. Active (`.nav-active`): `--accent-green` text,
`--border-accent` border, `rgba(0,255,136,0.08)` background.

The active class is hardcoded per page in the HTML alongside `aria-current="page"`; no
JavaScript is involved. Adding a page means adding the link to all existing pages and
setting `nav-active` on the correct one in each. There is no shared header partial,
because there is no template system. This is the single most error-prone manual step in
the codebase.

Current nav, in order, on all three pages: About, Dashboard, Custom, Azqato Invests,
Support. The last two are external links with `target="_blank"` and
`rel="noopener noreferrer"`.

### Form Inputs (`.customize-input`)

Introduced in v1.2.0 with the Custom strategy builder; the first and so far only text
input in the app. Background `--bg-card`, `--border` border, `--r-md`, monospace, bold,
uppercase via `text-transform`, 0.04em tracking. Focus: border becomes `--accent-green`
with a `0 0 0 3px rgba(0,255,136,0.12)` glow, and the default outline is removed.

Removing `outline` is only acceptable because the replacement focus indicator is at
least as visible. Never remove an outline without replacing it.

Each field is a `.customize-field` column holding a `<label>` (colored to the category),
a `.customize-field-desc` explanatory span with a `min-height` so inputs align in a row,
and the input itself. The label is bound with `for` and `id`, not by wrapping.

Note that the form's submit button lives outside the `<form>` element and is associated
with it through the `form="customize-form"` attribute, so that it can sit in the
`.customize-actions` row next to the Reset button rather than inside the field grid.

### Cards (`.card`)

Background `--bg-card`, `--border`, `--r-lg`, `--sp-xl` padding. Hover: border shifts to
`rgba(0,255,136,0.18)` and background lightens to `--bg-card-hover`. An `h3` inside gets
`--sp-sm` bottom margin; a `p` inside is `--text-secondary` at 0.9375rem with the bottom
margin zeroed. `.card-icon` is an emoji at 1.5rem with `--sp-md` below it.

Cards hover even when they are not interactive, which is a mild honesty problem in the
UI. It is accepted because the hover is subtle and the grids read better with it.

### Badges

All badges share the `.badge` base class: mono, 0.68rem, weight 700, uppercase, 0.1em
tracking, `--r-sm`, `0.2rem 0.6rem` padding. Every variant is a tinted background at 12
percent alpha, a solid 1px border, and matching text color.

| Class | Color | Use |
|-------|-------|-----|
| `.badge-live` | `--accent-green` | Fresh VIX data; animated `pulse-badge` |
| `.badge-cached` | `--accent-amber` | Value served from cache, and the LOADING state |
| `.badge-error` | `--accent-red` | STALE and ERROR states |
| `.badge-tier1` to `.badge-tier5` | Tier colors | Tier indicator in the hero gauge on `index.html` |

> **Discrepancy, carried from the code.** The status badge text and its class are
> decoupled: `strategy.html` and `custom.html` map the `loading` state to
> `.badge-cached`, and both `stale` and `error` to `.badge-error`. So an amber badge can
> read either CACHED or LOADING, and a red badge can read either STALE or ERROR. This is
> intentional in the code (fewer classes) but means color alone does not identify the
> state. Color is not the only channel here, since the text is always present, so this
> passes the accessibility bar. It is recorded because it surprises readers of the
> stylesheet.

### Tables (`.table-styled` inside `.table-wrapper`)

`.table-wrapper` supplies `overflow-x: auto`, the `--r-lg` radius, and the border, so
that wide tables scroll inside their own box instead of forcing the page to scroll.
Every table in the project must be wrapped this way.

Header: `--bg-secondary` background, mono uppercase 0.72rem in `--text-secondary`,
`white-space: nowrap`. Body rows: `--bg-card` background, hover lightens to
`--bg-card-hover`, last row's bottom border removed. `.data-value` cells are mono at
weight 600.

Tier rows carry a 3px colored left border keyed to a `data-tier` attribute (`tier1`
through `tier5`). The active tier row in the reference table on `strategy.html` gets
`.tier-active-row`, a green background tint and green text, both with `!important`
because it has to beat the `data-tier` left border and the row hover rule.

### Accordion (`.accordion-details`)

Native `<details>` and `<summary>`, no JavaScript. The default disclosure marker is
removed with both `list-style: none` and `::-webkit-details-marker { display: none }`,
and replaced with a `::after` chevron that rotates 180 degrees when `[open]`. Summary is
mono, weight 700, with a hover background. Content area is `--sp-xl` padded,
`--text-secondary`, line-height 1.75, with mono uppercase green `h4` sub-headings.

Native `<details>` is used specifically so the content remains findable by in-page search
and keyboard-reachable without any focus management.

### Tier Banner (`.tier-banner`)

Inline-block, mono, weight 700. Color, border color, and background tint all key off a
`data-tier` attribute set from JavaScript, so a tier change animates through
`--transition` rather than snapping. Default state (no `data-tier` yet) is
`--text-secondary` on `--bg-card`.

> **Discrepancy, carried from the code.** `strategy.html` renders the banner as
> `VIX 15.84 -> VIX 15-25, Moderate Fear` using `getTierLabel()`, while `custom.html`
> renders `VIX 15.84 -> Tier: 2` from a raw string replace and never calls
> `getTierLabel()`. Same component, two different content formats. The `strategy.html`
> version is the better one and `custom.html` should be brought in line, but both are
> shipped and the code is the current reality.

### Chart (Chart.js donut)

Type `doughnut`, `cutout: '68%'`, 3px segment borders in the page background color so
segments read as separated, `hoverOffset: 10`. The built-in legend is disabled; the
legend is real DOM (`.chart-legend`) so it is selectable, translatable, and readable by a
screen reader.

A custom inline plugin, `centerText`, draws the current VIX value and a "VIX" sublabel
into the donut hole on `afterDraw`. It guards on `chart.config.type !== 'doughnut'` and
falls back to `--` when no value is set. Its colors are canvas fill styles and therefore
hardcoded; see the hardcoded-colors table above.

Tooltips are restyled to the dark palette with a mono bold body and a
`  {label}: {value}%` callback. The two leading spaces in that callback are intentional
padding.

### Legend (`.chart-legend`, `.legend-item`)

A vertical stack of rows, each a `--r-md` card with a 14px rounded swatch, the ticker
symbol in mono at 1rem colored to match, and the percentage in `--accent-green` to the
right. Hover lightens the row.

`strategy.html` builds these rows with an `innerHTML` template literal from the trusted
`TICKERS` constant. `custom.html` builds the same rows with `document.createElement()`
and `.textContent`, because the symbol there is user input. Match the page you are
editing; do not "simplify" the Custom page's version back to a template literal. See
[PRD.md](PRD.md#security).

### Page Chrome

**Sticky header (`.site-header`):** `position: sticky`, `z-index: 100`,
`rgba(10,14,26,0.96)` background with a 10px backdrop blur and a bottom `--border`. The
near-opaque background is a fallback for browsers without `backdrop-filter`.

**Scanline overlay (`body::before`):** A fixed, full-viewport repeating linear gradient
that lays a 2px-on, 2px-off black stripe at 3 percent alpha over the entire page at
`z-index: 9999`. It is the single strongest contributor to the terminal look.
`pointer-events: none` is mandatory on it; without that it would swallow every click on
the site.

**Hero glow (`.hero-section::after`):** A 640px radial green gradient at 5 percent alpha,
positioned off the top-right corner, with the section set to `overflow: hidden` so it
crops. Purely atmospheric.

**Hero headline gradient:** White to `#b0b8c8` at 140 degrees, clipped to the text with
`-webkit-background-clip: text` and `-webkit-text-fill-color: transparent`. The
unprefixed `background-clip: text` is also present.

**Section rhythm:** `.section` is `--sp-3xl` vertical padding; `.section-alt` adds the
`--bg-secondary` background and is alternated down the page. `.dashboard-section` is
`--sp-2xl` and adds a top border between consecutive siblings via
`.dashboard-section + .dashboard-section`.

**Section title underbar:** `.section-title::after` draws a 48x3px green bar. It is
suppressed inside `.section-header`, which is the flex row used when a title shares a
line with a control.

### Status Text States

When a network fetch is in flight and there is nothing cached, the large VIX number is
replaced by counter text rather than a spinner. Two class pairs handle the size and
color change so the text fits the space a 96px numeral occupied:

| Class | Applies to | Effect |
|-------|-----------|--------|
| `.vix-status-text` | `.vix-number` on `strategy.html` and `custom.html` | Shrinks to `clamp(1.1rem, 3vw, 1.75rem)`, drops the glow, turns amber |
| `.vix-status-error` | Added on top of the above | Turns red |
| `.gauge-status-text` | `.gauge-value` on `index.html` | Shrinks to 1rem, drops the glow, stops `gauge-pulse` |
| `.gauge-status-error` | Added on top of the above | Turns red |

---

## Accessibility Standards

**Target:** WCAG 2.1 AA.

### Color Contrast

Ratios below were recomputed against the WCAG 2.x relative luminance formula during the
2026-08-25 audit. Where the previously documented figure differed, both are shown.

| Foreground | Background | Ratio | AA normal text (4.5:1) | Note |
|-----------|-----------|-------|------------------------|------|
| `--text-primary` `#ffffff` | `--bg-primary` `#0a0e1a` | 19.3:1 | Pass | Previously documented as 18.9:1 |
| `--accent-green` `#00ff88` | `--bg-primary` `#0a0e1a` | 14.4:1 | Pass | Previously documented as 11.5:1 |
| `--text-secondary` `#9ca3af` | `--bg-primary` `#0a0e1a` | 7.6:1 | Pass | Previously documented as 5.7:1 |
| `--text-secondary` `#9ca3af` | `--bg-card` `#111827` | 7.0:1 | Pass | |
| `--accent-amber` `#f59e0b` | `--bg-secondary` `#0d1220` | 8.7:1 | Pass | |
| `--tier2-color` `#10b981` | `--bg-card` `#111827` | 7.0:1 | Pass | |
| `--tier4-color` `#f97316` | `--bg-card` `#111827` | 6.3:1 | Pass | |
| `--accent-blue` `#3b82f6` | `--bg-card` `#111827` | 4.8:1 | Pass, narrowly | |
| `--accent-red` `#ef4444` | `--bg-card` `#111827` | 4.7:1 | Pass, narrowly | |
| `#000` | `--accent-green` `#00ff88` | 15.7:1 | Pass | Primary button |
| `--text-muted` `#6b7280` | `--bg-primary` `#0a0e1a` | 4.0:1 | **Fail** | Table notes, gauge sublabels |
| `--text-muted` `#6b7280` | `--bg-secondary` `#0d1220` | 3.9:1 | **Fail** | Footer copyright, VIX index label |
| `--text-muted` `#6b7280` | `--bg-card` `#111827` | 3.7:1 | **Fail** | Disclaimer text, accordion chevron |

The three previously documented figures were all understated, which means the palette is
in better shape than the old document claimed on those rows. The original text is
preserved in the Note column rather than deleted, since it may have been measured
against a different pair of colors or a different tool.

> **Open accessibility defect, found in this audit.** `--text-muted` fails AA for normal
> text against all three backgrounds it is used on, and every element using it is set
> below 14px, so the large-text exemption (3:1) does not apply either. The affected copy
> includes the risk disclaimer text on `index.html`, which is the one piece of text on
> the site that most needs to be legible. Raising `--text-muted` to roughly `#8b93a1`
> would clear 4.5:1 on all three backgrounds. This is a code change and was not made
> during this documentation audit; it is logged as open question 4 in
> [PRD.md](PRD.md#risks-and-open-questions).

### ARIA and Semantics

- `aria-live="polite"` on the VIX value, tier banner, allocation table body, chart
  legend, tier reference table body, and the Custom page's save status message.
- `role="status"` on the tier banner.
- `aria-label` on the nav landmark, several section landmarks, the chart canvas, the
  chart wrapper, the Refresh button, and the VIX value.
- `aria-current="page"` on the active nav link.
- `alt` text on the logo image.
- `lang="en"` on `<html>`; `<main>`, `<header>`, `<footer>`, and `<section>` used as
  landmarks throughout.

The `aria-live` regions are deliberately `polite`, not `assertive`. A VIX tick is not
urgent and should never interrupt a screen reader mid-sentence.

### Keyboard Navigation

All interactive elements (links, buttons, `<details>` summary, text inputs) are natively
focusable in document order. There are no custom widgets, no `tabindex` values, and no
focus traps. The only removed outline is on `.customize-input`, which supplies a stronger
replacement indicator.

Known gap: there is no skip-to-content link. With five nav links on every page this is a
minor rather than severe issue, but it is a gap.

### Known Accessibility Debt

1. **No `prefers-reduced-motion` support.** `gauge-pulse`, `pulse-badge`,
   `flash-update`, and the Chart.js draw-in animation all run unconditionally. The
   previous version of this document said this "should be addressed before a v1.1
   release"; v1.1.0, v1.1.1, and v1.2.0 have all shipped without it, so that deadline
   passed unmet and the debt is still open.
2. **`--text-muted` contrast failure.** See the table above.
3. **No skip link.** See above.
4. **The donut chart's center value is canvas-drawn** and therefore invisible to
   assistive technology. The same number is present as live text in `.vix-number`
   elsewhere on the page, so no information is lost, but the canvas itself conveys the
   allocation only through its `aria-label`, which is static and does not update with the
   data.

---

## Animation and Motion

All motion signals live data state. None of it is decorative, with the deliberate
exception of the hover affordances.

| Animation | Element | Duration | Easing | Trigger |
|-----------|---------|----------|--------|---------|
| `gauge-pulse` | Hero VIX number | 3s, infinite | ease-in-out | Always on `index.html`; disabled when `.gauge-status-text` is applied |
| `pulse-badge` | `.badge-live` | 2.5s, infinite | ease-in-out | Whenever a LIVE badge is on screen |
| `flash-update` | `.vix-number` | 0.5s, once, forwards | ease | Every VIX display update. Re-triggered by removing the class, forcing a reflow with `void el.offsetWidth`, then re-adding it |
| Chart draw-in | Canvas | 600ms | easeInOutQuart | `initChart()` and every `updateChart()`; `animateScale` and `animateRotate` both on |
| Button hover lift | `.btn-primary` | 0.2s | ease | Hover, 1px `translateY` plus glow |
| Card hover | `.card` | 0.2s | ease | Hover |
| Row hover | Table rows, legend items | 0.2s | ease | Hover |
| Accordion chevron | `::after` on `.accordion-summary` | 0.2s | ease | `<details>` open and close |
| Smooth scroll | `html` | Browser default | Browser default | Any in-page anchor, via `scroll-behavior: smooth` |
| Tier banner transition | `.tier-banner` | 0.2s | ease | `data-tier` attribute change |

**Rules for adding motion:**

- If the movement does not communicate that data changed or that an element is
  interactive, do not add it.
- Nothing may animate for longer than 600ms. The eye reads a chart update faster than
  that, and a slower animation makes the tool feel less responsive than the data behind
  it actually is.
- Infinite animations are limited to the two that indicate a live connection
  (`gauge-pulse`, `pulse-badge`). Do not add a third.
- Any new animation must be wrapped in the `prefers-reduced-motion` guard that debt item
  1 above will introduce, and whoever adds that guard should cover the existing four at
  the same time.

---

## Notes for Anyone (Human or Model) Editing the Visuals

- **There is one stylesheet.** `assets/css/styles.css`, roughly 1000 lines, organized by
  banner comments into Reset, Typography, Layout, Navigation, Buttons, Badges, Cards,
  Tables, Section Base, Hero, grids, Insight, Risk, Footer, per-page dashboard sections,
  Custom form, Accordion, utilities, status states, and Responsive. Add new rules to the
  matching section rather than to the end of the file.
- **Responsive rules live only in the Responsive block at the bottom.** Do not scatter
  media queries next to their components; this file's convention is to keep them
  together, and mixing conventions is worse than either one.
- **Inline `style` attributes exist and are load-bearing in three places:** ticker and
  category colors (set from JS, and they cannot be a class because the color is data), a
  handful of one-off margin overrides in the HTML, and the footer's Azqato link color. Do
  not attempt to eliminate the JS-set ones.
- **Every color you add should be a token** unless it is a canvas fill in `chart.js`.
- **Check the change at 375px** before considering it done. That is the documented floor,
  and `body { min-width: 375px }` means anything narrower simply scrolls.
- **The site is three separate HTML files with duplicated chrome.** A change to the
  header, footer, disclaimer, or nav must be made in `index.html`, `strategy.html`, and
  `custom.html`. There is no partial, no include, and no build step that would do it for
  you.
- **Verification is visual and manual.** There is no snapshot test, no visual regression
  tool, and no linter. The verification procedure for a design change is in
  [PRD.md](PRD.md#working-practice).
