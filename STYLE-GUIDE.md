# Style Guide: Mobile Blue Dashboard

Source of truth: the CSS and markup of the shipped mobile page (prefix `mo-`, root `<div class="mo" id="app">`). This guide lets you rebuild the look without the page. All examples use placeholders, never real figures, rates or lender names. Contrast ratios below were computed with a script (WCAG 2.x relative luminance).

Design in one line: a calm, rounded, card-based phone app. One blue accent, system font, big tabular numbers, hairline borders in light mode, flat tonal cards in dark mode, a five-tab bottom bar, and everything readable at 390 px wide first.

---

## 1. Color tokens

All colors are CSS custom properties on `:root`, prefixed `--mo-`. Never hard-code a hex in a component, except the chart series palette (section 1.3).

### 1.1 Tokens

| Token | Light | Dark | Use |
|---|---|---|---|
| `--mo-bg` | `#f2f3f6` | `#0e1117` | Page background, nested card fill inside accordions |
| `--mo-card` | `#ffffff` | `#171b24` | Card, tile, input, active segment fill |
| `--mo-fg` | `#14171f` | `#eceef3` | Primary text, big numbers |
| `--mo-muted` | `#596273` | `#9aa3b2` | Labels, captions, notes, inactive tab, axis text |
| `--mo-line` | `#e2e5eb` | `#272d3a` | 1px borders, table row rules, grid lines |
| `--mo-accent` | `#2a6fdb` | `#6ea2ff` | Active tab, links, focus ring, slider, selected step, main readout border |
| `--mo-accent-soft` | `#e6eefc` | `#1f2c47` | Soft accent fill (defined, light use) |
| `--mo-accent2` | `#d9692a` | `#f0955a` | Secondary accent (defined, rarely used) |
| `--mo-ok` | `#1a7f55` | `#4cc58d` | Positive: up deltas, done, yes, provided |
| `--mo-warn` | `#a86a0a` | `#e0b04a` | Warning: Needs lines, in progress, needed, count badge |
| `--mo-bad` | `#c0392b` | `#ff7b6b` | Negative: down deltas, losses |
| `--mo-track` | `#e8ebf0` | `#262c39` | Segmented control track, bar tracks, chips, warning box fill |
| `--mo-shadow` | `0 1px 2px rgba(20,23,31,.06), 0 4px 14px rgba(20,23,31,.05)` | `none` | Card elevation |
| `--mo-bar` | `rgba(255,255,255,.94)` | `rgba(23,27,36,.94)` | Sticky header and bottom bar (with blur) |
| `--mo-pop` | `#ffffff` | `#222834` | Tooltip fill |
| `--mo-popshadow` | `0 4px 14px rgba(0,0,0,.18)` | `0 4px 14px rgba(0,0,0,.5)` | Tooltip and active segment lift |

Derived (not tokens): warning list fill `rgba(224,176,74,.14)`; meter and bullet tracks `color-mix(in srgb, var(--mo-fg) 9%, transparent)` (10% on meters); row hover `color-mix(in srgb, var(--mo-fg) 4%, transparent)`.

### 1.2 Dark mode rule

Two selectors, same values, so the OS setting and a manual override both work:

```css
:root { /* light values */ }
@media (prefers-color-scheme: dark) {
  :root:not([data-theme="light"]) { /* dark values */ }
}
:root[data-theme="dark"] { /* dark values */ }
```

- No attribute: follow the OS.
- `data-theme="light"`: force light even when the OS is dark.
- `data-theme="dark"`: force dark even when the OS is light.
- Set `body { background: var(--mo-bg); color: var(--mo-fg); }` so there is never an unthemed flash.
- Dark mode drops the card shadow (`--mo-shadow: none`) and relies on the tonal step between `--mo-bg` and `--mo-card`. Light mode keeps the border plus shadow.
- Halo behind chart text uses the surface it sits on (`--mo-card`, or `--mo-bg` inside accordion cards).

### 1.3 Chart series colors (same in both modes)

Eight fixed colors, index 0 first. Do not theme them.

| Index | Hex | Name | Typical meaning |
|---|---|---|---|
| 0 | `#2a6fdb` | Blue | Primary series, "your setting", assets up |
| 1 | `#e0783a` | Orange | Second series, "today", debts down |
| 2 | `#2f9e6f` | Green | Plan or goal series, goal band |
| 3 | `#8b5cc7` | Purple | Series 4 |
| 4 | `#c8a415` | Gold | Series 5 |
| 5 | `#d1477a` | Pink | Series 6 |
| 6 | `#3b9ab2` | Teal | Series 7 |
| 7 | `#7a7a7a` | Gray | Overflow and "other" |

Mapping rules: one entity keeps one color on every chart (accounts, scenarios). Status colors (`ok`, `warn`, `bad`) are for meaning only, never as series colors. Wrap-around uses index 7 as fallback.

### 1.4 Contrast ratios (computed)

Text thresholds: 4.5:1 for normal text. Non-text (lines, bars, dots): 3:1.

Light mode:

| Pair | Ratio | Result |
|---|---|---|
| fg on bg | 16.15 | pass |
| fg on card | 17.92 | pass |
| muted on bg | 5.54 | pass |
| muted on card | 6.14 | pass |
| muted on track | 5.14 | pass |
| accent on card | 4.78 | pass |
| accent on bg | 4.31 | FAIL |
| accent on accent-soft | 4.10 | FAIL |
| ok on card | 4.99 | pass |
| ok on bg | 4.49 | FAIL (by 0.01) |
| ok on track | 4.17 | FAIL |
| warn on card | 4.44 | FAIL (by 0.06) |
| warn on bg | 4.00 | FAIL |
| warn on track | 3.71 | FAIL |
| bad on card | 5.44 | pass |
| bad on bg | 4.90 | pass |
| bad on track | 4.55 | pass |
| white on warn (count badge, "!" dot) | 4.44 | FAIL (small bold text) |
| fg on warning list fill | 16.37 (card) / 14.93 (bg) | pass |
| muted on warning list fill | 5.61 (card) / 5.12 (bg) | pass |

Dark mode:

| Pair | Ratio | Result |
|---|---|---|
| fg on bg | 16.28 | pass |
| fg on card | 14.84 | pass |
| muted on bg | 7.43 | pass |
| muted on card | 6.77 | pass |
| muted on track | 5.50 | pass |
| accent on bg / card / soft | 7.45 / 6.79 / 5.48 | pass |
| ok on card / bg / track | 7.94 / 8.71 / 6.44 | pass |
| warn on card / bg / track | 8.60 / 9.43 / 6.98 | pass |
| bad on card / bg / track | 6.81 / 7.47 / 5.53 | pass |
| white on warn (count badge, "!" dot) | 2.00 | FAIL |
| fg on warning list fill | 11.34 (card) / 12.82 (bg) | pass |
| muted on warning list fill | 5.17 (card) / 5.85 (bg) | pass |

Non-text:

| Item | Light on card | Dark on card |
|---|---|---|
| Series 0 to 7 | 4.78, 3.04, 3.36, 4.72, 2.39, 4.29, 3.26, 4.29 | 3.61, 5.68, 5.12, 3.65, 7.20, 4.02, 5.29, 4.01 |
| `--mo-line` border | 1.26 | 1.25 |

**Flagged failures (below the threshold, reproduce as shipped or fix):**

1. Light `warn` text on `bg` (4.00), `track` (3.71) and on `card` (4.44). Affects Needs lines, "In progress" cells, and the warning box. Suggested fix: light `--mo-warn: #8f5a07` (about 5.6 on card).
2. Light `ok` text on `bg` (4.49) and on `track` (4.17). Suggested fix: light `--mo-ok: #17714c`.
3. Light `accent` on `bg` (4.31) and on `accent-soft` (4.10). Link text sits on a card (4.78, passes) but not on the page background. Suggested fix: light `--mo-accent: #2468cc` (about 5.4 on card).
4. White text on `warn` for the count badge and the "!" dot: 4.44 light, 2.00 dark. Suggested fix: use `--mo-bg` colored (dark) text on the dark-mode warn fill, or a darker light-mode warn.
5. Light series gold `#c8a415` is 2.39 on card (below 3:1 for graphics), and orange and teal sit near 3.0 to 3.3. Always pair color with a direct label, a dash pattern or a marker; never use color alone.
6. Borders are about 1.25:1 (decorative hairlines, acceptable because content never relies on the border alone).

---

## 2. Typography

- Font stack (single family everywhere): `system-ui, -apple-system, "Segoe UI", Roboto, sans-serif`, held in `--mo-font`. No web fonts.
- Base: `16px` / line-height `1.4`. `-webkit-text-size-adjust:100%`.
- Numbers: always `font-variant-numeric: tabular-nums` on money, tiles, tables and readouts. Big numbers get tight tracking (`letter-spacing:-.02em`, hero `-.025em`).

| Role | Class | Size | Weight | Line height | Other |
|---|---|---|---|---|---|
| Page title (header) | `.mo-top h1` | 26px | 750 | 1.15 | tracking -.02em |
| Page subtitle | `.mo-top p` | 13px | 400 | 1.4 | muted |
| Section label | `.mo-sec` | 13px | 700 | 1.4 | uppercase, tracking .06em, muted |
| Card heading | `.mo-card h2` | 13px | 650 | 1.4 | uppercase, tracking .05em, muted |
| Card sub heading | `.mo-card h3` | 15px | 650 | 1.4 | |
| Hero number | `.mo-hero-n` | 38px | 760 | 1.1 | tracking -.025em, wraps anywhere |
| Hero tile value | `.mo-tile.hero .mo-tv` | 36px (40px at 720px+) | 760 | 1.12 | |
| Tile value | `.mo-tv` | 20px (24px at 720px+) | 720 | 1.12 | tracking -.02em |
| Mid number | `.mo-mid` | 22px | 720 | 1.15 | |
| Readout value | `.mo-rd b` | 20px | 720 | 1.15 | |
| Tile label | `.mo-tl` | 12px | 650 | 1.4 | uppercase, tracking .05em, muted |
| Tile sub text | `.mo-ts` | 13px | 400 | 1.35 | muted |
| Body take | `.mo-take` | 14px | 400 | 1.45 | muted |
| Note | `.mo-note` | 12.5px | 400 | 1.5 | muted |
| Footer | `.mo-foot` | 13px | 400 | 1.4 | muted |
| Chart axis text | `.mo-axis text` | 11px | 400 | n/a | fill muted |
| Chart direct labels | SVG text | 10 to 11px | 400 | n/a | fill muted or series color |
| Tab label | `.mo-bar button` | 11px | 600 | n/a | |
| Chip | `.mo-chip` | 11px | 600 | n/a | |
| Badge | `.mo-badge` | 12.8px (`.8rem`) | 400 | n/a | |
| Input | `.mo-in` | 16px | 400 | n/a | never below 16px (stops iOS zoom) |

How the pieces read together: a small uppercase muted label, then a big heavy tabular number, then a small muted sub line. Labels never compete with numbers.

---

## 3. Spacing and layout

- Side gutter: `16px` (`.mo` padding-inline). Header and bar bleed full width with `margin-inline:-16px`.
- Max width: `960px` centered (`.mo`); bottom bar inner `max-width:960px`.
- Vertical rhythm: page stack gap `12px`; card inner gap `10px`; tile inner gap `3px`; section label top margin `12px`.
- Card: padding `16px`, radius `18px`, border `1px solid var(--mo-line)`, shadow `var(--mo-shadow)`.
- Tile: padding `14px 16px`, radius `18px`, same border and shadow, `min-height:0`.
- Inner radii: accordion `16px`; readout, leg, control box `14px`; segmented track and input `12px` and `10px`; chips and pills `999px`; bars `5px`; chart bars `2px` to `3px`.
- Grids: tiles are 2 columns (`repeat(2, minmax(0,1fr))`, gap 12px); hero tile spans both (`grid-column:1/-1`). At 720px and up, tiles use `repeat(auto-fit, minmax(11.5rem,1fr))`, `.mo-grid2` becomes 2 columns, controls become 3 columns.
- Safe areas: bottom bar adds `padding-bottom:env(safe-area-inset-bottom)`. The page shell is `height:100dvh` (fallback `100vh`) with the middle region scrolling (`overscroll-behavior:contain`).
- Touch targets: minimum 44px for buttons, inputs, tasks and links; tab bar buttons 62px; accordion summary 60px; fold summary 48px; list rows with a chevron 56px; range input min height 32px.
- Container queries: `.mo-card` is `container-type:inline-size`; below 680px tables collapse to stacked cards, below 560px bullet rows go to two rows.
- Elevation: light uses border plus soft two-layer shadow. Dark uses border only. Cards nested in an accordion drop shadow and fill with `--mo-bg`.

```css
.mo{max-width:960px;margin-inline:auto;padding-inline:16px;font-family:var(--mo-font);font-size:16px;line-height:1.4;color:var(--mo-fg);background:var(--mo-bg);-webkit-tap-highlight-color:transparent}
.mo-page{display:flex;flex-direction:column;gap:12px}
```

---

## 4. Components

Shared: `.mo button{font-family:inherit}`. All interactive elements use the focus ring in section 7.

### 4.1 Header (sticky, blurred)

```css
.mo-top{position:sticky;top:0;z-index:20;background:var(--mo-bar);backdrop-filter:blur(12px);-webkit-backdrop-filter:blur(12px);margin-inline:-16px;padding:14px 16px 10px;border-bottom:1px solid var(--mo-line)}
.mo-top h1{margin:0;font-weight:750;font-size:26px;line-height:1.15;letter-spacing:-.02em}
.mo-top p{margin:2px 0 0;font-size:13px;color:var(--mo-muted)}
```
```html
<header class="mo-top"><h1>Home</h1><p>Short status line, updated date</p></header>
```

### 4.2 Card

```css
.mo-card{background:var(--mo-card);border:1px solid var(--mo-line);border-radius:18px;padding:16px;display:flex;flex-direction:column;gap:10px;min-width:0;box-shadow:var(--mo-shadow);container-type:inline-size}
.mo-card h2{font-size:13px;font-weight:650;color:var(--mo-muted);text-transform:uppercase;letter-spacing:.05em;margin:0}
```
```html
<div class="mo-card"><h2>Card title</h2><p class="mo-take">One plain sentence that says the point.</p><p class="mo-note">Small explanation of how it is counted.</p></div>
```

### 4.3 Tile (big number)

```css
.mo-tile{background:var(--mo-card);border:1px solid var(--mo-line);border-radius:18px;padding:14px 16px;display:flex;flex-direction:column;gap:3px;min-width:0;box-shadow:var(--mo-shadow)}
.mo-tile.hero{grid-column:1/-1}
.mo-tl{font-size:12px;font-weight:650;text-transform:uppercase;letter-spacing:.05em;color:var(--mo-muted)}
.mo-tv{font-weight:720;font-size:20px;letter-spacing:-.02em;font-variant-numeric:tabular-nums;line-height:1.12}
.mo-tile.hero .mo-tv{font-size:36px;font-weight:760}
.mo-ts{font-size:13px;line-height:1.35;color:var(--mo-muted)}
.mo-delta{display:inline-flex;gap:.25rem;font-size:.8rem}
.mo-delta.up{color:var(--mo-ok)} .mo-delta.down{color:var(--mo-bad)}
.mo-spark{display:block;width:100%;height:28px;margin-top:auto}
```
```html
<div class="mo-tiles">
  <div class="mo-tile hero"><span class="mo-tl">Label</span><span class="mo-tv">$0,000</span><span class="mo-delta up">▲ +$000 this month</span><span class="mo-ts">As of Mon D</span></div>
  <div class="mo-tile"><span class="mo-tl">Label</span><span class="mo-tv">$000</span></div>
</div>
```
Sparkline: 28px tall SVG, 2px stroke in the series color, area fill at opacity .14, `vector-effect:non-scaling-stroke`. Deltas always carry a glyph (▲ or ▼) so meaning is not color only.

### 4.4 Buttons

There is no filled primary button in the shipped page. The primary action style is the accent link row, and the selected state of a control is carried by weight, white surface and accent. If a filled primary is needed, build it from the tokens (see "derived primary").

Link button (primary navigation action):
```css
.mo-link{display:flex;align-items:center;gap:6px;min-height:44px;background:none;border:0;border-top:1px solid var(--mo-line);padding:6px 0 0;color:var(--mo-accent);font-size:16px;font-weight:650;cursor:pointer;width:100%;text-align:left}
```
```html
<button class="mo-link" type="button">All tasks and data requests</button>
```

Segmented control / tab chips (secondary choice, one of N):
```css
.mo-seg{display:flex;background:var(--mo-track);border-radius:12px;padding:3px;gap:0;width:100%}
.mo-seg button{flex:1 1 0;min-height:44px;border:0;background:none;color:var(--mo-muted);font-size:14px;font-weight:600;border-radius:10px;padding:4px 6px;cursor:pointer;line-height:1.2}
.mo-seg button[aria-pressed=true]{background:var(--mo-card);color:var(--mo-fg);box-shadow:0 1px 3px rgba(0,0,0,.18)}
```
```html
<div class="mo-seg" role="group" aria-label="View"><button aria-pressed="true">Option A</button><button aria-pressed="false">Option B</button></div>
```

Small pill choices (outside a track, at 720px and up the older chip form): `border:1px solid var(--mo-line); border-radius:12px; padding:.3rem .7rem`; pressed adds `border-color:var(--mo-fg)` and weight 600.

Ghost button: the fold summary and `mo-sort` header buttons: no fill, no border, inherit color (`.mo-sort{background:none;border:0;padding:0;font:inherit;color:inherit;display:inline-flex;gap:.25rem}`), sort direction shown by `aria-sort` with a small triangle (`::after`, `.6rem`).

Derived primary (not in the page, token-only): `background:var(--mo-accent); color:#fff; min-height:44px; border-radius:12px; font-weight:650`. Note white on the light accent is 4.78 (passes) but white on the dark accent `#6ea2ff` is 2.54 (fails); in dark mode use `color:var(--mo-bg)` instead.

Slider:
```css
.mo-cb{display:flex;flex-direction:column;gap:.5rem;border:1px solid var(--mo-line);border-radius:14px;padding:12px;min-width:0}
.mo-cb-top{display:flex;justify-content:space-between;align-items:center;gap:.5rem}
.mo-cb input[type=range]{width:100%;margin:0;accent-color:var(--mo-accent);min-height:32px}
```
```html
<div class="mo-cb"><div class="mo-cb-top"><label for="x">Label</label><b>Value</b></div><input id="x" type="range" aria-label="Label slider"></div>
```

Number input:
```css
.mo-in{min-height:44px;font:inherit;font-size:16px;width:7.5rem;text-align:right;border-radius:10px;background:var(--mo-card);color:var(--mo-fg);border:1px solid var(--mo-line);padding:.25rem .5rem;font-variant-numeric:tabular-nums}
```
```html
<input class="mo-in" id="n" type="number" inputmode="decimal" aria-label="Label">
```
Pair every slider with a number input; both write the same value.

### 4.5 Accordion

Main accordion (`details`), plus a lighter fold for tables.
```css
details.mo-acc{background:var(--mo-card);border:1px solid var(--mo-line);border-radius:16px;box-shadow:var(--mo-shadow)}
details.mo-acc>summary{list-style:none;display:flex;justify-content:space-between;align-items:center;gap:10px;min-height:60px;padding:8px 16px;font-weight:650;font-size:16px;cursor:pointer}
details.mo-acc>summary::-webkit-details-marker{display:none}
details.mo-acc>summary::after{content:"+";font-size:24px;color:var(--mo-muted);font-weight:400}
details.mo-acc[open]>summary::after{content:"\2212"} /* typographic minus glyph */
details.mo-acc .sm{display:block;font-size:12px;color:var(--mo-muted);font-weight:500}
details.mo-acc>.body{padding:0 12px 14px;display:flex;flex-direction:column;gap:12px}
details.mo-acc .mo-card,details.mo-acc .mo-tile{box-shadow:none;background:var(--mo-bg)}
details.mo-fold{border-top:1px solid var(--mo-line)}
details.mo-fold>summary{display:flex;justify-content:space-between;align-items:center;min-height:48px;color:var(--mo-accent);font-weight:650;font-size:15px;cursor:pointer}
```
```html
<details class="mo-acc"><summary><span>Section name<span class="sm">One line of what is inside</span></span></summary><div class="body">...</div></details>
```
Uses native `details`, so keyboard and screen readers work with no script.

### 4.6 Tables

```css
.mo-table{width:100%;border-collapse:collapse;font-variant-numeric:tabular-nums}
.mo-table th{font-weight:600;text-align:left;color:var(--mo-muted);font-size:.8rem;position:sticky;top:0;background:var(--mo-card);white-space:nowrap}
.mo-table th,.mo-table td{padding:.4rem .5rem;border-bottom:1px solid var(--mo-line);vertical-align:middle}
.mo-table .num{text-align:right;white-space:nowrap}
.mo-table td.key{font-weight:600}
.mo-scroll{overflow:auto;max-height:28rem}
```
Status text cells: `td[data-val="Done"|"Yes"|"Provided"]` color `--mo-ok`; `"In progress"|"Needed"|"No"` color `--mo-warn`; `"To do"` color `--mo-muted`.

Phone rule (container under 680px): header hidden, each row becomes a bordered card with label/value lines.
```css
@container (max-width:680px){
 .mo-table thead{display:none}
 .mo-table,.mo-table tbody{display:block;width:100%}
 .mo-table tr{display:block;border:1px solid var(--mo-line);border-radius:12px;padding:6px 12px;margin:0 0 8px}
 .mo-table td{display:flex;justify-content:space-between;align-items:baseline;gap:12px;border:0;padding:5px 0;text-align:right}
 .mo-table td::before{content:attr(data-label);color:var(--mo-muted);font-size:12px;text-align:left;flex:none;max-width:45%}
 .mo-table td:empty{display:none}
}
```
```html
<table class="mo-table"><thead><tr><th>Name</th><th class="num">Amount</th></tr></thead><tbody><tr><td class="key" data-label="Name">Item</td><td class="num" data-label="Amount">$000</td></tr></tbody></table>
```
Each `td` carries `data-label` for the stacked layout. Tables live inside folds ("Show table: Name") so the default screen stays light. A sort bar (`.mo-seg.mo-sortbar`) replaces header sorting on phones (hidden at 720px and up).

### 4.7 Status chips and pills

```css
.mo-chip{font-size:11px;font-weight:600;color:var(--mo-muted);background:var(--mo-track);border:0;border-radius:999px;padding:.05rem .5rem;white-space:nowrap}
.mo-badge{display:inline-flex;gap:.25rem;align-items:center;padding:.1rem .45rem;border-radius:999px;font-size:.8rem;border:1px solid var(--mo-line);white-space:nowrap}
.mo-badge.up{color:var(--mo-ok);border-color:var(--mo-ok)}
.mo-badge.down{color:var(--mo-bad);border-color:var(--mo-bad)}
.mo-badge.none{color:var(--mo-muted)}
.mo-count{display:inline-block;margin-left:6px;padding:1px 9px;border-radius:99px;background:var(--mo-warn);color:#fff;font-size:12px;font-weight:700;vertical-align:1px}
.mo-steps{display:flex;gap:4px;flex-wrap:wrap}
.mo-step{flex:1 1 4.5rem;font-size:.8rem;text-align:center;padding:.25rem .3rem;border-radius:999px;border:1px solid var(--mo-line);color:var(--mo-muted)}
.mo-step.done{color:var(--mo-ok);border-color:var(--mo-ok)}
.mo-step.now{border-color:var(--mo-accent);color:var(--mo-accent);font-weight:600}
```
```html
<span class="mo-chip">Status</span>
<span class="mo-badge up">▲ +0.0%</span> <span class="mo-badge down">▼ 0.0%</span> <span class="mo-badge none">no return yet</span>
<div class="mo-steps"><span class="mo-step done">Step 1</span><span class="mo-step now">Step 2</span><span class="mo-step">Step 3</span></div>
```
Progress meters: `.mo-meter{height:10px;border-radius:999px;background:color-mix(in srgb,var(--mo-fg) 10%,transparent);overflow:hidden;display:flex;gap:2px}` with `<i>` segments; single bars `.mo-bar2{height:10px;border-radius:5px;background:var(--mo-track)}`.

### 4.8 Bottom bar and icons

Five fixed tabs (Home, Money, Retire, Cash, More).
```css
.mo-bar{position:relative;flex:none;margin-inline:-16px;z-index:30;background:var(--mo-bar);backdrop-filter:blur(14px);-webkit-backdrop-filter:blur(14px);border-top:1px solid var(--mo-line);padding-bottom:env(safe-area-inset-bottom)}
.mo-bar .in{max-width:960px;margin:0 auto;display:grid;grid-template-columns:repeat(5,1fr)}
.mo-bar button{background:none;border:0;color:var(--mo-muted);font-size:11px;font-weight:600;min-height:62px;padding:8px 2px 6px;display:flex;flex-direction:column;align-items:center;justify-content:center;gap:3px;cursor:pointer}
.mo-bar button svg{width:26px;height:26px;fill:none;stroke:currentColor;stroke-width:1.8;stroke-linecap:round;stroke-linejoin:round}
.mo-bar button[aria-selected="true"]{color:var(--mo-accent)}
.mo-bar button[aria-selected="true"] svg{stroke-width:2.3}
```
```html
<nav class="mo-bar" aria-label="Main"><div class="in" role="tablist">
  <button role="tab" aria-selected="true" data-tab="home"><svg viewBox="0 0 24 24" aria-hidden="true"><path d="M3 11l9-8 9 8"/><path d="M5 10v10h5v-6h4v6h5V10"/></svg>Home</button>
  <button role="tab" aria-selected="false" data-tab="more"><svg viewBox="0 0 24 24" aria-hidden="true"><circle cx="5" cy="12" r="1.6"/><circle cx="12" cy="12" r="1.6"/><circle cx="19" cy="12" r="1.6"/></svg>More</button>
</div></nav>
```
Icon rules: 24x24 viewBox rendered at 26px, outline only (`fill:none`), `currentColor` stroke 1.8, round caps and joins, active stroke 2.3 plus accent color. Active state is color, heavier stroke and `aria-selected`; the label is always shown. Icons: Home = house outline, Money = card with stripe, Retire = rising line with arrow, Cash = coin with dollar mark, More = three dots.

Row with chevron (settings-like list): `.mo-st` is a 56px row, 12px status dot (`.d`, 50% radius), title bold over a 13px muted line, and a `›` glyph (22px muted) via `::after`.

### 4.9 Tooltip

```css
.mo-tip{position:absolute;pointer-events:none;background:var(--mo-pop);border:1px solid var(--mo-line);border-radius:12px;box-shadow:var(--mo-popshadow);padding:.3rem .6rem;font-size:.8rem;line-height:1.4;width:max-content;max-width:min(19rem,92%);z-index:3}
.mo-tip .tt{font-weight:600}
.mo-tip .sw{display:inline-block;width:.6rem;height:.6rem;border-radius:2px;margin-right:.35rem}
```
```html
<div class="mo-tip"><div class="tt">Title line</div><div><i class="sw" style="background:#2a6fdb"></i>Series name $000</div></div>
```
Placement: 14px right and below the pointer, flips left or up near the edge, clamped to the chart box. First line is the title (bold), following lines are series with a color swatch. On touch, a tap shows the readout and keeps it until the next tap (tooltips hide on any pointerdown first).

### 4.10 Notices

Needs line (something missing, nothing guessed):
```css
.mo-needs{color:var(--mo-warn)}
.mo-warnbox{border-left:3px solid var(--mo-warn);padding:.3rem .6rem;border-radius:6px;background:var(--mo-track)}
.mo-needlist{list-style:none;margin:6px 0 0;padding:0}
.mo-needlist li{display:flex;gap:10px;align-items:flex-start;padding:10px 12px;margin:0 0 6px;background:rgba(224,176,74,.14);border-radius:12px;font-size:14px;min-height:44px}
.mo-needlist li::before{content:"!";flex:none;width:20px;height:20px;border-radius:50%;background:var(--mo-warn);color:#fff;font-size:13px;font-weight:700;display:flex;align-items:center;justify-content:center;margin-top:1px}
.mo-needlist small{display:block;color:var(--mo-muted);font-size:12px}
```
```html
<p class="mo-needs">Needs: item one, item two</p>
<p class="mo-note">No progress or chart is shown until these are in. Nothing is estimated.</p>
<ul class="mo-needlist"><li><span>Task name<small>Why it matters</small></span></li></ul>
<p class="mo-note mo-warnbox">Plain sentence about a limit that was hit.</p>
```
Estimate labels: put the word in plain text, never as an icon: `Value (your estimate)` as a tile label, `(estimate)` after a computed figure, and a muted note saying who supplied it. Missing values read `not added`, `rate not added`, `payment not added`, `none set yet`, `no balance`, or `n/a` (see section 6). A rule: when data is missing the card shows a Needs line and no number, never a guess.

---

## 5. Charts (d3, inline SVG)

Library: d3 v7 loaded as a script, SVG only, no canvas.

Setup:
- Each chart sits in `.mo-chart` (`position:relative; min-height:120px; width:100%`) so the tooltip can be absolutely positioned.
- SVG: `width:100%`, fixed `height`, `viewBox="0 0 W H"` with W taken from the box width, `class="mo-svg"`, `role="img"`, and an `aria-label` that says what the chart shows. `svg{display:block;max-width:100%;overflow:visible;touch-action:pan-y}`. Redraw on `ResizeObserver` when width changes by more than 1px.
- Narrow switch: under 560px box width use narrow margins (line chart `l:46 r:84 t:16 b:44`; wide `l:54 r:150`). Horizontal bar charts `l:62..76 r:10 t:30 b:26`.
- Text in SVG inherits the page font; sizes 10 to 11px, fill `var(--mo-muted)`.

Axes and grid:
```css
.mo-axis text{fill:var(--mo-muted);font-size:11px}
.mo-axis line,.mo-axis path{stroke:var(--mo-line)}
.mo-axis .domain{display:none}
.mo-grid line{stroke:var(--mo-line);shape-rendering:crispEdges}
```
- Y axis: `d3.axisLeft(y).ticks(5).tickFormat(compact).tickSize(0).tickPadding(6)`; light horizontal grid lines spanning the plot, no vertical grid.
- X axis: `d3.axisBottom(x)` with `tickSize(0 to 4)`, `tickPadding(6)`, sparse ticks chosen by hand (for example 5 values), unit word "Age" at the right end.
- Compact tick format: `$000`, `$0.0k`, `$00k`, `$0.00M`. If the y axis does not start at zero, print a baseline note under the chart: "Baseline: the axis starts at $x, not zero."
- Halo for any label over lines: class `mo-halo` (`paint-order:stroke; stroke:var(--mo-card); stroke-width:3px; stroke-linejoin:round`).

Lines and areas:
- `d3.line().curve(d3.curveMonotoneX)`; `fill:none`, `stroke-linecap:round`, `stroke-linejoin:round`.
- Widths: emphasized series 3.2 to 3.6px, normal 2 to 2.4px, comparison series 1.3px at opacity .9. Draw bold series last (on top).
- Area under the chosen series: same color, opacity `.14` to `.2`.
- Dashed lines: cap or limit line `stroke-dasharray:5 4`; "today" reference path `6 5`; vertical hover rule `3 3` in muted.
- Hover dots: r 4.5, series fill, `stroke:var(--mo-card)`, width 2.

End labels (no legend needed for lines):
- Direct labels to the right of the last point, text colored by series, sorted by y position and nudged apart (minimum vertical gap about 13px) so none overlap. Right margin is reserved for them (84px narrow, 150px wide).

Goal range shading:
- Rect across the plot between low and high goal values, fill series 2 green at opacity `.13`.
- Two edge lines `stroke-width:1.2`, opacity `.8`, same green.
- Labels at left inside the band, 10px muted: "Goal high $x" (below its line) and "Goal low $x" (above its line).

Other chart types:
- Waterfall: assets step up in series 0 blue, debts step down in series 1 orange, totals in `var(--mo-fg)` at opacity .85; bars `rx:2`; connector lines `stroke:var(--mo-muted)`, width 1, opacity .5.
- Donut: slices carry the account or debt color, `tabindex=0` and `aria-label="Name $000"`; hovering or focusing a slice dims the other legend rows (`.dim{opacity:.45}`); legend list to the right (`.mo-dl`, 13px).
- Bullet rows (`.mo-bl`): 24px tall track at `color-mix(fg 9%)`, value text right-aligned muted; becomes two rows under 560px.
- Bubble: x = balance, y = rate, size = payment; color by debt.
- Legends: `.mo-legend` flex-wrap, 13px muted; line swatch is a 1.4rem wide `border-top:3px` (solid, dashed or dotted to match the series), box swatch is `.7rem` square with `2px` radius.

Tooltip: `mkTip(box)` helper (section 4.9). Pointer move on a transparent overlay rect finds the nearest x and shows a crosshair, dots and one tooltip with all series at that x. The SVG has `tabindex=0` so keyboard users can focus it.

Color mapping rules: series colors only from section 1.3; the same entity keeps the same color across charts; semantic status colors (`ok`, `warn`, `bad`) only for deltas and status, never for series; gray (index 7) is "other" or "no data"; always add a direct label, dash style or marker so color is not the only cue.

---

## 6. Copy style

- Plain words, short sentences, second person ("what you own and what you owe"). Say the point first, then one `.mo-note` for how it is counted.
- No em dashes or en dashes anywhere. Use commas, periods, parentheses or colons. Ranges are written "6% to 8%". Negative amounts use an ASCII hyphen-minus (`-$1,111`). Check text with a script before shipping.
- Labels follow a fixed shape:
  - Missing data: `Needs: item one, item two` (colon, lowercase list, comma separated; semicolons when items contain commas).
  - Hand off: `Next: action`, `Blocker: reason`.
  - Provenance: `(estimate)`, `Value (your estimate)`, `as of Mon D`, `Held in Account name`.
  - Not available: `n/a`, `not added`, `rate not added`, `payment not added`, `none set yet`, `no balance`, `not applicable`, `no return yet`.
- Card titles are short noun phrases or plain questions ("Net worth over time", "What you owe"). Section labels are one or two words ("Net worth", "Assets", "Debts", "Tables").
- Folds say what they hold: `Show table: Name`.
- Formats:
  - Currency: `$` + thousands separators, no decimals when whole, two decimals when not (`$1,234`, `$1,234.56`); negative `-$1,234`; signed deltas `+$1,234` or `-$1,234`.
  - Compact (axes and labels): `$777`, `$1.2k`, `$250k`, `$1.25M` (k from 1,000, whole k from 100,000).
  - Percent: up to 1 decimal in prose (`4.5%`), up to 2 decimals in rate tables (`6.25%`); trailing zeros dropped.
  - Dates: `Mon D` (`Oct 15`) with no leading zero and no year inside the app; add the year only when it is ambiguous. Data stored as `YYYY-MM-DD`.
  - Ages and counts: plain integers with thousands separators.
- Delta words: "up" and "down" with a glyph (▲ ▼) so color is never the only cue.
- Never state a number that is not in the data. If it is derived, say how in the note below it.

---

## 7. Accessibility and mobile rules

- Design at 390 px width first. Everything is a single column of cards; the 720px breakpoint only adds columns. No horizontal page scroll; wide content scrolls inside `.mo-scroll`, or stacks (tables).
- Viewport: `width=device-width, initial-scale=1`; do not disable zoom. Inputs are 16px so iOS does not zoom.
- Touch targets: 44px minimum (see section 3). Tab bar 62px.
- Focus ring (keyboard only): `.mo button:focus-visible, .mo summary:focus-visible { outline:2px solid var(--mo-accent); outline-offset:-2px; border-radius:10px }`. Apply the same ring to inputs and chart SVG with `tabindex=0`.
- Aria patterns used:
  - Bottom bar: `nav[aria-label="Main"]` containing `role="tablist"`, buttons `role="tab"` with `aria-selected`; icons `aria-hidden="true"`.
  - Segmented controls: container `role="group"` with `aria-label`, buttons with `aria-pressed="true|false"`.
  - Sortable headers: `aria-sort="ascending|descending|none"` on the `th` and the button.
  - Charts: `role="img"` + descriptive `aria-label` on the SVG; donut slices focusable with `aria-label="Name $000"`; the same numbers also exist in a table fold for screen readers.
  - Sliders: a `<label for>` or `aria-label` ("Target age slider") and a paired number input.
  - Accordions: native `details`/`summary`.
  - Hidden pages: `[hidden]` attribute (`.mo-page[hidden]{display:none}`).
- Color is never the only signal: deltas have ▲ ▼, status has words, series have direct labels or dash styles, Needs lines say "Needs:".
- Contrast: see the flagged list in section 1.4. Fix the four text failures before reuse.
- Reduced motion: the shipped page has almost no motion (no animated transitions beyond the bottom bar translate and tooltip appear), and it does not yet define a `prefers-reduced-motion` rule. When reusing, add `@media (prefers-reduced-motion: reduce){*{animation:none!important;transition:none!important;scroll-behavior:auto!important}}`.
- Tap handling: `-webkit-tap-highlight-color:transparent`; chart tooltips on touch appear on tap and stay until the next tap; `touch-action:pan-y` on SVG so vertical scroll still works.
- Safe areas: honor `env(safe-area-inset-bottom)` on the bottom bar; the main region uses `100dvh` and its own scroll with `overscroll-behavior:contain`.
- Print and theme: respect `prefers-color-scheme` and the `data-theme` override; never rely on JS to pick the theme.

## 8. v3 facelift components (feature v3.0, structure only)

Same tokens, same 44 px targets, same reduced motion rule. New or reshaped components:
- Percent bar: a track from 0 to 200 percent with a tick and label at 100; the fill is one token color; the value is printed as text at the end of the bar ("Needs data" instead of a bar when there is no sourced row). Three bars per metric (guideline, typical, rank).
- Ladder: vertical rungs at 25, 50, 75, 100 percent, the current position marked "you are here" in words, the next rung with a one line "what it takes", and the age each rung is reached.
- Diverging gap bar: zero line in the middle, reported above expected to one side and below to the other, direction in words ("more than expected", "less than expected"), never color alone. Expected, reported and gap sit in a small table beside it.
- Reference tick bar (loan to value): the bar for the ratio with ticks for each lender reference point, each tick labeled with its source.
- Task row: title, due date chip (words such as "due soon" or "past due"), priority chip, and action buttons (Done, status, due date, priority). Done rows show note and done date in the Done fold.
- Data request line: count of items still needed, the top two, and a link row to the full list.
- Quest row: title, tasks done of tasks total, and what it unlocks in plain words.
- Payoff cards: Next payment card (amount, date, reasons), tiles for debt free and interest saved, compare table (compact at 390 px), trail with a stop per debt and milestone pills.
- Expected value label: a small note under any total that uses an expected value, stating the date and that it is not a bank or servicer reading.
- Mortgage for the year: accordion with the typed form fields, derived monthly lines in plain words, as-of and source.
- Copy: no dashes of any kind, no multiples written as "x", no streak or level words.
