---
name: cgd-colors
description: "CGD (Center for Global Development) data visualization style guide. Provides the official CGD color palette, typography, line styles, and best practices for creating publication-ready figures and charts. Use when creating or restyling figures for CGD blogs, papers, briefs, or any CGD-branded publication. Applies to matplotlib, ggplot2, R base graphics, Datawrapper, Flourish, or any plotting tool. Trigger on: CGD colors, CGD style, CGD palette, CGD figures, CGD brand, or when producing visuals for a CGD publication."
metadata:
  author: Dany Bahar
---

# CGD Data Visualization Style Guide

## Core Palette (Categorical Order)

| #  | Name       | Hex       |
|----|------------|-----------|
| 1  | Light Teal | `#006970` |
| 2  | Gold       | `#FFB52C` |
| 3  | Blue       | `#2D99B5` |
| 4  | Light Blue | `#BFDEE0` |
| 5+ | See full palette in references |

Neutral/None: Light Gray `#DFE0E2`

## Workflow

1. **Determine data type** — categorical, sequential, or polar (diverging)
2. **Count distinct series** — pick that many colors from the appropriate palette
3. **Read the full palette** from `references/color-palette.md` for exact hex/RGB/CMYK values and copy-paste code snippets (Python and R)
4. **Apply figure styles** from `references/figure-styles.md` for typography, line weights, and best practices
5. **Export** as PNG/SVG (digital: 144+ ppi, print: 300+ dpi)

## Quick Rules

- Always use colors in the prescribed order — do not skip or reorder
- For 2 categories: Light Teal + Gold
- For highlight-vs-background scatter: use Blue (muted, low alpha) + Gold (highlight)
- Stoplight: Green `#00896C` / Gold `#FFB52C` / Red `#D15553`
- Titles in Teal, axis labels in Teal, data text in Teal Black `#1A272A`
- No outlines, shadows, 3D, or rotated text
- Direct label over legend when possible

## References

- **`references/color-palette.md`** — Full color specs (hex, RGB, CMYK, PMS) for all palette types (categorical, sequential, polar, stoplight). Includes ready-to-paste Python and R color vectors.
- **`references/figure-styles.md`** — Typography specs (font, size, case, color), line style specs (weight, dash patterns), best practices checklist, and file format guidance.
