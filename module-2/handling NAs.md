## Handling NAs — Full Guide using `mtcars`

---

## Mental Model
> **NA = unknown value** — not zero, not empty string, unknown.
> NA is contagious — any calculation touching NA returns NA unless handled.

---

## Setup — inject NAs into mtcars

```r
df <- mtcars
df$mpg[c(1,5,10)] <- NA
df$hp[c(3,7)]     <- NA
```

---

# DETECTING NAs

## 1. Check if a value is NA — `is.na()`

```r
is.na(df$mpg)              # logical vector
sum(is.na(df$mpg))         # count NAs in one column
sum(is.na(df))             # total NAs in whole df
```

---

## 2. Count NAs per column

```r
colSums(is.na(df))
```
**Scenario:** First thing you do on a new dataset — find where the gaps are.

---

## 3. Percentage NA per column

```r
colMeans(is.na(df)) * 100
```
**Scenario:** Decide whether to drop or impute — 2% missing vs 60% missing need different strategies.

---

## 4. Which rows have any NA

```r
df[!complete.cases(df), ]     # rows with at least one NA
df[complete.cases(df), ]      # rows with no NAs at all
```

---

## 5. `anyNA()` — quick check

```r
anyNA(df$mpg)       # TRUE if any NA exists
anyNA(df)           # TRUE if any NA anywhere in df
```

---

# FILTERING NAs

## 6. Filter out NAs — `filter` + `is.na`

```r
library(dplyr)

df |> filter(!is.na(mpg))            # drop rows where mpg is NA
df |> filter(!is.na(mpg), !is.na(hp)) # drop if either is NA
```

---

## 7. `drop_na()` — tidyr shortcut

```r
library(tidyr)

drop_na(df)              # drop rows with ANY NA
drop_na(df, mpg)         # drop rows where mpg is NA
drop_na(df, mpg, hp)     # drop rows where mpg OR hp is NA
```
**Scenario:** Clean dataset before modeling — models break on NAs.

---

## 8. Keep ONLY rows with NA — find missing data

```r
df |> filter(is.na(mpg))
```
**Scenario:** Audit which records need data entry, find gaps in sensor readings.

---

# HANDLING NAs IN CALCULATIONS

## 9. NA is contagious

```r
mean(df$mpg)              # returns NA
mean(df$mpg, na.rm = TRUE) # ignores NAs, returns number
sum(df$mpg,  na.rm = TRUE)
sd(df$mpg,   na.rm = TRUE)
```
**Remember:** almost every base R function has `na.rm = TRUE` — always use it.

---

## 10. NAs in summarise

```r
df |>
  group_by(cyl) |>
  summarise(
    avg_mpg = mean(mpg, na.rm = TRUE),
    avg_hp  = mean(hp,  na.rm = TRUE),
    na_count = sum(is.na(mpg))
  )
```
**Scenario:** Group summary that shows both the average AND how many NAs were excluded.

---

## 11. NAs in `mutate`

```r
df |>
  mutate(
    mpg_flag = is.na(mpg),                          # flag NAs
    mpg_safe = ifelse(is.na(mpg), 0, mpg)           # replace with 0
  )
```

---

# REPLACING / IMPUTING NAs

## 12. `replace_na()` — replace with a fixed value

```r
library(tidyr)

df |> mutate(mpg = replace_na(mpg, 0))
df |> mutate(mpg = replace_na(mpg, mean(mpg, na.rm = TRUE)))
```
**Scenario:** Replace missing numeric with mean, missing category with "unknown".

---

## 13. `coalesce()` — use first non-NA value

```r
x <- c(1, NA, NA, 4)
y <- c(NA, 2, NA, 5)
z <- c(NA, NA, 3, 6)

coalesce(x, y, z)    # returns c(1, 2, 3, 4)
```
**Scenario:** You have three backup columns — use the first non-NA across all three. Merge data sources where any one might have the value.

---

## 14. `ifelse()` for conditional replacement

```r
df |>
  mutate(mpg = ifelse(is.na(mpg), median(mpg, na.rm = TRUE), mpg))
```

---

## 15. `case_when()` for complex imputation

```r
df |>
  mutate(mpg = case_when(
    is.na(mpg) & cyl == 4 ~ mean(mpg[cyl == 4], na.rm = TRUE),
    is.na(mpg) & cyl == 6 ~ mean(mpg[cyl == 6], na.rm = TRUE),
    is.na(mpg) & cyl == 8 ~ mean(mpg[cyl == 8], na.rm = TRUE),
    .default = mpg
  ))
```
**Scenario:** Impute missing mpg using the average of its own cylinder class — smarter than global mean.

---

## 16. `fill()` — carry forward/backward

```r
library(tidyr)

df |> fill(mpg)                    # carry last value down
df |> fill(mpg, .direction = "up") # carry next value up
```
**Scenario:** Time series with sparse readings — fill gaps with last known value.

---

# NA IN JOINS

## 17. NAs from joins

```r
perf |> left_join(specs, by = "car")
# unmatched rows get NA in all specs columns
# check after every join
```

```r
# find rows where join produced NAs
result |> filter(is.na(wt))
```

---

# NA IN LOGICAL OPERATIONS

## 18. NA in conditions — watch out

```r
NA == 5        # returns NA not FALSE
NA > 3         # returns NA
is.na(NA)      # returns TRUE — only safe test
```

```r
# filtering with == misses NAs silently
filter(df, mpg == 21)       # NAs excluded silently
filter(df, mpg == 21 | is.na(mpg))   # keep NAs explicitly
```
**Remember:** `NA == anything` is always `NA`, never `TRUE` or `FALSE`.

---

# Full Pipeline

## 19. Realistic NA handling pipeline

```r
df |>
  mutate(
    mpg_missing = is.na(mpg),
    mpg = case_when(
      is.na(mpg) & cyl == 4 ~ mean(mpg[cyl==4], na.rm=TRUE),
      is.na(mpg) & cyl == 6 ~ mean(mpg[cyl==6], na.rm=TRUE),
      is.na(mpg) & cyl == 8 ~ mean(mpg[cyl==8], na.rm=TRUE),
      .default = mpg
    )
  ) |>
  drop_na(hp) |>
  group_by(cyl) |>
  summarise(
    avg_mpg     = mean(mpg, na.rm = TRUE),
    imputed_cnt = sum(mpg_missing),
    .groups     = "drop"
  )
```
**Scenario:** Impute mpg by group mean, drop rows with missing hp, summarise with imputation audit.

---

## NA Cheat Sheet

| Goal | Tool |
|---|---|
| Detect NAs | `is.na()` |
| Count NAs per col | `colSums(is.na(df))` |
| Complete rows only | `complete.cases()` |
| Drop NA rows | `drop_na()` |
| Ignore in calc | `na.rm = TRUE` |
| Replace with value | `replace_na()` |
| First non-NA | `coalesce()` |
| Carry forward | `fill()` |
| Impute by group | `case_when + group mean` |

---

## Common Mistakes

```r
mean(df$mpg)                  # silently returns NA — add na.rm=TRUE
df$mpg == NA                  # always NA — use is.na()
filter(df, mpg != 21)         # drops NAs silently — intended?
drop_na(df)                   # drops ALL rows with any NA — too aggressive?
```

---

## Next Steps
- `naniar` package — advanced NA visualization and stats
- `mice` package — multiple imputation for serious missing data
- `VIM` package — visualize NA patterns across columns
- `complete()` from tidyr — make implicit NAs explicit in time series


## What It Does

Returns all rows that have **at least one NA** anywhere.

```r
df[!complete.cases(df), ]
```

| Part | Meaning |
|------|---------|
| `complete.cases(df)` | TRUE for rows with no NAs |
| `!` | flip — now TRUE = has NA |
| `df[..., ]` | subset those rows |

---

## Scenario

```r
df <- data.frame(
  name = c("Alice", "Bob", "Carol"),
  age  = c(25, NA, 30),
  sal  = c(50000, 60000, NA)
)

df[!complete.cases(df), ]
#    name age    sal
# 2   Bob  NA  60000
# 3 Carol  30     NA
```

*Use case: audit your data before modelling — missing values break most ML algorithms silently.*

---

## Common Next Steps

```r
# Count bad rows
sum(!complete.cases(df))

# Drop them
df_clean <- df[complete.cases(df), ]

# Tidyverse equivalent
df |> filter(!complete.cases(df))   # spot NAs
df |> drop_na()                      # remove them
```

---

**Rule:** always run this after loading data — know your NAs before they silently corrupt analysis.
