## `filter()` — Full Guide using `mtcars`

---

## Mental Model
> **"filter = rows"** — you're keeping rows that meet a condition, dropping the rest.

---

## 1. Basic — single condition

```r
filter(mtcars, cyl == 6)
filter(mtcars, mpg > 20)
```
**Remember:** `==` for equality, not `=`. Single `=` will error.

---

## 2. AND conditions — `,` or `&`

```r
filter(mtcars, cyl == 6, hp > 110)
filter(mtcars, cyl == 6 & hp > 110)   # same result
```
**Scenario:** Find mid-range cars — good mileage AND moderate horsepower.

---

## 3. OR conditions — `|`

```r
filter(mtcars, cyl == 4 | cyl == 8)
```
**Scenario:** Keep only economy cars (4 cyl) or muscle cars (8 cyl), exclude 6.

---

## 4. `%in%` — match multiple values

```r
filter(mtcars, cyl %in% c(4, 6))
```
**Remember:** cleaner than writing multiple `|` conditions. Think "is cyl IN this list?"

**Scenario:** Filter specific product categories, regions, user types.

---

## 5. NOT — `!` and `!=`

```r
filter(mtcars, cyl != 6)
filter(mtcars, !cyl %in% c(4, 6))    # exclude 4 and 6
```
**Scenario:** Remove outlier group before analysis.

---

## 6. Range — `between()`

```r
filter(mtcars, between(mpg, 15, 25))
```
Equivalent to `mpg >= 15 & mpg <= 25` but cleaner.

**Scenario:** Keep only cars with typical mpg, exclude extremes.

---

## 7. Filter on computed value

```r
filter(mtcars, hp / wt > 40)         # power-to-weight ratio
filter(mtcars, mpg > mean(mpg))       # above average mpg
```
**Scenario:** Flag high-performing cars without creating a new column first.

---

## 8. Filter NAs — `is.na()` and `!is.na()`

```r
filter(mtcars, !is.na(mpg))          # drop rows where mpg is NA
filter(mtcars, is.na(hp))            # keep only rows with missing hp
```
**Scenario:** Clean dataset before modeling — missing values break most functions.

---

## 9. Filter with string matching — `grepl()`

```r
# mtcars rownames aren't a column, so first:
mtcars$car <- rownames(mtcars)

filter(mtcars, grepl("Merc", car))    # all Mercedes
filter(mtcars, grepl("^M", car))      # starts with M
```
**Scenario:** Filter product names, customer names, categories by partial match.

---

## 10. `filter` + `%in%` with external list

```r
target_cars <- c("Datsun 710", "Honda Civic", "Fiat 128")
mtcars$car <- rownames(mtcars)

filter(mtcars, car %in% target_cars)
```
**Scenario:** You have a lookup list from another table — filter only matching rows.

---

## 11. Advanced — `if_any()` and `if_all()`

```r
# rows where ANY of these columns > 100
filter(mtcars, if_any(c(hp, disp), ~ . > 100))

# rows where ALL of these columns > 100
filter(mtcars, if_all(c(hp, disp), ~ . > 100))
```
**Scenario:** Flag a record if **any** risk score exceeds threshold vs **all** must exceed it.

---

## Comparison Operators Cheat Sheet

| Operator | Meaning |
|---|---|
| `==` | equal |
| `!=` | not equal |
| `>` `<` | greater / less |
| `>=` `<=` | greater or equal / less or equal |
| `%in%` | matches any in list |
| `!` | negate |
| `is.na()` | is missing |
| `between()` | inclusive range |
| `grepl()` | string pattern match |

---

## Real Pipeline Example

```r
mtcars |>
  filter(cyl %in% c(4, 6), mpg > mean(mpg), !is.na(hp)) |>
  select(mpg, cyl, hp) |>
  arrange(desc(mpg))
```
**Scenario:** Shortlist fuel-efficient, mid-size cars with complete data for a report.

---

## Common Mistakes

```r
filter(mtcars, cyl = 6)     # WRONG — use ==
filter(mtcars, cyl == "6")  # WRONG — cyl is numeric, no quotes
filter(mtcars, mpg = NA)    # WRONG — use is.na(mpg)
```

---

## Next Steps
- `slice()` — filter rows by position, not condition
- `distinct()` — remove duplicate rows
- `case_when()` — if/else logic to label rows instead of drop them


## `mtcars` first — know your data

```r
head(mtcars)
```
Key columns: `mpg`, `cyl`, `hp`, `wt`, `gear`

---

## `filter` — keep certain ROWS

**Scenario: You only want powerful cars**
```r
filter(mtcars, hp > 150)
```

**Scenario: Powerful AND fuel efficient**
```r
filter(mtcars, hp > 150, mpg > 20)
```

---

## `select` — keep certain COLUMNS

**Scenario: You only need 3 columns for your report**
```r
select(mtcars, mpg, hp, wt)
```

**Scenario: Remove columns you don't need**
```r
select(mtcars, -gear, -carb)
```

---

## `mutate` — create a NEW column

**Scenario: Convert weight from lbs to kg**
```r
mutate(mtcars, wt_kg = wt * 453.6)
```

**Scenario: Flag cars as "fast" or "slow"**
```r
mutate(mtcars, speed_label = ifelse(hp > 150, "fast", "slow"))
```

---

## All 3 together (real workflow)

```r
mtcars %>%
  filter(cyl == 6) %>%            # keep only 6-cylinder cars
  select(mpg, hp, wt) %>%         # keep only 3 columns
  mutate(wt_kg = wt * 453.6)      # add weight in kg
```

*Scenario: Analyze only 6-cylinder cars, with clean columns, adding metric weight.*

---

### Next steps
- `summarise` — calculate stats on your filtered data
- `arrange` — sort rows by a column
- `group_by` + `mutate` — create columns based on groups
