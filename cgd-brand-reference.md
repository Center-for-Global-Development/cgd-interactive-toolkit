# CGD Branding Reference

## Colors

### Primary Palette

| Name | Hex | RGB | Usage |
|------|-----|-----|-------|
| **Teal** | `#0B4C5B` | 11, 76, 91 | Logo, primary brand color |
| **Gold** | `#FFB52C` | 255, 181, 44 | Logo, accents |
| **Teal Gray** | `#85A5AD` | 133, 165, 173 | Logo (teal at 50% opacity) |
| **Light Teal** | `#006970` | 0, 105, 112 | Secondary teal |
| **Cream** | `#F3F6F7` | 243, 246, 247 | Backgrounds |
| **Dark Gray** | `#394649` | 57, 70, 73 | Body text, dark UI elements |
| **Teal Black** | `#1A272A` | 26, 39, 42 | Darkest tone, near-black |

### Supplementary Palette

| Name | Hex | RGB | Usage |
|------|-----|-----|-------|
| **Blue** | `#2D99B5` | 45, 153, 181 | |
| **Light Blue** | `#BFDEE0` | 191, 222, 224 | |
| **Light Gold** | `#FEE8BF` | 254, 232, 191 | |
| **Light Gray** | `#DFE0E2` | 223, 224, 226 | |

### Stoplight / Status Colors

| Name | Hex | RGB |
|------|-----|-----|
| **Red** | `#D15553` | 209, 85, 83 |
| **Green** | `#00896C` | 0, 137, 108 |

### CSS Custom Properties

```css
:root {
  /* Primary */
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

### Tailwind Config

```js
colors: {
  cgd: {
    teal: '#0B4C5B',
    gold: '#FFB52C',
    'teal-gray': '#85A5AD',
    'light-teal': '#006970',
    cream: '#F3F6F7',
    'dark-gray': '#394649',
    'teal-black': '#1A272A',
    blue: '#2D99B5',
    'light-blue': '#BFDEE0',
    'light-gold': '#FEE8BF',
    'light-gray': '#DFE0E2',
    red: '#D15553',
    green: '#00896C',
  }
}
```

## Fonts

| Font | Use | Source |
|------|-----|--------|
| **Sofia Pro** | Titles, headings, graphics, web UI | Adobe Fonts |
| **Bitter** | Body text, print | Google Fonts |

### Font Loading

```html
<!-- Bitter from Google Fonts -->
<link href="https://fonts.googleapis.com/css2?family=Bitter:ital,wght@0,400;0,700;1,400&display=swap" rel="stylesheet">
```

Sofia Pro requires an Adobe Fonts subscription or self-hosting a licensed copy. For quick prototypes where Sofia Pro isn't available, consider system sans-serif or a geometric sans like Inter as a stand-in.

## Logo Variants

| Variant | When to use |
|---------|-------------|
| **Standard** (teal + gold) | Default / formal use |
| **Inverse** (light on dark) | Dark backgrounds; uses light gray |
| **Teal only** | When gold/yellow would clash with context |
| **Monochrome** (black or white) | Single-color contexts |
| **Logomark** (letters only, no wordmark) | Social media, small spaces |

## Design Elements

- **Buttons**: CTA style (gold background with arrow icon) and minimal style (outlined)
- **Hyperlinks**: Teal-colored, underlined
- **Highlights**: Gold underline/background on key text
- **Accent arrows**: Gold chevron icons alongside link text
- **Decorative**: Dot grids, horizontal lines, teal-to-light-teal gradients, bokeh/soft-focus photo treatments, overlapping elements
