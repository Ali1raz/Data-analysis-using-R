Pie charts are not in `ggplot2` natively — you build them by transforming a bar chart into polar coordinates.

---

## The Trick

```r
ggplot(df, aes(x = "", y = sales, fill = city)) +
  geom_col() +
  coord_polar(theta = "y")
```

`coord_polar()` bends the bar chart into a circle.

---

## Full Example

```r
library(ggplot2)

df <- data.frame(
  city  = c("NY", "LA", "Chicago"),
  sales = c(300, 450, 200)
)

ggplot(df, aes(x = "", y = sales, fill = city)) +
  geom_col(width = 1) +
  coord_polar(theta = "y") +
  labs(title = "Sales by City") +
  theme_void()
```

`theme_void()` removes the axes and grid — looks clean.

---

## What Each Line Does

| Line | Purpose |
|---|---|
| `x = ""` | no x axis, just one stack |
| `y = sales` | slice size = your numbers |
| `fill = city` | each city gets a color |
| `coord_polar(theta = "y")` | wraps y axis into a circle |
| `theme_void()` | removes all background clutter |

---

## Mental Model

> Pie chart = stacked bar chart + bent into a circle
