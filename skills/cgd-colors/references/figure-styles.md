# CGD Figure Styles — Typography, Lines, and Best Practices

## Typography

| Element     | Font                   | Size  | Case           | Color       |
|-------------|------------------------|-------|----------------|-------------|
| Titles      | Sofia Pro Bold         | 18pt  | Title Case     | Teal `#006970` |
| Axis Labels | Sofia Pro Medium       | 14pt  | Sentence case  | Teal `#006970` |
| Axis Data   | Sofia Pro Regular      | 12pt  | —              | Teal Black `#1A272A` |
| Data Labels | Sofia Pro Light Italic | 12pt  | —              | Teal Black `#1A272A` |
| Notes       | Sofia Pro Light Italic | 12pt  | —              | Teal Black `#1A272A` |

**Fallback fonts** (when Sofia Pro unavailable): Use a clean sans-serif — Calibri, Helvetica, or system sans-serif. Match the weight mappings (Bold→Bold, Medium→Medium, Regular→Regular, Light Italic→Light Italic).

---

## Line Styles

| Element     | Style   | Weight | Color                   | Details                    |
|-------------|---------|--------|-------------------------|----------------------------|
| Axis lines  | Solid   | 1pt    | Teal Black `#1A272A`    |                            |
| Grid lines  | Solid   | 1pt    | Light Gray `#DFE0E2`    |                            |
| Data series | Solid   | 4pt    | Light Teal `#006970`    |                            |
| Projection  | Dashed  | 4pt    | Light Teal `#006970`    | 10pt dash, 4pt gap         |
| Trend lines | Dotted  | 3pt    | Gold `#FFB52C`          | 8pt gap                    |
| Separators  | Dashed  | 1pt    | Teal Gray `#85A5AD`     | 10pt dash, 4pt gap         |
| Indicators  | Solid   | 1pt    | Teal Gray `#85A5AD`     |                            |

---

## Best Practices

1. **No unnecessary grids or backgrounds** — only use when needed for clarity
2. **Direct labeling over legends** — label data series on the chart when possible
3. **No overlapping labels** — break into multiple charts or highlight key points only
4. **No outlines, shadows, or 3D effects** — keep visuals clean
5. **No rotated text** — shorten or reposition labels instead
6. **Meaningful titles** — short, descriptive, convey the takeaway (not just "Chart of X")

---

## File Format

- **Digital (web/social):** PNG, JPG, SVG at 72-144 ppi; use RGB/Hex colors
- **Print:** PNG, JPG, EPS, SVG at 300+ dpi; use CMYK/PMS colors
