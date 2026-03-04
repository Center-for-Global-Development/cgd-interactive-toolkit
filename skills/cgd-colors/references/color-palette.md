# CGD Color Palette — Complete Reference

## Categorical Colors (Standard Order)

Use colors in this order for categorical/nominal data:

| Position | Name       | Hex       | RGB             |
|----------|------------|-----------|-----------------|
| 1        | Light Teal | `#006970` | 0, 105, 112     |
| 2        | Gold       | `#FFB52C` | 255, 181, 44    |
| 3        | Blue       | `#2D99B5` | 45, 153, 181    |
| 4        | Light Blue | `#BFDEE0` | 191, 222, 224   |
| 5        | Light Gold | `#FEE8BF` | 254, 232, 191   |
| 6        | Teal Gray  | `#85A5AD` | 133, 165, 173   |
| 7        | Dark Gray  | `#394649` | 57, 70, 73      |
| 8        | Light Gray | `#DFE0E2` | 223, 224, 226   |

**None/Neutral:** Light Gray `#DFE0E2`

### Category count guidance

- **1 category:** Light Teal
- **2 categories:** Light Teal, Gold
- **3 categories:** Light Teal, Gold, Blue
- **4 categories:** Light Teal, Gold, Blue, Light Blue
- **5+ categories:** Continue down the table

---

## Sequential Colors (Ordered/Ranked Data)

Center on Light Teal. Expand outward:

| Degrees | Colors (light → dark)                                          |
|---------|----------------------------------------------------------------|
| 1       | Light Teal                                                     |
| 2       | Light Blue, Light Teal                                         |
| 3       | Light Gray, Light Blue, Light Teal                             |
| 4       | Light Gray, Light Blue, Light Teal, Dark Gray                  |
| 5-8     | Light Gray → Teal Gray → Light Blue → Blue → Light Teal → Teal → Dark Gray → Teal Black |

Additional sequential colors:

| Name       | Hex       | RGB           |
|------------|-----------|---------------|
| Teal       | `#0B4C5B` | 11, 76, 91    |
| Teal Black | `#1A272A` | 26, 39, 42    |

---

## Polar Colors (Diverging Data)

Center is neutral; expand toward Light Teal (cool) and Gold (warm):

| Degrees | Colors                                              |
|---------|-----------------------------------------------------|
| 1       | Light Teal                                           |
| 2       | Light Teal, Gold                                     |
| 3       | Light Teal, Teal Gray, Gold                          |
| 4       | Light Teal, Teal Gray, Light Gold, Gold              |
| 5       | Light Teal, Teal Gray, Light Blue, Light Gold, Gold  |

---

## Stoplight Colors

| Label  | Name  | Hex       | RGB           |
|--------|-------|-----------|---------------|
| Good   | Green | `#00896C` | 0, 137, 108   |
| Caution| Gold  | `#FFB52C` | 255, 181, 44  |
| Bad    | Red   | `#D15553` | 209, 85, 83   |

---

## CMYK / PMS Values (for Print)

| Name       | CMYK              | PMS      |
|------------|-------------------|----------|
| Light Teal | 90, 43, 49, 18   | 322 C    |
| Gold       | 0, 32, 93, 0     | 1235 C   |
| Blue       | 76, 24, 22, 0    | 7459 C   |
| Light Blue | 24, 3, 11, 0     | 628 C    |
| Light Gold | 0, 8, 27, 0      | 7506 C   |
| Teal Gray  | 51, 25, 28, 0    | 5493 C   |
| Dark Gray  | 75, 58, 56, 41   | 446 C    |
| Light Gray | 0, 0, 0, 13      | 649 C    |
| Teal       | 93, 59, 48, 31   | 3165 C   |
| Teal Black | 80, 64, 62, 68   | 433 C    |
| Green      | 86, 24, 69, 7    | 569 C    |
| Red        | 13, 81, 66, 2    | 2032 C   |

---

## Quick-Copy Hex Lists

```python
# Categorical (standard order)
CGD_CATEGORICAL = ['#006970', '#FFB52C', '#2D99B5', '#BFDEE0',
                   '#FEE8BF', '#85A5AD', '#394649', '#DFE0E2']

# Sequential (light → dark, centered on Light Teal)
CGD_SEQUENTIAL = ['#DFE0E2', '#85A5AD', '#BFDEE0', '#2D99B5',
                  '#006970', '#0B4C5B', '#394649', '#1A272A']

# Polar (cool → warm)
CGD_POLAR = ['#006970', '#85A5AD', '#BFDEE0', '#FEE8BF', '#FFB52C']

# Stoplight
CGD_STOPLIGHT = {'good': '#00896C', 'caution': '#FFB52C', 'bad': '#D15553'}

# Neutral
CGD_NEUTRAL = '#DFE0E2'
```

```r
# R equivalents
cgd_categorical <- c("#006970", "#FFB52C", "#2D99B5", "#BFDEE0",
                     "#FEE8BF", "#85A5AD", "#394649", "#DFE0E2")

cgd_sequential <- c("#DFE0E2", "#85A5AD", "#BFDEE0", "#2D99B5",
                    "#006970", "#0B4C5B", "#394649", "#1A272A")

cgd_polar <- c("#006970", "#85A5AD", "#BFDEE0", "#FEE8BF", "#FFB52C")

cgd_stoplight <- c(good = "#00896C", caution = "#FFB52C", bad = "#D15553")
```
