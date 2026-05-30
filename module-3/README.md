# Module 03 — Visualization with ggplot2

---

## Setup

```r
install.packages("ggplot2")  # run once
library(ggplot2)
data(iris)
```

Every ggplot2 plot follows the same pattern:

```r
ggplot(data, aes(x = col, y = col, ...)) +
  geom_*() +
  theme_*() +
  labs(...)
```

- `ggplot()` sets the dataset and aesthetic mappings
- `aes()` maps columns to visual properties — x, y, color, fill, size, shape
- `geom_*()` decides the chart type
- layers are added with `+`

---

## 3.1 Scatter Plot

Shows the relationship between two continuous variables.

```r
ggplot(iris, aes(x = Sepal.Length, y = Petal.Length)) +
  geom_point() +
  theme_minimal() +
  labs(
    title = "Sepal Length vs Petal Length",
    x     = "Sepal Length (cm)",
    y     = "Petal Length (cm)"
  )
```

### Add color by species

```r
ggplot(iris, aes(x = Sepal.Length, y = Petal.Length, color = Species)) +
  geom_point(size = 2, alpha = 0.7) +
  theme_minimal() +
  labs(
    title = "Sepal Length vs Petal Length by Species",
    x     = "Sepal Length (cm)",
    y     = "Petal Length (cm)"
  )
```

- `color = Species` inside `aes()` maps species to point color automatically
- `size` controls point size — a fixed number, not mapped to data
- `alpha` controls transparency — 0 is invisible, 1 is solid

### Add a trend line

```r
ggplot(iris, aes(x = Sepal.Length, y = Petal.Length, color = Species)) +
  geom_point(size = 2, alpha = 0.6) +
  geom_smooth(method = "lm", se = FALSE) +
  theme_minimal() +
  labs(title = "Scatter with Linear Trend Lines")
```

- `geom_smooth(method = "lm")` draws a regression line per group
- `se = FALSE` removes the confidence interval ribbon

---

## 3.2 Bar Chart

Shows counts or values per category.

### Count plot — how many flowers per species

```r
ggplot(iris, aes(x = Species)) +
  geom_bar(fill = "steelblue") +
  theme_minimal() +
  labs(
    title = "Number of Flowers per Species",
    x     = "Species",
    y     = "Count"
  )
```

`geom_bar()` counts rows automatically — no need to pre-summarize.

### Column chart — mean sepal length per species

When you have a pre-computed value to plot, use `geom_col()` instead.

```r
library(dplyr)

iris_means <- iris |>
  group_by(Species) |>
  summarise(mean_sepal = mean(Sepal.Length))

ggplot(iris_means, aes(x = Species, y = mean_sepal, fill = Species)) +
  geom_col() +
  theme_minimal() +
  labs(
    title = "Mean Sepal Length per Species",
    x     = "Species",
    y     = "Mean Sepal Length (cm)"
  )
```

- `fill` colors the bar interior; `color` would color the border
- `fill = Species` inside `aes()` maps species to fill color automatically

---

## 3.3 Histogram

Shows the distribution of a single continuous variable.

```r
ggplot(iris, aes(x = Sepal.Length)) +
  geom_histogram(binwidth = 0.3, fill = "steelblue", color = "white") +
  theme_minimal() +
  labs(
    title = "Distribution of Sepal Length",
    x     = "Sepal Length (cm)",
    y     = "Count"
  )
```

- `binwidth` controls how wide each bar is — experiment with this value
- `color = "white"` adds a white border between bars for readability

### Overlapping histograms by species

```r
ggplot(iris, aes(x = Sepal.Length, fill = Species)) +
  geom_histogram(binwidth = 0.3, alpha = 0.5, position = "identity") +
  theme_minimal() +
  labs(
    title = "Sepal Length Distribution by Species",
    x     = "Sepal Length (cm)",
    y     = "Count"
  )
```

- `position = "identity"` overlaps bars instead of stacking them
- `alpha = 0.5` makes bars semi-transparent so all three are visible

### Density plot — smoothed version of a histogram

```r
ggplot(iris, aes(x = Sepal.Length, fill = Species)) +
  geom_density(alpha = 0.4) +
  theme_minimal() +
  labs(
    title = "Density of Sepal Length by Species",
    x     = "Sepal Length (cm)",
    y     = "Density"
  )
```

---

## 3.4 Box Plot

Shows median, quartiles, and outliers — excellent for comparing distributions across groups.

```r
ggplot(iris, aes(x = Species, y = Sepal.Length, fill = Species)) +
  geom_boxplot(alpha = 0.7) +
  theme_minimal() +
  labs(
    title = "Sepal Length by Species",
    x     = "Species",
    y     = "Sepal Length (cm)"
  )
```

What each part of the box means:

| Element | Meaning |
|---------|---------|
| Middle line | Median (50th percentile) |
| Box bottom | 25th percentile (Q1) |
| Box top | 75th percentile (Q3) |
| Whiskers | 1.5 × IQR from box edges |
| Dots beyond whiskers | Outliers |

### Box plot with raw data overlaid

```r
ggplot(iris, aes(x = Species, y = Sepal.Length, fill = Species)) +
  geom_boxplot(alpha = 0.5, outlier.shape = NA) +
  geom_jitter(width = 0.15, alpha = 0.4, size = 1.5) +
  theme_minimal() +
  labs(title = "Sepal Length with Individual Points")
```

- `geom_jitter()` adds the raw points with a small horizontal jitter so they don't overlap
- `outlier.shape = NA` hides the boxplot's own outlier dots since `geom_jitter` already shows all points

### Violin plot — box plot + density shape

```r
ggplot(iris, aes(x = Species, y = Petal.Length, fill = Species)) +
  geom_violin(alpha = 0.6) +
  geom_boxplot(width = 0.15, fill = "white", alpha = 0.8) +
  theme_minimal() +
  labs(
    title = "Petal Length Distribution by Species",
    x     = "Species",
    y     = "Petal Length (cm)"
  )
```

The inner narrow boxplot shows the summary statistics while the violin shape shows where data is dense.

---

## 3.5 Color Control

### Use a manual color palette

```r
ggplot(iris, aes(x = Sepal.Length, y = Petal.Length, color = Species)) +
  geom_point(size = 2) +
  scale_color_manual(values = c(
    "setosa"     = "#E41A1C",
    "versicolor" = "#377EB8",
    "virginica"  = "#4DAF4A"
  )) +
  theme_minimal()
```

### Use a built-in palette

```r
# ColorBrewer palette — good for publications
ggplot(iris, aes(x = Sepal.Length, y = Petal.Length, color = Species)) +
  geom_point(size = 2) +
  scale_color_brewer(palette = "Set1") +
  theme_minimal()
```

Other useful `scale_*` functions:

| Function | Use |
|----------|-----|
| `scale_color_manual()` | Custom colors for lines/points |
| `scale_fill_manual()` | Custom colors for bars/boxes |
| `scale_color_brewer()` | ColorBrewer palettes for discrete data |
| `scale_color_gradient()` | Gradient for continuous color mapping |

---

## 3.6 Facets

Facets split a single plot into a grid of panels — one per group. This avoids overplotting and makes comparisons cleaner.

### `facet_wrap()` — wrap panels into rows

```r
ggplot(iris, aes(x = Sepal.Length, y = Petal.Length)) +
  geom_point(color = "steelblue", alpha = 0.6) +
  facet_wrap(~Species) +
  theme_minimal() +
  labs(title = "Sepal vs Petal Length — one panel per species")
```

### `facet_wrap()` with free axis scales

By default all panels share the same axis range. Use `scales = "free"` to let each panel scale independently.

```r
ggplot(iris, aes(x = Sepal.Length)) +
  geom_histogram(binwidth = 0.3, fill = "steelblue", color = "white") +
  facet_wrap(~Species, scales = "free_y") +
  theme_minimal() +
  labs(title = "Sepal Length Distribution per Species")
```

### `facet_grid()` — two-variable grid

Useful when you have two categorical variables to split by. Here we create a second grouping variable to demonstrate.

```r
iris$size_group <- ifelse(iris$Sepal.Length > median(iris$Sepal.Length), "Large", "Small")

ggplot(iris, aes(x = Petal.Length, y = Petal.Width, color = Species)) +
  geom_point(alpha = 0.6) +
  facet_grid(size_group ~ Species) +
  theme_minimal() +
  labs(title = "Petal Dimensions by Species and Sepal Size Group")
```

---

## 3.7 Themes & Labels

### Themes

```r
p <- ggplot(iris, aes(x = Species, y = Sepal.Length, fill = Species)) +
  geom_boxplot()

p + theme_minimal()   # clean, no background
p + theme_classic()   # white background, axis lines only
p + theme_bw()        # white background with grid
p + theme_void()      # completely blank
```

### Labels and annotations

```r
ggplot(iris, aes(x = Sepal.Length, y = Petal.Length, color = Species)) +
  geom_point(size = 2, alpha = 0.7) +
  theme_minimal() +
  labs(
    title    = "Sepal vs Petal Length",
    subtitle = "150 iris flowers across 3 species",
    caption  = "Source: Fisher's Iris Dataset (1936)",
    x        = "Sepal Length (cm)",
    y        = "Petal Length (cm)",
    color    = "Species"
  )
```

### Customize text sizes

```r
ggplot(iris, aes(x = Sepal.Length, y = Petal.Length, color = Species)) +
  geom_point() +
  theme_minimal() +
  theme(
    plot.title   = element_text(size = 16, face = "bold"),
    axis.title   = element_text(size = 12),
    legend.title = element_text(size = 11),
    legend.position = "bottom"
  ) +
  labs(title = "Custom Styled Plot")
```

---

## 3.8 Saving a Plot

```r
p <- ggplot(iris, aes(x = Sepal.Length, y = Petal.Length, color = Species)) +
  geom_point(size = 2) +
  theme_minimal()

ggsave("iris_scatter.png", plot = p, width = 8, height = 5, dpi = 300)
```

---

## Exercises

**1.** Make a scatter plot of `Petal.Width` vs `Petal.Length`, colored by `Species`. Add a linear trend line per species.

**2.** Plot a histogram of `Petal.Width` with `binwidth = 0.1`. Then facet it by `Species`.

**3.** Create a box plot of all four numeric columns. To do this, reshape the data first:

```r
library(tidyr)
iris_long <- iris |>
  pivot_longer(cols = -Species, names_to = "measurement", values_to = "value")

ggplot(iris_long, aes(x = Species, y = value, fill = Species)) +
  geom_boxplot() +
  facet_wrap(~measurement, scales = "free_y") +
  theme_minimal()
```

**4.** Recreate the column chart from 3.2 but use `scale_fill_manual()` to set your own three colors.

**5.** Take any plot from this module and move the legend to the bottom using `theme(legend.position = "bottom")`.

---

## Summary

| Chart type | Geom | Best for |
|------------|------|---------|
| Scatter plot | `geom_point()` | Relationship between two continuous variables |
| Bar chart | `geom_bar()` / `geom_col()` | Counts or values per category |
| Histogram | `geom_histogram()` | Distribution of one continuous variable |
| Density plot | `geom_density()` | Smoothed distribution |
| Box plot | `geom_boxplot()` | Distribution summary + outliers across groups |
| Violin plot | `geom_violin()` | Distribution shape across groups |
| Trend line | `geom_smooth()` | Fitted line over a scatter plot |
| Facets | `facet_wrap()` / `facet_grid()` | Split any plot into panels by group |
