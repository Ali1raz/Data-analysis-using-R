# Module 01 — R Basics & Data Structures

---

## 1.1 RStudio Interface

Four panes to know:

| Pane | Purpose |
|------|---------|
| **Console** | Run code immediately, see output |
| **Script Editor** | Write and save reusable `.R` files |
| **Environment** | See all variables currently in memory |
| **Files / Plots / Help** | Browse files, view plots, read docs |

Run a line in the script editor with `Ctrl+Enter` (Windows) or `Cmd+Enter` (Mac).

---

## 1.2 Variables & Assignment

```r
x <- 10
name <- "iris"
is_valid <- TRUE

x         # prints 10
```

- `<-` is the standard assignment operator in R
- `=` works too but is reserved by convention for function arguments
- Variable names are case-sensitive — `Iris` and `iris` are different

---

## 1.3 Data Types

```r
# Numeric
x <- 3.14

# Integer
n <- 5L

# Character (string)
s <- "setosa"

# Logical
flag <- TRUE

# Factor (categorical — very important in R)
species <- factor(c("setosa", "versicolor", "virginica"))

class(x)        # "numeric"
class(species)  # "factor"
levels(species) # "setosa" "versicolor" "virginica"
```

Factors matter because R treats them differently in models and plots — a column that looks like text may need to be explicitly converted.

---

## 1.4 Vectors

A vector holds multiple values of the **same type**.

```r
lengths <- c(5.1, 4.9, 4.7, 4.6, 5.0)
names_v <- c("a", "b", "c")

# Arithmetic applies element-wise
lengths * 2
lengths - mean(lengths)

# Indexing starts at 1
lengths[1]        # 5.1
lengths[2:4]      # 4.9 4.7 4.6
lengths[lengths > 4.8]  # values greater than 4.8
```

---

## 1.5 Matrices

A matrix is a 2D vector — all values must be the same type.

```r
m <- matrix(1:9, nrow = 3, ncol = 3)
m

m[1, ]    # first row
m[, 2]    # second column
m[2, 3]   # row 2, col 3
```

---

## 1.6 Lists

A list holds values of **mixed types** — including other lists.

```r
record <- list(
  name    = "setosa",
  length  = 5.1,
  numeric = TRUE
)

record$name       # "setosa"
record[["length"]] # 5.1
```

---

## 1.7 Data Frames

A data frame is the most important structure in R for data analysis — a table where each column can be a different type.

```r
df <- data.frame(
  species = c("setosa", "versicolor"),
  length  = c(5.1, 6.3),
  width   = c(3.5, 2.8)
)

df$length          # column by name
df[1, ]            # first row
df[, "species"]    # column by string name
df[df$length > 5.5, ]  # filtered rows
```

---

## 1.8 Loading & Exploring `iris`

```r
data(iris)      # loads the built-in dataset into memory
```

### First look

```r
head(iris)      # first 6 rows
tail(iris)      # last 6 rows
dim(iris)       # 150 rows, 5 columns
nrow(iris)      # 150
ncol(iris)      # 5
names(iris)     # column names
```

### Structure & types

```r
str(iris)
# 'data.frame': 150 obs. of 5 variables:
#  $ Sepal.Length: num  5.1 4.9 4.7 ...
#  $ Sepal.Width : num  3.5 3.0 3.2 ...
#  $ Petal.Length: num  1.4 1.4 1.3 ...
#  $ Petal.Width : num  0.2 0.2 0.2 ...
#  $ Species     : Factor w/ 3 levels "setosa","versicolor","virginica"
```

### Summary statistics

```r
summary(iris)
#  Sepal.Length    Sepal.Width     Petal.Length    Petal.Width   
#  Min.   :4.300   Min.   :2.000   Min.   :1.000   Min.   :0.100  
#  Mean   :5.843   Mean   :3.057   Mean   :3.758   Mean   :1.199  
#  Max.   :7.900   Max.   :4.400   Max.   :6.900   Max.   :2.500  
```

---

## 1.9 Subsetting `iris`

```r
# Single column as vector
iris$Sepal.Length

# Single column as data frame
iris[, "Sepal.Length", drop = FALSE]

# Specific rows and columns
iris[1:10, c("Sepal.Length", "Species")]

# Filter by condition
setosa <- iris[iris$Species == "setosa", ]
nrow(setosa)  # 50

# Multiple conditions
iris[iris$Sepal.Length > 6 & iris$Species == "virginica", ]
```

---

## 1.10 Basic Statistics on `iris`

```r
mean(iris$Sepal.Length)     # 5.843
median(iris$Sepal.Length)   # 5.800
sd(iris$Sepal.Length)       # 0.828
var(iris$Sepal.Length)      # 0.686
range(iris$Sepal.Length)    # 4.3 7.9
quantile(iris$Sepal.Length) # 0% 25% 50% 75% 100%

# Per-species mean using tapply
tapply(iris$Petal.Length, iris$Species, mean)
# setosa     versicolor  virginica
# 1.462      4.260       5.552
```

---

## 1.11 Exercises

**1.** Load `mtcars`. How many rows and columns does it have? What are the column names?

**2.** What is the mean and standard deviation of `mpg` in `mtcars`?

**3.** Subset `mtcars` to only cars with more than 6 cylinders (`cyl > 6`). How many rows remain?

**4.** In `iris`, find all rows where `Petal.Width > 2.0`. Which species do they belong to?

**5.** Create a data frame by hand with 3 columns — `name`, `score`, `passed` — and 4 rows of your own values.

---

## Summary

| Concept | Key function / syntax |
|---------|-----------------------|
| Load dataset | `data(iris)` |
| Inspect | `str()`, `summary()`, `head()`, `dim()` |
| Column access | `df$col`, `df[, "col"]` |
| Row filter | `df[df$col == value, ]` |
| Basic stats | `mean()`, `sd()`, `median()`, `range()` |
| Grouped stats | `tapply(x, group, FUN)` |
