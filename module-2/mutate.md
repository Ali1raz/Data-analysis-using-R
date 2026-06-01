## `mutate()` — Full Guide using `mtcars`

---

## Mental Model
> **"mutate = columns you create or change"** — existing rows stay, you're adding or modifying columns.

---

## 1. Basic — create a new column

```r
mutate(mtcars, kpl = mpg * 0.425)
```
**Remember:** left side = new column name, right side = formula.

---

## 2. Multiple columns at once

```r
mutate(mtcars,
  kpl = mpg * 0.425,
  hp_per_cyl = hp / cyl,
  wt_kg = wt * 453.6)
```
**Scenario:** Unit conversions for an international report — do all at once.

---

## 3. Reference a just-created column

```r
mutate(mtcars,
  kpl = mpg * 0.425,
  kpl_rounded = round(kpl, 1))    # uses kpl from same mutate
```
**Remember:** columns created earlier in the same `mutate()` are available immediately.

---

## 4. Modify existing column — overwrite it

```r
mutate(mtcars, mpg = mpg * 1.1)       # increase mpg by 10%
mutate(mtcars, wt = round(wt, 0))     # round in place
```
**Scenario:** Apply a correction factor to a measurement column.

---

## 5. `case_when()` — if/else logic

```r
mutate(mtcars, size = case_when(
  cyl == 4 ~ "small",
  cyl == 6 ~ "medium",
  cyl == 8 ~ "large",
  .default = "unknown"
))
```
**Remember:** conditions go top to bottom, first match wins. Always add `.default`.

**Scenario:** Segment customers by spend tier, classify products by price range.

---

## 6. `ifelse()` — simple binary condition

```r
mutate(mtcars, efficient = ifelse(mpg > 20, "yes", "no"))
```
**Scenario:** Flag rows as pass/fail, above/below threshold, yes/no.

---

## 7. Ranking — `rank()`, `min_rank()`, `dense_rank()`

```r
mutate(mtcars, mpg_rank = min_rank(desc(mpg)))
```
**Scenario:** Rank cars by fuel efficiency. Rank salespeople by revenue.

---

## 8. Cumulative calculations

```r
mutate(mtcars, cum_hp = cumsum(hp))
mutate(mtcars, running_avg = cummean(mpg))
```
**Scenario:** Running total of sales, cumulative page views over time.

---

## 9. `ntile()` — split into n buckets

```r
mutate(mtcars, mpg_quartile = ntile(mpg, 4))
```
Assigns 1–4 based on where each row falls.

**Scenario:** Split customers into high/mid/low value thirds. Create score buckets.

---

## 10. `lag()` and `lead()` — previous/next row value

```r
mutate(mtcars, prev_mpg = lag(mpg))
mutate(mtcars, next_mpg = lead(mpg))
mutate(mtcars, mpg_change = mpg - lag(mpg))
```
**Scenario:** Month-over-month change in sales. Day-over-day price difference.

---

## 11. `across()` — mutate multiple columns at once

```r
mutate(mtcars, across(c(mpg, hp, wt), round))
mutate(mtcars, across(where(is.numeric), ~ . / max(.)))  # normalize all
```
**Remember:** `~` starts a formula, `.` means "current column value".

**Scenario:** Normalize all numeric features before running a ML model.

---

## 12. String operations inside mutate

```r
mtcars$car <- rownames(mtcars)

mutate(mtcars,
  car_upper = toupper(car),
  brand = sub(" .*", "", car))    # extract first word = brand
```
**Scenario:** Clean messy text columns — extract brand from product name.

---

## 13. `transmute()` — mutate but drop all other columns

```r
transmute(mtcars,
  kpl = mpg * 0.425,
  hp_per_cyl = hp / cyl)
```
Returns **only** the new columns. Like `mutate()` + `select()` combined.

**Scenario:** Build a clean derived feature table for modeling.

---

## Cheat Sheet

| Goal | Tool |
|---|---|
| New column | `mutate(df, new = formula)` |
| If/else binary | `ifelse()` |
| Multiple conditions | `case_when()` |
| Apply to many columns | `across()` |
| Rank rows | `min_rank()`, `dense_rank()` |
| Buckets | `ntile()` |
| Running total | `cumsum()`, `cummean()` |
| Row before/after | `lag()`, `lead()` |
| Keep only new cols | `transmute()` |

---

## Real Pipeline Example

```r
mtcars |>
  mutate(
    kpl = mpg * 0.425,
    size = case_when(cyl == 4 ~ "small", cyl == 6 ~ "medium", cyl == 8 ~ "large"),
    efficiency_rank = min_rank(desc(mpg)),
    mpg_quartile = ntile(mpg, 4)
  ) |>
  select(kpl, size, efficiency_rank, mpg_quartile, everything()) |>
  arrange(efficiency_rank)
```

---

## Common Mistakes

```r
mutate(mtcars, mpg > 20)               # WRONG — missing column name
mutate(mtcars, flag = mpg > 20 = "hi") # WRONG — can't chain like this
# case_when without .default — unmatched rows become NA silently
```

---

## Next Steps
- `summarise()` — collapse rows instead of adding columns
- `group_by() + mutate()` — mutate **within** groups (e.g. rank within each cyl group)
- `rowwise()` — apply mutate row by row when working with lists or complex functions

## Two Common Mistakes

---

**1. Chaining conditions wrong**

```r
# WRONG
mutate(mtcars, flag = mpg > 20 = "hi")

# RIGHT — use if_else
mutate(mtcars, flag = if_else(mpg > 20, "hi", "lo"))

# RIGHT — use case_when for multiple conditions
mutate(mtcars, flag = case_when(
  mpg > 25 ~ "hi",
  mpg > 20 ~ "mid",
  .default = "lo"
))
```

*`=` is assignment, not a conditional — you need `~` inside `case_when` or `if_else(condition, true, false)`*

---

**2. Silent NAs from missing `.default`**

```r
# DANGEROUS — cyl == 5 would silently become NA
mutate(mtcars, size = case_when(
  cyl == 4 ~ "small",
  cyl == 8 ~ "large"
))

# SAFE
mutate(mtcars, size = case_when(
  cyl == 4 ~ "small",
  cyl == 8 ~ "large",
  .default = "other"   # catches everything unmatched
))
```

*Scenario: you join new data with unexpected `cyl` values — without `.default` you get NAs with no warning, silently breaking downstream analysis.*

---

**Quick check after any `case_when`:**
```r
df |> count(size)   # spot unexpected NAs immediately
```

