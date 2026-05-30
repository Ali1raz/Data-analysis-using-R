## `summarise()` + Aggregation — Full Guide using `mtcars`

---

## Mental Model
> **summarise = collapse many rows into one number per group**
> You lose individual rows — you get a summary table back.

---

# SUMMARISE BASICS

## 1. Basic — whole dataset

```r
summarise(mtcars,
  avg_mpg = mean(mpg),
  max_hp  = max(hp),
  total   = n()
)
```
Returns **one row**. No groups yet.

---

## 2. The essential aggregation functions

```r
summarise(mtcars,
  avg   = mean(mpg),
  med   = median(mpg),
  total = sum(mpg),
  hi    = max(mpg),
  lo    = min(mpg),
  spread = sd(mpg),
  var   = var(mpg),
  count = n(),
  uniq  = n_distinct(cyl)
)
```

---

## 3. `group_by` + `summarise` — the real power

```r
mtcars |>
  group_by(cyl) |>
  summarise(avg_mpg = mean(mpg), .groups = "drop")
```
**Scenario:** Average fuel efficiency per engine class — 3 rows out of 32.

---

## 4. Multi-column summary

```r
mtcars |>
  group_by(cyl) |>
  summarise(
    count    = n(),
    avg_mpg  = mean(mpg),
    avg_hp   = mean(hp),
    max_hp   = max(hp),
    sd_mpg   = sd(mpg),
    .groups  = "drop"
  )
```
**Scenario:** Full engine class profile for a performance report.

---

## 5. `n()` vs `n_distinct()`

```r
mtcars |>
  group_by(cyl) |>
  summarise(
    total_cars  = n(),              # count all rows
    unique_gears = n_distinct(gear) # count unique values
  )
```
**Scenario:** How many orders per customer vs how many unique products they bought.

---

## 6. Conditional count — count rows meeting a condition

```r
mtcars |>
  group_by(cyl) |>
  summarise(
    efficient_count = sum(mpg > 20),        # count TRUEs
    efficient_pct   = mean(mpg > 20) * 100  # % of group
  )
```
**Remember:** `sum(condition)` counts TRUEs. `mean(condition)` gives proportion.

**Scenario:** What % of cars per cylinder class exceed 20 mpg.

---

## 7. `first()`, `last()`, `nth()`

```r
mtcars |>
  group_by(cyl) |>
  summarise(
    first_hp = first(hp),
    last_hp  = last(hp),
    third_hp = nth(hp, 3)
  )
```
**Scenario:** First and last transaction per customer, first entry per session.

---

## 8. Weighted mean

```r
mtcars |>
  group_by(cyl) |>
  summarise(wtd_avg_mpg = weighted.mean(mpg, wt))
```
**Scenario:** Average score weighted by sample size, revenue-weighted price average.

---

## 9. Summarise across multiple columns — `across()`

```r
mtcars |>
  group_by(cyl) |>
  summarise(across(c(mpg, hp, wt), mean))
```

```r
# with renaming
mtcars |>
  group_by(cyl) |>
  summarise(across(c(mpg, hp), list(avg = mean, sd = sd)))
```
Returns `mpg_avg`, `mpg_sd`, `hp_avg`, `hp_sd` automatically.

**Scenario:** Profile every numeric metric by group in one call — no copy-paste.

---

## 10. `summarise` vs `mutate` reminder

```r
# summarise — 32 rows become 3
mtcars |> group_by(cyl) |> summarise(avg = mean(mpg))

# mutate — stays 32 rows, adds group avg to each row
mtcars |> group_by(cyl) |> mutate(avg = mean(mpg))
```

---

# BASE R AGGREGATION

## 11. `aggregate()` — base R, returns a data frame

```r
aggregate(mpg ~ cyl, data = mtcars, FUN = mean)
aggregate(cbind(mpg, hp) ~ cyl, data = mtcars, FUN = mean)
```
**Remember:** `~` means "by" — mpg by cyl.

**Scenario:** Quick group mean without loading dplyr.

---

## 12. `tapply()` — base R, returns named vector

```r
tapply(mtcars$mpg, mtcars$cyl, mean)
tapply(mtcars$hp,  mtcars$cyl, max)
```
**Scenario:** Fast inline check — no pipe needed, works anywhere.

---

## 13. `table()` — frequency counts

```r
table(mtcars$cyl)
table(mtcars$cyl, mtcars$am)   # two-way table
```
**Scenario:** Quick count of categories — how many cars per cyl group.

---

## 14. `prop.table()` — proportions from table

```r
prop.table(table(mtcars$cyl))           # proportions
prop.table(table(mtcars$cyl)) * 100     # percentages
```
**Scenario:** What % of the fleet is 4-cylinder vs 6 vs 8.

---

# Full Pipelines

## 15. filter → group → summarise → arrange

```r
mtcars |>
  filter(wt < 4) |>
  group_by(cyl, am) |>
  summarise(
    count   = n(),
    avg_mpg = mean(mpg),
    avg_hp  = mean(hp),
    .groups = "drop"
  ) |>
  arrange(desc(avg_mpg))
```
**Scenario:** Lightweight car performance report broken down by engine and transmission.

---

## 16. Summarise then mutate — add % of total

```r
mtcars |>
  group_by(cyl) |>
  summarise(count = n(), .groups = "drop") |>
  mutate(pct = count / sum(count) * 100)
```
**Scenario:** Market share — count per group then calculate each group's % of total.

---

## Aggregation Functions Cheat Sheet

| Function | What it does |
|---|---|
| `mean()` | average |
| `median()` | middle value |
| `sum()` | total |
| `min()` / `max()` | extremes |
| `sd()` / `var()` | spread |
| `n()` | row count |
| `n_distinct()` | unique value count |
| `first()` / `last()` | first or last value |
| `sum(cond)` | count of TRUEs |
| `mean(cond)` | proportion of TRUEs |
| `weighted.mean()` | weighted average |

---

## Common Mistakes

```r
# using summarise when you want to keep all rows — use mutate instead

# forgetting n() needs no argument — n(col) is wrong
summarise(mtcars, count = n())     # correct
summarise(mtcars, count = n(mpg))  # wrong

# NAs silently break mean/sum — always check
summarise(mtcars, avg = mean(mpg, na.rm = TRUE))
```

---

## Next Steps
- `pivot_longer()` / `pivot_wider()` — reshape summary tables
- `janitor::tabyl()` — cleaner frequency tables with totals
- `skimr::skim()` — instant full dataset summary
- `tidy models` — move from summaries into modeling
