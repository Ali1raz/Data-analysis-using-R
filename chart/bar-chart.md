# Bar Chart

---

## The Question Bar Charts Answer

> *"How much of each category do I have?"*

---

## Two Situations

### Situation 1 — R counts for you

You have raw rows, just want to count them.

```r
library(ggplot2)

ggplot(mpg, aes(x = class)) +
  geom_bar()
```

`mpg` has rows of cars. R counts how many per class automatically.

---

### Situation 2 — You already have the numbers

You have a table with values already calculated.

```r
df <- data.frame(
  city  = c("NY", "LA", "Chicago"),
  sales = c(300, 450, 200)
)

ggplot(df, aes(x = city, y = sales)) +
  geom_col()
```

**Rule:** no pre-calculated numbers → `geom_bar()` / have numbers → `geom_col()`

---

## Add Color

```r
ggplot(df, aes(x = city, y = sales, fill = city)) +
  geom_col()
```

`fill` = inside color of bars. Use `fill`, not `color` (color only does the border).

---

## Flip It Horizontal

```r
ggplot(df, aes(x = sales, y = city)) +
  geom_col()
```

Just swap x and y. Good when category names are long.

---

## Full Clean Example

```r
ggplot(df, aes(x = city, y = sales, fill = city)) +
  geom_col() +
  labs(title = "Sales by City", x = "City", y = "Sales") +
  theme_minimal()
```

---

## Next Steps

1. Try `geom_bar()` on `mpg` with `aes(x = drv)` — see front/rear/4wd counts
2. Add `fill = class` inside `aes()` to stack colors by a second variable
3. Ask me about **grouped bars** or **sorted bars** when ready
