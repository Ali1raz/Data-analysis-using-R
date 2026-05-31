## Data Visualization in R — Full Guide using `mtcars`

---

## Mental Model
> **ggplot2 builds plots in layers** — data → axes → geometry → styling.

```r
library(ggplot2)

# base template
ggplot(data, aes(x = col1, y = col2)) +
  geom_*()
```
**Remember:** `aes()` = what to plot. `geom_*()` = how to draw it.

---

# SCATTER PLOTS

## What are they
> Show relationship between **two numeric variables**. Each row = one point.

## 1. Basic scatter

```r
ggplot(mtcars, aes(x = wt, y = mpg)) +
  geom_point()
```
**Scenario:** Does heavier car = worse mileage? Spot the trend.

---

## 2. Add color by group

```r
ggplot(mtcars, aes(x = wt, y = mpg, color = factor(cyl))) +
  geom_point(size = 3)
```
**Remember:** wrap numeric grouping columns in `factor()` to treat as categories.

---

## 3. Add trend line

```r
ggplot(mtcars, aes(x = wt, y = mpg)) +
  geom_point() +
  geom_smooth(method = "lm", se = TRUE)   # se = confidence band
```
**Scenario:** Show sales trend over time with uncertainty band.

---

## 4. Add labels to points

```r
library(ggrepel)

mtcars$car <- rownames(mtcars)

ggplot(mtcars, aes(x = wt, y = mpg, label = car)) +
  geom_point() +
  geom_text_repel(size = 3)
```
**Scenario:** Label outlier cars without overlapping text.

---

# BAR & COLUMN CHARTS

## What are they
> Show counts or values **per category**. Great for comparisons.

## 5. Count bars — `geom_bar()`

```r
ggplot(mtcars, aes(x = factor(cyl))) +
  geom_bar()
```
Counts rows automatically. No y needed.

---

## 6. Value bars — `geom_col()`

```r
mtcars |>
  group_by(cyl) |>
  summarise(avg_hp = mean(hp)) |>
  ggplot(aes(x = factor(cyl), y = avg_hp)) +
  geom_col()
```
**Remember:** `geom_bar()` = counts. `geom_col()` = you supply the y value.

---

## 7. Horizontal bars

```r
ggplot(mtcars, aes(x = factor(cyl))) +
  geom_bar() +
  coord_flip()
```
**Scenario:** Long category names — flip so labels are readable.

---

## 8. Stacked and dodged bars

```r
ggplot(mtcars, aes(x = factor(cyl), fill = factor(am))) +
  geom_bar(position = "dodge")    # side by side

ggplot(mtcars, aes(x = factor(cyl), fill = factor(am))) +
  geom_bar(position = "stack")    # stacked

ggplot(mtcars, aes(x = factor(cyl), fill = factor(am))) +
  geom_bar(position = "fill")     # 100% stacked
```
**Scenario:** Compare transmission type proportion across cylinder classes.

---

# HISTOGRAMS & DENSITY

## What are they
> Show **distribution** of one numeric variable. How spread out is the data?

## 9. Histogram

```r
ggplot(mtcars, aes(x = mpg)) +
  geom_histogram(bins = 10, fill = "steelblue", color = "white")
```
**Remember:** `bins` controls bucket width. Try different values.

**Scenario:** Is mpg normally distributed? Skewed? Bimodal?

---

## 10. Density plot — smooth histogram

```r
ggplot(mtcars, aes(x = mpg)) +
  geom_density(fill = "steelblue", alpha = 0.5)
```

---

## 11. Overlapping density by group

```r
ggplot(mtcars, aes(x = mpg, fill = factor(cyl))) +
  geom_density(alpha = 0.4)
```
**Scenario:** Do 4-cyl cars have a different mpg distribution than 8-cyl? Overlap and compare.

---

## 12. Histogram + density together

```r
ggplot(mtcars, aes(x = mpg, y = after_stat(density))) +
  geom_histogram(bins = 10, fill = "steelblue", color = "white") +
  geom_density(color = "red", linewidth = 1)
```

---

# BOX PLOTS & VIOLIN PLOTS

## What are they
> **Box plot** — shows median, IQR, outliers per group.
> **Violin** — shows full distribution shape per group.

## 13. Basic box plot

```r
ggplot(mtcars, aes(x = factor(cyl), y = mpg)) +
  geom_boxplot()
```
**Scenario:** Compare mpg spread across cylinder groups — see median and outliers.

---

## 14. Box plot with points overlaid

```r
ggplot(mtcars, aes(x = factor(cyl), y = mpg)) +
  geom_boxplot(outlier.shape = NA) +
  geom_jitter(width = 0.2, alpha = 0.6)
```
**Remember:** `geom_jitter` spreads points horizontally so they don't overlap.

---

## 15. Violin plot

```r
ggplot(mtcars, aes(x = factor(cyl), y = mpg)) +
  geom_violin(fill = "steelblue", alpha = 0.6)
```
**Scenario:** Box plot hides if data is bimodal — violin reveals the full shape.

---

## 16. Violin + box together

```r
ggplot(mtcars, aes(x = factor(cyl), y = mpg)) +
  geom_violin(fill = "steelblue", alpha = 0.4) +
  geom_boxplot(width = 0.1)
```
**Scenario:** Best of both — shape from violin, summary stats from box.

---

# LINE CHARTS (TIME SERIES)

## What are they
> Show how a value **changes over time**. Needs ordered x-axis.

## 17. Basic line — simulate time in mtcars

```r
df_time <- data.frame(
  time = 1:nrow(mtcars),
  mpg  = mtcars$mpg
)

ggplot(df_time, aes(x = time, y = mpg)) +
  geom_line()
```

---

## 18. Line + points

```r
ggplot(df_time, aes(x = time, y = mpg)) +
  geom_line(color = "steelblue") +
  geom_point(size = 2)
```
**Scenario:** Monthly sales — show trend line and individual data points.

---

## 19. Multiple lines by group

```r
mtcars$car <- rownames(mtcars)
mtcars$time <- 1:nrow(mtcars)

ggplot(mtcars, aes(x = time, y = mpg, color = factor(cyl), group = factor(cyl))) +
  geom_line() +
  geom_point()
```
**Remember:** `group =` tells ggplot which points to connect. Without it lines won't draw correctly.

---

# COLORS, THEMES, FACETS

## 20. Manual colors — `scale_color_manual`

```r
ggplot(mtcars, aes(x = wt, y = mpg, color = factor(cyl))) +
  geom_point(size = 3) +
  scale_color_manual(values = c("4" = "green", "6" = "orange", "8" = "red"))
```

---

## 21. Built-in palettes — `scale_color_brewer`

```r
ggplot(mtcars, aes(x = wt, y = mpg, color = factor(cyl))) +
  geom_point(size = 3) +
  scale_color_brewer(palette = "Set1")
```
Other palettes: `"Set2"`, `"Dark2"`, `"Paired"`, `"Spectral"`.

---

## 22. Continuous color scale

```r
ggplot(mtcars, aes(x = wt, y = mpg, color = hp)) +
  geom_point(size = 3) +
  scale_color_gradient(low = "blue", high = "red")
```
**Scenario:** Show a third variable (hp) through point color on a scatter plot.

---

## 23. Themes — overall look

```r
p <- ggplot(mtcars, aes(x = wt, y = mpg)) + geom_point()

p + theme_minimal()
p + theme_classic()
p + theme_bw()
p + theme_dark()
p + theme_void()
```
**Remember:** `theme_minimal()` for clean reports. `theme_classic()` for publications.

---

## 24. Custom theme tweaks

```r
ggplot(mtcars, aes(x = wt, y = mpg)) +
  geom_point() +
  theme_minimal() +
  theme(
    plot.title   = element_text(size = 16, face = "bold"),
    axis.title   = element_text(size = 12),
    legend.position = "bottom"
  )
```

---

## 25. Labels — `labs()`

```r
ggplot(mtcars, aes(x = wt, y = mpg, color = factor(cyl))) +
  geom_point() +
  labs(
    title    = "Weight vs Fuel Efficiency",
    subtitle = "mtcars dataset",
    x        = "Weight (1000 lbs)",
    y        = "Miles per Gallon",
    color    = "Cylinders",
    caption  = "Source: Motor Trend 1974"
  )
```

---

## 26. Facets — small multiples

```r
# one variable
ggplot(mtcars, aes(x = wt, y = mpg)) +
  geom_point() +
  facet_wrap(~ cyl)

# two variables — grid
ggplot(mtcars, aes(x = wt, y = mpg)) +
  geom_point() +
  facet_grid(am ~ cyl)
```
**Scenario:** Same plot repeated per subgroup — compare patterns across cylinder classes side by side.

---

## 27. Facet with free scales

```r
ggplot(mtcars, aes(x = wt, y = mpg)) +
  geom_point() +
  facet_wrap(~ cyl, scales = "free")
```
**Scenario:** Each group has very different ranges — let axes adjust per panel.

---

# Full Publication Plot

## 28. Everything together

```r
ggplot(mtcars, aes(x = wt, y = mpg, color = factor(cyl))) +
  geom_point(size = 3, alpha = 0.8) +
  geom_smooth(method = "lm", se = FALSE, linetype = "dashed") +
  scale_color_brewer(palette = "Set1") +
  facet_wrap(~ cyl) +
  labs(
    title    = "Weight vs Efficiency by Cylinder Class",
    x        = "Weight (1000 lbs)",
    y        = "Miles per Gallon",
    color    = "Cylinders"
  ) +
  theme_minimal() +
  theme(
    plot.title      = element_text(face = "bold", size = 14),
    legend.position = "bottom"
  )
```

---

## Geom Cheat Sheet

| Plot type | Geom |
|---|---|
| Scatter | `geom_point()` |
| Line | `geom_line()` |
| Bar counts | `geom_bar()` |
| Bar values | `geom_col()` |
| Histogram | `geom_histogram()` |
| Density | `geom_density()` |
| Box plot | `geom_boxplot()` |
| Violin | `geom_violin()` |
| Jitter | `geom_jitter()` |
| Trend line | `geom_smooth()` |
| Text labels | `geom_text()` |

---

## Common Mistakes

```r
# forgetting factor() on numeric group columns — plots wrong colors
aes(color = cyl)           # treats as continuous
aes(color = factor(cyl))   # treats as categorical

# forgetting group= in line charts — lines won't connect
aes(x = time, y = val, color = grp)              # broken
aes(x = time, y = val, color = grp, group = grp) # correct

# stacking geoms in wrong order — points hidden under boxplot
geom_boxplot() + geom_jitter()   # jitter on top — correct
geom_jitter() + geom_boxplot()   # box covers points — wrong
```

---

## Next Steps
- `patchwork` — combine multiple plots into one figure
- `plotly::ggplotly()` — make any ggplot interactive in one line
- `scales` package — format axis labels as %, $, commas
- `gganimate` — animate plots over time
- `ggthemes` — extra themes including Economist, FiveThirtyEight, WSJ
