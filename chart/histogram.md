# Histogram & Line Chart

---

## Histogram

### The Question It Answers
> *"How is my data spread out?"*

### When to Use
One column, continuous numbers. You want to see where values cluster.

### Example

```r
library(ggplot2)

ggplot(mtcars, aes(x = mpg)) +
  geom_histogram(bins = 10, fill = "steelblue", color = "white")
```

**Scenario:** Are most cars getting 15mpg or 25mpg? Where do they bunch up?

---

### What `bins` Does

```r
geom_histogram(bins = 5)   # few wide bars, less detail
geom_histogram(bins = 30)  # many narrow bars, more detail
```

Start with `bins = 10`, adjust until it looks right.

---

### Mental Model
> Histogram = cut your data into buckets, count what falls in each

No y column needed — R counts for you.

---

---

## Line Chart

### The Question It Answers
> *"How does something change over time?"*

### When to Use
X axis is time or ordered sequence. Y is a value.

### Example

```r
ggplot(economics, aes(x = date, y = unemploy)) +
  geom_line()
```

**Scenario:** How did unemployment rise and fall over decades?

---

### Add Points on Top

```r
ggplot(economics, aes(x = date, y = unemploy)) +
  geom_line() +
  geom_point()
```

Good when you have few data points and want to see each one clearly.

---

### Multiple Lines

```r
df <- data.frame(
  month  = rep(1:6, 2),
  sales  = c(10,15,13,20,18,25, 8,12,10,15,14,20),
  region = rep(c("North","South"), each = 6)
)

ggplot(df, aes(x = month, y = sales, color = region)) +
  geom_line()
```

`color = region` draws one line per region automatically.

---

## Side by Side

| | Histogram | Line Chart |
|---|---|---|
| Columns needed | 1 (x only) | 2 (x + y) |
| X axis | numbers | time / order |
| Shows | distribution | change over time |
| geom | `geom_histogram()` | `geom_line()` |
