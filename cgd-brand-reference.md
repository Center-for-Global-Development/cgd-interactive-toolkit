# CGD Branding Reference

This file is the working brand reference for CGD interactives. It combines CGD's general visual identity (logo, colors, type) with the chart-specific rules from the **CGD Data Visualization Style Guide** (v03, 4.4.23), adapted for responsive, web-embedded interactives.

---

## Why the Brand Matters

From the style guide, the rationale to keep in mind when making design tradeoffs:

- **Visuals reflect CGD's values** — transparent data and rigorous analysis, plus clear, consistent, polished presentation.
- **Recognizable = trusted.** When a visual reads as "from CGD," readers trust the information as accurate and unbiased.
- **Better design means more reach.** People read and share visuals that are recognizable and easy to understand.

---

## Colors

### Brand / Identity Palette

| Name | Hex | RGB | Usage |
|------|-----|-----|-------|
| **Teal** | `#0B4C5B` | 11, 76, 91 | Logo, primary brand color |
| **Gold** | `#FFB52C` | 255, 181, 44 | Logo, accents |
| **Teal Gray** | `#85A5AD` | 133, 165, 173 | Logo (teal at 50% opacity) |
| **Light Teal** | `#006970` | 0, 105, 112 | Secondary teal; **primary data-viz color** |
| **Cream** | `#F3F6F7` | 243, 246, 247 | Print backgrounds |
| **Dark Gray** | `#394649` | 57, 70, 73 | Body text, dark UI elements |
| **Teal Black** | `#1A272A` | 26, 39, 42 | Darkest tone, near-black; chart text |

### Supplementary Palette

| Name | Hex | RGB |
|------|-----|-----|
| **Blue** | `#2D99B5` | 45, 153, 181 |
| **Light Blue** | `#BFDEE0` | 191, 222, 224 |
| **Light Gold** | `#FEE8BF` | 254, 232, 191 |
| **Light Gray** | `#DFE0E2` | 223, 224, 226 |
| **Teal** | `#0B4C5B` | 11, 76, 91 |

(The supplementary colors fill out the chart palettes below.)

### Stoplight / Status Colors

| Name | Hex | RGB | Meaning |
|------|-----|-----|---------|
| **Green** | `#00896C` | 0, 137, 108 | Good |
| **Gold** | `#FFB52C` | 255, 181, 44 | Caution |
| **Red** | `#D15553` | 209, 85, 83 | Bad |

The logo and identity system lead with **Teal `#0B4C5B`**. The data-viz system leads with **Light Teal `#006970`** (it's the first categorical color and the center of the sequential ramp). When in doubt: use `#0B4C5B` for brand chrome (headers, logo lockups) and `#006970` as the dominant color *inside* charts.

### CSS Custom Properties

```css
:root {
  /* Identity */
  --cgd-teal: #0B4C5B;
  --cgd-gold: #FFB52C;
  --cgd-teal-gray: #85A5AD;
  --cgd-light-teal: #006970;
  --cgd-cream: #F3F6F7;
  --cgd-dark-gray: #394649;
  --cgd-teal-black: #1A272A;

  /* Supplementary */
  --cgd-blue: #2D99B5;
  --cgd-light-blue: #BFDEE0;
  --cgd-light-gold: #FEE8BF;
  --cgd-light-gray: #DFE0E2;

  /* Status */
  --cgd-red: #D15553;
  --cgd-green: #00896C;
}
```

---

## Data Visualization Color Systems

The style guide defines four ways to assign color, depending on the kind of data. Apply colors **in the order listed** as a chart adds series/steps.

### Categorical (nominal / unordered data)

| Order | Name | Hex |
|-------|------|-----|
| 1 | Light Teal | `#006970` |
| 2 | Gold | `#FFB52C` |
| 3 | Blue | `#2D99B5` |
| 4 | Light Blue | `#BFDEE0` |
| 5 | Light Gold | `#FEE8BF` |
| 6 | Teal Gray | `#85A5AD` |
| 7 | Dark Gray | `#394649` |
| 8 | Light Gray | `#DFE0E2` |

Use Light Gray `#DFE0E2` for **None / Neutral** ("not applicable," absent, or neutral) categories.

### Sequential (ordered / ranked data)

Sequential palettes center on Light Teal and expand outward. "Degrees" = number of distinct steps.

| Degrees | Colors (in order) |
|---------|-------------------|
| 1 | Light Teal |
| 2 | Light Blue → Light Teal |
| 3 | Light Gray → Light Blue → Light Teal |
| 4 | Light Gray → Light Blue → Light Teal → Dark Gray |
| 5–8 | Light Gray → Teal Gray → Light Blue → Blue → Light Teal → Teal → Dark Gray → Teal Black |

Full 5–8 degree ramp:

| Order | Name | Hex |
|-------|------|-----|
| 1 | Light Gray | `#DFE0E2` |
| 2 | Teal Gray | `#85A5AD` |
| 3 | Light Blue | `#BFDEE0` |
| 4 | Blue | `#2D99B5` |
| 5 | Light Teal | `#006970` |
| 6 | Teal | `#0B4C5B` |
| 7 | Dark Gray | `#394649` |
| 8 | Teal Black | `#1A272A` |

### Polar (diverging / two-direction data)

Pairs the teal family (one direction) with gold (the other).

| Degrees | Colors (in order) |
|---------|-------------------|
| 1 | Light Teal |
| 2 | Light Teal, Gold |
| 3 | Light Teal, Teal Gray, Gold |
| 4 | Light Teal, Teal Gray, Light Gold, Gold |
| 5 | Light Teal, Teal Gray, Light Blue, Light Gold, Gold |

### Stoplight (status / rating: good → caution → bad)

| Meaning | Name | Hex |
|---------|------|-----|
| Good | Green | `#00896C` |
| Caution | Gold | `#FFB52C` |
| Bad | Red | `#D15553` |

### Quick-copy arrays (JS)

```js
// Categorical (standard order)
const CGD_CATEGORICAL = ['#006970', '#FFB52C', '#2D99B5', '#BFDEE0',
                         '#FEE8BF', '#85A5AD', '#394649', '#DFE0E2'];

// Sequential (light → dark, centered on Light Teal)
const CGD_SEQUENTIAL = ['#DFE0E2', '#85A5AD', '#BFDEE0', '#2D99B5',
                        '#006970', '#0B4C5B', '#394649', '#1A272A'];

// Polar (teal ↔ gold)
const CGD_POLAR = ['#006970', '#85A5AD', '#BFDEE0', '#FEE8BF', '#FFB52C'];

// Stoplight
const CGD_STOPLIGHT = { good: '#00896C', caution: '#FFB52C', bad: '#D15553' };

// None / neutral
const CGD_NEUTRAL = '#DFE0E2';
```

---

## Typography

### Brand Fonts

| Font | Use | Source |
|------|-----|--------|
| **Sofia Pro** | Titles, headings, graphics, chart text, web UI | Adobe Fonts |
| **Bitter** | Body text, print | Google Fonts |

```html
<!-- Bitter from Google Fonts -->
<link href="https://fonts.googleapis.com/css2?family=Bitter:ital,wght@0,400;0,700;1,400&display=swap" rel="stylesheet">
```

Sofia Pro requires an Adobe Fonts subscription or a self-hosted licensed copy. For quick prototypes where Sofia Pro isn't available, use a clean geometric sans-serif (Inter) or system sans-serif as a stand-in, preserving the weight mapping (Bold → Bold, Medium → Medium, Regular → Regular, Light Italic → Light Italic).

### Figure / Chart Typography

| Element | Font | Size | Case | Color |
|---------|------|------|------|-------|
| Titles | Sofia Pro **Bold** | 18pt | Title Case | Teal `#0B4C5B` |
| Axis Labels | Sofia Pro **Medium** | 14pt | Sentence case | Teal `#0B4C5B` |
| Axis Data (tick values) | Sofia Pro **Regular** | 12pt | — | Teal Black `#1A272A` |
| Data Labels | Sofia Pro **Light Italic** | 12pt / 13pt line | — | Teal Black `#1A272A` |
| Notes | Sofia Pro **Light Italic** | 12pt / 13pt line | — | Teal Black `#1A272A` |

Treat these point size as a general hierarchy/ratio guide and use relative units; don't hardcode the point sizes into the CSS, as they'll need to adjust responsively.

---

## Chart & Figure Styles

### Line Styles

| Element | Style | Weight | Color | Detail |
|---------|-------|--------|-------|--------|
| Axis lines | Solid | 1pt | Teal Black `#1A272A` | |
| Grid lines | Solid | 1pt | Light Gray `#DFE0E2` | |
| Data series | Solid | 4pt | Light Teal `#006970` | |
| Projection | Dashed | 4pt | Light Teal `#006970` | 10pt dash, 4pt gap |
| Trend lines | Dotted | 3pt | Gold `#FFB52C` | 8pt gap |
| Separators | Dashed | 1pt | Teal Gray `#85A5AD` | 10pt dash, 4pt gap |
| Indicators | Solid | 1pt | Teal Gray `#85A5AD` | |

Treat these point size as a general hierarchy/ratio guide and use relative units; don't hardcode the point sizes into the CSS, as they'll need to adjust responsively.

### Chart Layout Conventions

- **Title:** should usually be ommitted and included in the page the interactive is embedded in, rather than the frame. In rare case where the title will change based on interactivity and thus needs to be in the chart, it should be aligned top left and in title case.
- **Y-axis label:** horizontal (not rotated), anchored top-left.
- **X-axis label:** horizontal, centered along the bottom.

### Best Practices

1. **No unnecessary grids or backgrounds** — use them only when needed for clarity.
2. **Direct labeling over legends** — label series on the chart itself when possible, although adjustment may be necessary to maintain responsiveness
3. **No overlapping data points or labels** — split into multiple charts or only label a subset of the points, showing more information on hover or via filtering buttons. As a rough ceiling, directly label only the ~10–12 most important points and let hover/tooltips carry the rest.
4. **No outlines, shadows, or other effects** that add visual clutter.
5. **No tilted or rotated text** — shorten or reposition instead.

---

## Logo Variants

| Variant | When to use |
|---------|-------------|
| **Standard** (teal + gold) | Default / formal use |
| **Inverse** (light on dark) | Dark backgrounds; uses light gray |
| **Teal only** | When gold/yellow would clash with context |
| **Monochrome** (black or white) | Single-color contexts |
| **Logomark** (letters only, no wordmark) | Social media, small spaces |

---

## Design Elements

- **Buttons:** CTA style (gold background with arrow icon) and minimal style (outlined).
- **Hyperlinks:** Teal-colored, underlined.
- **Highlights:** Gold underline/background on key text.
- **Accent arrows:** Gold chevron icons alongside link text.
- **Decorative:** Dot grids, horizontal lines, teal-to-light-teal gradients, bokeh/soft-focus photo treatments, overlapping elements.

