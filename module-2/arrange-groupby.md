## `arrange()` + `group_by()` — Full Guide using `mtcars`

---

## Mental Model
> **arrange = sort rows** — changes order, nothing else.
> **group_by = split into invisible buckets** — all verbs after it act per group.

---

# ARRANGE

## 1. Basic — ascending

```r
arrange(mtcars, mpg)        # lowest mpg first
arrange(mtcars, wt)
```

---

## 2. Descending — `desc()`

```r
arrange(mtcars, desc(mpg))  # highest mpg first
arrange(mtcars, desc(hp))
```

---

## 3. Multiple columns — tiebreaker logic

```r
arrange(mtcars, cyl, desc(mpg))
```
Sort by cyl ascending, then within same cyl sort by mpg descending.

**Scenario:** Leaderboard — rank by division first, then score within division.

---

## 4. Arrange after mutate — sort on derived column

```r
mtcars |>
  mutate(hp_per_cyl = hp / cyl) |>
  arrange(desc(hp_per_cyl))
```
**Scenario:** Find most power-dense engines — column didn't exist before.

---

## 5. `arrange` does NOT affect calculations

```r
# mean(mpg) gives same result regardless of arrange order
# arrange is cosmetic — use it last in pipelines
```
**Remember:** put `arrange()` at the **end** of your pipeline, it's for presentation.

---

# GROUP_BY

## 6. Basic — `group_by` + `summarise`

```r
mtcars |>
  group_by(cyl) |>
  summarise(avg_mpg = mean(mpg))
```
**Scenario:** Average fuel efficiency per engine size class.

---

## 7. Multiple summary stats at once

```r
mtcars |>
  group_by(cyl) |>
  summarise(
    avg_mpg = mean(mpg),
    max_hp  = max(hp),
    count   = n(),
    sd_wt   = sd(wt)
  )
```
**Scenario:** Full group profile — count, average, spread per category.

---

## 8. Group by multiple columns

```r
mtcars |>
  group_by(cyl, am) |>
  summarise(avg_mpg = mean(mpg), .groups = "drop")
```
`am` = transmission (0 = auto, 1 = manual). Returns one row per cyl+am combo.

**Remember:** always add `.groups = "drop"` to avoid sticky group warnings.

---

## 9. `group_by` + `mutate` — add group stats WITHOUT collapsing rows

```r
mtcars |>
  group_by(cyl) |>
  mutate(avg_mpg_in_group = mean(mpg))
```
Every row keeps its data **plus** gets the group average added.

**Scenario:** Compare each car's mpg to its cylinder-class average — rows stay intact.

---

## 10. `group_by` + `filter` — filter within groups

```r
mtcars |>
  group_by(cyl) |>
  filter(mpg == max(mpg))    # best mpg car in each cyl group
```
**Scenario:** Top performer per region, most recent record per user, highest sale per category.

---

## 11. `group_by` + `mutate` + rank — rank within groups

```r
mtcars |>
  group_by(cyl) |>
  mutate(rank_in_group = min_rank(desc(mpg))) |>
  filter(rank_in_group <= 2)    # top 2 per cyl group
```
**Scenario:** Top 2 salespeople per region, top 3 products per category.

---

## 12. `ungroup()` — release groups

```r
mtcars |>
  group_by(cyl) |>
  mutate(avg = mean(mpg)) |>
  ungroup() |>
  arrange(desc(avg))
```
**Remember:** after `group_by`, your df is grouped until you `ungroup()`. Forgetting this causes silent bugs downstream.

---

## 13. `count()` — shortcut for group_by + summarise + n()

```r
count(mtcars, cyl)
count(mtcars, cyl, am)          # two-way count
count(mtcars, cyl, sort = TRUE) # sorted descending
```
**Scenario:** Quick frequency table — how many cars per cylinder class.

---

# Together — Full Pipeline

## 14. filter + mutate + group_by + summarise + arrange

```r
mtcars |>
  filter(wt < 4) |>
  mutate(kpl = mpg * 0.425) |>
  group_by(cyl) |>
  summarise(
    avg_kpl  = mean(kpl),
    max_hp   = max(hp),
    count    = n(),
    .groups  = "drop"
  ) |>
  arrange(desc(avg_kpl))
```
**Scenario:** Fuel efficiency report for lightweight cars — grouped by engine size, sorted best to worst.

---

## 15. group_by + mutate vs group_by + summarise

```r
# summarise — collapses to one row per group
mtcars |> group_by(cyl) |> summarise(avg = mean(mpg))
# returns 3 rows (one per cyl value)

# mutate — keeps all rows, adds group stat
mtcars |> group_by(cyl) |> mutate(avg = mean(mpg))
# returns 32 rows, each with its group average
```
**Remember:** summarise = **shrink**. mutate = **enrich**.

---

## Cheat Sheet

| Goal | Tool |
|---|---|
| Sort ascending | `arrange(df, col)` |
| Sort descending | `arrange(df, desc(col))` |
| Tiebreaker sort | `arrange(df, a, desc(b))` |
| Aggregate per group | `group_by + summarise` |
| Add group stat to rows | `group_by + mutate` |
| Best row per group | `group_by + filter(x == max(x))` |
| Top N per group | `group_by + mutate(rank) + filter` |
| Quick frequency | `count(df, col)` |
| Release groups | `ungroup()` |

---

## Common Mistakes

```r
# forgetting .groups = "drop" — leaves sticky grouping
mtcars |> group_by(cyl) |> summarise(avg = mean(mpg))
# add: .groups = "drop"

# arrange before summarise — pointless, summarise ignores order
# always arrange LAST

# forgetting ungroup() before a second mutate
# silent bug — second mutate still acts per group
```

---

## Next Steps
- `summarise()` deep dive — `first()`, `last()`, `n_distinct()`
- `rowwise()` — group by each individual row
- `pivot_longer()` / `pivot_wider()` — reshape after summarising
