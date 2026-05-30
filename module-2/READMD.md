## `filter()` + `select()` + `mutate()` Together

(more examples in their individual files)

---

## Mental Model
> **Filter rows → Shape columns → Create new columns**
> Always in that order — filter early to work on less data.

---

## The Order Rule

```r
df |>
  filter()   # 1. shrink rows first
  select()   # 2. keep only columns you need
  mutate()   # 3. create new columns from what's left
```

---

## 1. Basic together

```r
mtcars |>
  filter(cyl == 4) |>
  select(mpg, hp, wt) |>
  mutate(kpl = mpg * 0.425)
```
**Scenario:** Analyst wants a clean small-car table with metric units only.

---

## 2. Mutate THEN filter — when you filter on a derived column

```r
mtcars |>
  mutate(hp_per_cyl = hp / cyl) |>
  filter(hp_per_cyl > 30) |>
  select(hp, cyl, hp_per_cyl)
```
**Remember:** if your filter condition uses a **new column**, mutate must come first.

**Scenario:** Flag high power-density engines — column doesn't exist yet so create it first.

---

## 3. Select AFTER mutate — keep only what matters

```r
mtcars |>
  filter(mpg > 20) |>
  mutate(
    kpl = mpg * 0.425,
    size = ifelse(cyl <= 4, "small", "large")
  ) |>
  select(kpl, size, hp)
```
**Scenario:** Final reporting table — hide raw/intermediate columns, show only clean output.

---

## 4. Multi-condition filter + rename in select + case_when in mutate

```r
mtcars |>
  filter(cyl %in% c(4, 6), wt < 3.5) |>
  select(mileage = mpg, cylinders = cyl, horsepower = hp) |>
  mutate(tier = case_when(
    mileage > 25 ~ "economy",
    mileage > 18 ~ "standard",
    .default     = "poor"
  ))
```
**Scenario:** Build a car rating table for a consumer report — filter relevant cars, clean names, add tier label.

---

## 5. Using `across()` after filtering and selecting

```r
mtcars |>
  filter(cyl == 8) |>
  select(mpg, hp, wt, disp) |>
  mutate(across(everything(), ~ round(., 1)))
```
**Scenario:** Clean up decimal noise on a subset of heavy cars for a presentation.

---

## 6. Full realistic pipeline

```r
mtcars |>
  filter(cyl %in% c(4, 6), !is.na(hp), mpg > mean(mpg)) |>
  select(mpg, cyl, hp, wt) |>
  mutate(
    kpl           = round(mpg * 0.425, 2),
    hp_per_cyl    = round(hp / cyl, 1),
    weight_class  = ifelse(wt > 3, "heavy", "light"),
    mpg_rank      = min_rank(desc(mpg))
  ) |>
  select(-mpg) |>                        # drop raw mpg, keep kpl
  arrange(mpg_rank)
```
**Scenario:** Performance report — only fuel-efficient mid-size cars, converted units, ranked and classified.

---

## 7. Save intermediate steps for debugging

```r
step1 <- mtcars |> filter(cyl == 4)
step2 <- step1  |> select(mpg, hp, wt)
step3 <- step2  |> mutate(kpl = mpg * 0.425)
```
**Remember:** when your pipeline breaks, split it into named steps and check each one with `head()` or `glimpse()`.

---

## Order Decision Tree

```
Need to filter on a NEW column?
  YES → mutate first, then filter
  NO  → filter first (always filter early)

Need to rename columns?
  YES → do it in select, before mutate (so mutate uses new names)

Need to drop raw source columns after mutate?
  YES → add a second select() at the end or use transmute()
```

---

## Common Mistakes

```r
# filtering on a column you already dropped with select
mtcars |>
  select(mpg, hp) |>
  filter(cyl == 4)        # ERROR — cyl no longer exists

# fix — filter before select
mtcars |>
  filter(cyl == 4) |>
  select(mpg, hp)
```

---

## Cheat Sheet

| Situation | Order |
|---|---|
| Standard flow | filter → select → mutate |
| Filter on derived col | mutate → filter → select |
| Clean output table | filter → mutate → select (drop raws at end) |
| Rename then compute | filter → select(rename) → mutate |

---

## Next Steps
- `arrange()` — sort your final result
- `summarise()` — collapse to summary stats after filtering
- `group_by() + mutate()` — ranked/normalized within groups
