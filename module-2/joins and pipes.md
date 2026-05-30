## Joins + Pipe Operators — Full Guide using `mtcars`

---

## Mental Model
> **join = combine two tables by a shared column**
> **pipe = pass result of one step into the next**

---

# PIPE OPERATORS

## 1. Base R pipe `|>` vs magrittr `%>%`

```r
# base R (R 4.1+) — no package needed
mtcars |> filter(cyl == 4) |> select(mpg, hp)

# magrittr — needs dplyr/tidyverse
mtcars %>% filter(cyl == 4) %>% select(mpg, hp)
```

**Remember:** they do the same thing 95% of the time. Use `|>` for new code.

---

## 2. What pipe actually does

```r
# these are identical
filter(mtcars, cyl == 4)
mtcars |> filter(cyl == 4)

# x |> f() means f(x)
# x |> f() |> g() means g(f(x))
```

---

## 3. Pipe with placeholder `_` (R 4.2+)

```r
mtcars |> lm(mpg ~ hp, data = _)
```
Use `_` when the data doesn't go in the first argument.

**Scenario:** Functions like `lm()`, `t.test()` take `data=` not first arg.

---

## 4. Magrittr extras — `%T>%` tee pipe

```r
library(magrittr)

mtcars %>%
  filter(cyl == 4) %T>%
  print() %>%            # prints but passes original along
  summarise(avg = mean(mpg))
```
**Scenario:** Debug mid-pipeline without breaking the chain.

---

## 5. Magrittr `%$%` exposition pipe

```r
mtcars %$% cor(mpg, hp)    # exposes columns as variables
```
Equivalent to `with(mtcars, cor(mpg, hp))`.

---

# JOINS

## Setup — create two tables from mtcars

```r
library(dplyr)

# table 1 — performance data
perf <- mtcars |>
  mutate(car = rownames(mtcars)) |>
  select(car, mpg, hp, cyl)

# table 2 — weight and transmission data
specs <- mtcars |>
  mutate(car = rownames(mtcars)) |>
  select(car, wt, am, gear)
```

---

## 6. `left_join()` — keep all rows from LEFT table

```r
left_join(perf, specs, by = "car")
```
All rows from `perf` stay. Matching rows from `specs` added. Non-matches get `NA`.

**Scenario:** You have a customer list — left table. Add purchase data where available. Customers with no purchases stay in, purchase cols become NA.

---

## 7. `right_join()` — keep all rows from RIGHT table

```r
right_join(perf, specs, by = "car")
```
Mirror of left_join. All rows from `specs` stay.

**Scenario:** Rarely used — just flip the tables and use `left_join` instead. Easier to reason about.

---

## 8. `inner_join()` — keep only MATCHING rows

```r
inner_join(perf, specs, by = "car")
```
Only rows that exist in **both** tables. Non-matches dropped entirely.

**Scenario:** Only analyze customers who have both a profile AND a purchase record.

---

## 9. `full_join()` — keep ALL rows from BOTH tables

```r
full_join(perf, specs, by = "car")
```
Everything kept. NAs where no match.

**Scenario:** Merge two datasets that partially overlap — don't want to lose anything.

---

## 10. `semi_join()` — filter LEFT by RIGHT, no new columns

```r
semi_join(perf, specs, by = "car")
```
Keeps rows from `perf` that have a match in `specs`. **Does not add specs columns.**

**Scenario:** Filter a product list to only items that appear in this month's orders — don't need order details, just the filter.

---

## 11. `anti_join()` — keep rows with NO match

```r
anti_join(perf, specs, by = "car")
```
Opposite of semi_join — keeps rows from `perf` that have **no match** in `specs`.

**Scenario:** Find customers who have NOT made a purchase. Find products never ordered. Identify missing data.

---

## 12. Join on different column names

```r
# if key columns have different names
left_join(perf, specs, by = c("car" = "vehicle"))
```

---

## 13. Join on multiple columns

```r
left_join(df1, df2, by = c("cyl", "am"))
```
**Scenario:** Match on both region AND year — single column match would be ambiguous.

---

## 14. Handling duplicate column names

```r
# if both tables have an "hp" column
left_join(perf, specs, by = "car", suffix = c("_perf", "_specs"))
# creates hp_perf and hp_specs
```

---

# Join Types Visual

```
LEFT JOIN        INNER JOIN       FULL JOIN        ANTI JOIN
perf  specs      perf  specs      perf  specs      perf  specs
[■■■] [   ]      [■■■] [   ]      [■■■] [■■■]      [■  ] [   ]
[■■■] [■■■]  →  [■■■] [■■■]  →  [■■■] [■■■]  →        
[■■■] [   ]      drop  drop       [■■■] [   ]      
```

---

# Full Pipeline with Joins + Pipes

## 15. Realistic example

```r
perf |>
  inner_join(specs, by = "car") |>
  filter(cyl %in% c(4, 6)) |>
  mutate(
    kpl          = mpg * 0.425,
    transmission = ifelse(am == 1, "manual", "auto")
  ) |>
  group_by(cyl, transmission) |>
  summarise(
    count   = n(),
    avg_kpl = mean(kpl),
    avg_hp  = mean(hp),
    .groups = "drop"
  ) |>
  arrange(desc(avg_kpl))
```
**Scenario:** Combine performance and specs, analyze fuel efficiency by engine + transmission type.

---

## 16. Anti-join to find missing data

```r
# which cars are in perf but missing from specs?
anti_join(perf, specs, by = "car")

# fix — full_join then flag NAs
full_join(perf, specs, by = "car") |>
  mutate(missing_specs = is.na(wt))
```

---

## Join Cheat Sheet

| Join | Keeps |
|---|---|
| `left_join` | all left, matching right |
| `right_join` | matching left, all right |
| `inner_join` | only matches |
| `full_join` | everything |
| `semi_join` | left rows that match (no new cols) |
| `anti_join` | left rows with NO match |

## Pipe Cheat Sheet

| Pipe | Use |
|---|---|
| `\|>` | standard, base R |
| `%>%` | magrittr, same thing |
| `_` placeholder | when data isn't first arg |
| `%T>%` | tee — print mid-pipeline |
| `%$%` | expose columns as variables |

---

## Common Mistakes

```r
# joining without specifying by= — dangerous silent join on all shared cols
left_join(perf, specs)           # risky
left_join(perf, specs, by = "car")  # explicit, always do this

# many-to-many joins — duplicate rows silently multiply
# always check nrow() before and after a join

# forgetting to ungroup() after grouped pipeline before joining
```

---

## Next Steps
- `tidyr::pivot_longer()` / `pivot_wider()` — reshape before/after joins
- `bind_rows()` / `bind_cols()` — stack tables instead of joining
- `fuzzyjoin` package — join on approximate matches, ranges, regex
