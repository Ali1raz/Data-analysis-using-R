## `select()` — Full Guide using `mtcars`

---

## Mental Model
> **"select = columns"** — you're choosing which columns to keep, drop, or rename.

```r
library(dplyr)
```

---

## 1. Basic — pick columns by name

```r
select(mtcars, mpg, cyl, hp)
```
**Remember:** just list what you want, comma-separated.

---

## 2. Drop columns with `-`

```r
select(mtcars, -carb)
select(mtcars, -c(carb, gear, qsec))
```
**Scenario:** You have 11 columns, only need to remove noise columns before modeling.

---

## 3. Select a range with `:`

```r
select(mtcars, mpg:hp)   # all columns from mpg to hp
```
**Remember:** works like a slice — left to right as they appear in the df.

---

## 4. Helper functions — `starts_with`, `ends_with`, `contains`

```r
select(mtcars, starts_with("c"))    # cyl, carb
select(mtcars, contains("a"))       # gear, carb, drat
```
**Scenario:** Survey with 80 columns named `q1_score`, `q2_score`... — use `starts_with("q")`.

---

## 5. Rename while selecting — `new = old`

```r
select(mtcars, cylinders = cyl, mileage = mpg)
```
**Scenario:** Presenting data to a non-technical audience — rename to readable names.

---

## 6. Reorder columns

```r
select(mtcars, hp, mpg, cyl, everything())
```
`everything()` brings the rest — useful to **push important columns to the front**.

**Scenario:** You want `hp` and `mpg` visible first when printing.

---

## 7. Select by column type — `where()`

```r
select(mtcars, where(is.numeric))
select(mtcars, where(is.character))  # in other datasets
```
**Scenario:** Before running a correlation — keep only numeric columns automatically.

---

## 8. Select by position — `last_col()`, index numbers

```r
select(mtcars, last_col())          # last column only
select(mtcars, last_col(offset = 1)) # second to last
select(mtcars, 1, 3, 5)             # by index
```

---

## 9. Combine helpers

```r
select(mtcars, starts_with("c") | contains("p"))
select(mtcars, !starts_with("c"))   # NOT starts with c
```

---

## 10. Advanced — `any_of()` and `all_of()`

```r
cols <- c("mpg", "hp", "fake_col")

select(mtcars, any_of(cols))   # skips fake_col silently
select(mtcars, all_of(cols))   # errors if fake_col missing
```
**Scenario:** You're writing a reusable function — `any_of()` is safe when columns might not exist. `all_of()` is strict, use when columns **must** be there.

---

## Cheat Sheet to Remember

| Goal | Syntax |
|---|---|
| Keep columns | `select(df, a, b)` |
| Drop columns | `select(df, -a)` |
| Range | `select(df, a:d)` |
| Name pattern | `starts_with / ends_with / contains` |
| Rename | `select(df, new = old)` |
| Reorder | `everything()` |
| By type | `where(is.numeric)` |
| Safe list | `any_of(cols)` |
| Strict list | `all_of(cols)` |

---

## Real Pipeline Example

```r
mtcars |>
  select(mileage = mpg, cylinders = cyl, horsepower = hp) |>
  filter(cylinders == 4) |>
  arrange(desc(mileage))
```

---

## Next Steps
- `rename()` — rename without dropping other columns
- `relocate()` — reorder columns without selecting
- `pull()` — extract one column as a vector (not a df)
