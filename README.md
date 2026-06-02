# Data Analysis Using R

A comprehensive, hands-on guide to data analysis with R — from the basics of R programming and data wrangling through visualization, regression, classification, and neural networks. Each module builds on the previous one with real-world examples, practical code snippets, and built-in datasets (`iris`, `mtcars`, `economics`).

[![Ask DeepWiki about this](https://deepwiki.com/badge.svg)](https://deepwiki.com/Ali1raz/Data-analysis-using-R)


---

## Table of Contents

### Module 01 — R Basics & Data Structures

| Topic | Link |
|-------|------|
| R Basics & Data Structures | [module-1/basics.md](module-1/basics.md) |

### Module 02 — Data Wrangling with dplyr

| Topic | Link |
|-------|------|
| Module Overview | [module-2/READMD.md](module-2/READMD.md) |
| `filter()` — Row Selection | [module-02/filter.md](module-02/filter.md) |
| `select()` — Column Selection | [module-2/select.md](module-2/select.md) |
| `mutate()` — Creating New Columns | [module-2/mutate.md](module-2/mutate.md) |
| `arrange()` & `group_by()` | [module-2/arrange-groupby.md](module-2/arrange-groupby.md) |
| `summarise()` & Aggregation | [module-2/summarize & aggregate.md](module-2/summarize%20%26%20aggregate.md) |
| Joins & Pipe Operators | [module-2/joins and pipes.md](module-2/joins%20and%20pipes.md) |
| Handling NAs | [module-2/handling NAs.md](module-2/handling%20NAs.md) |

### Module 03 — Visualization with ggplot2

| Topic | Link |
|-------|------|
| Module Overview | [module-3/README.md](module-3/README.md) |
| Visualization Guide (mtcars) | [module-3/visualization.md](module-3/visualization.md) |
| ggplot2 — How to Remember | [module-3/how-to-remember.md](module-3/how-to-remember.md) |

### Module 04 — Regression

| Topic | Link |
|-------|------|
| Regression Concepts Overview | [module-4/README.md](https://github.com/Ali1raz/Data-analysis-using-R/blob/Ali1raz-patch-4.1/module-4/README.md) |
| Simple Linear Regression | [module-4/simple linear regression.md](https://github.com/Ali1raz/Data-analysis-using-R/blob/Ali1raz-patch-4.2/module-4/simple%20linear%20regression.md) |
| Multiple Linear Regression | [module-4/multiple linear regression.md](https://github.com/Ali1raz/Data-analysis-using-R/blob/Ali1raz-patch-4.3/module-4/multiple%20linear%20regression.md) |
| Logistic Regression | [module-4/logistic regression.md](https://github.com/Ali1raz/Data-analysis-using-R/blob/Ali1raz-patch-4.3-1/module-4/logistic%20regression.md) |

### Charts (ggplot2 Reference)

| Topic | Link |
|-------|------|
| Bar Charts | [chart/bar-chart.md](https://github.com/Ali1raz/Data-analysis-using-R/blob/Ali1raz-patch-5.1/chart/bar-chart.md) |
| Pie Charts | [chart/pie-chart.md](https://github.com/Ali1raz/Data-analysis-using-R/blob/Ali1raz-patch-5.2/chart/pie-chart.md) |
| Histograms & Line Charts | [chart/histogram.md](https://github.com/Ali1raz/Data-analysis-using-R/blob/Ali1raz-patch-5.3/chart/histogram.md) |

### Assignments

| Assignment | Link |
|------------|------|
| Assignment 1 & 2 — Data Analysis | [Ali1raz/data-analysis-assignment-1-2](https://github.com/Ali1raz/data-analysis-assignment-1-2) |
| Assignment 3 — Regression Analysis | [assignments/a3.md](https://github.com/Ali1raz/Data-analysis-using-R/blob/Ali1raz-patch-7.1/assignments/a3.md) |
| Assignment 4 — Classification | [assignments/a4.md](https://github.com/Ali1raz/Data-analysis-using-R/blob/Ali1raz-patch-7.1/assignments/a4.md) |

---

## Lesson Plan

### Module 01 — R Basics & Data Structures

**Datasets:** `iris`, `mtcars`
**Packages:** base R

**Topics:**
1. RStudio interface — console, script editor, environment pane
2. Variables and assignment (`<-`)
3. Data types — numeric, character, logical, factor
4. Vectors, lists, matrices, data frames
5. Loading built-in datasets — `data(iris)`
6. Exploring data — `head()`, `tail()`, `str()`, `summary()`, `dim()`
7. Subsetting — `df[row, col]`, `df$column`, `df[df$col == value, ]`
8. Basic arithmetic and built-in functions — `mean()`, `sd()`, `sum()`, `length()`

```r
data(iris)
head(iris)
str(iris)
summary(iris)
iris[iris$Species == "setosa", ]
mean(iris$Sepal.Length)
```

---

### Module 02 — Data Wrangling with dplyr

**Datasets:** `iris`, `mtcars`
**Packages:** `tidyverse`

**Topics:**
1. The pipe operator `|>` and why it exists
2. `filter()` — row selection by condition
3. `select()` — column selection
4. `mutate()` — creating new columns
5. `arrange()` — sorting rows
6. `group_by()` + `summarise()` — grouped aggregations
7. `rename()`, `distinct()`, `count()`
8. Handling missing values — `is.na()`, `na.omit()`, `drop_na()`

```r
library(tidyverse)

iris |>
  filter(Species != "virginica") |>
  group_by(Species) |>
  summarise(
    mean_sepal = mean(Sepal.Length),
    sd_sepal   = sd(Sepal.Length)
  )
```

---

### Module 03 — Visualization with ggplot2

**Datasets:** `iris`, `mtcars`
**Packages:** `ggplot2`

**Topics:**
1. The grammar of graphics — `ggplot()`, `aes()`, `geom_*()`
2. Scatter plots — `geom_point()`
3. Bar charts — `geom_bar()`, `geom_col()`
4. Histograms & density plots — `geom_histogram()`, `geom_density()`
5. Box plots & violin plots — `geom_boxplot()`, `geom_violin()`
6. Line charts — `geom_line()`, `geom_smooth()`
7. Faceting — `facet_wrap()`, `facet_grid()`
8. Themes, labels, color scales — `theme_minimal()`, `labs()`, `scale_color_manual()`

```r
library(ggplot2)

ggplot(iris, aes(x = Sepal.Length, y = Petal.Length, color = Species)) +
  geom_point(size = 2, alpha = 0.7) +
  geom_smooth(method = "lm", se = FALSE) +
  facet_wrap(~Species) +
  theme_minimal() +
  labs(title = "Sepal vs Petal Length by Species")
```

---

### Module 04 — Linear & Multiple Regression

**Datasets:** `iris`, `mtcars`
**Packages:** base R, `ggplot2`

**Topics:**
1. What regression does — fitting a line to minimize residuals
2. Simple linear regression — `lm(y ~ x)`
3. Reading `summary()` output — coefficients, R², p-values, F-statistic
4. Residual diagnostics — `plot(model)`
5. Multiple regression — `lm(y ~ x1 + x2 + x3)`
6. Categorical predictors — factors as dummy variables
7. Interaction terms — `lm(y ~ x1 * x2)`
8. Train/test split — evaluating RMSE on held-out data

```r
model <- lm(Petal.Length ~ Sepal.Length + Species, data = iris)
summary(model)
par(mfrow = c(2, 2)); plot(model)

# RMSE on test set
set.seed(42)
idx   <- sample(1:nrow(iris), 0.8 * nrow(iris))
train <- iris[idx, ];  test <- iris[-idx, ]
preds <- predict(model, newdata = test)
sqrt(mean((test$Petal.Length - preds)^2))
```

---

### Module 05 — Decision Trees & Classification

**Datasets:** `iris`
**Packages:** `rpart`, `rpart.plot`, `caret`

**Topics:**
1. Classification vs regression trees
2. How trees split — Gini impurity, information gain
3. Building a tree — `rpart()`
4. Visualizing the tree — `rpart.plot()`
5. Pruning — `cp` parameter, `plotcp()`
6. Overfitting and the bias-variance tradeoff
7. Predictions — `predict(model, type = "class")`
8. Evaluating — confusion matrix, accuracy, precision, recall

```r
library(rpart); library(rpart.plot); library(caret)

tree <- rpart(Species ~ ., data = iris, method = "class", cp = 0.01)
rpart.plot(tree, type = 4, extra = 102)

preds <- predict(tree, iris, type = "class")
confusionMatrix(preds, iris$Species)
```

---

### Module 06 — Random Forests & Ensembles

**Datasets:** `iris`, `mtcars`
**Packages:** `randomForest`, `caret`

**Topics:**
1. Why single trees fail — high variance, sensitivity to data
2. Bagging — bootstrap aggregation intuition
3. Random forests — random feature subsets at each split
4. Building a forest — `randomForest()`
5. `ntree` and `mtry` hyperparameters
6. Variable importance — `varImpPlot()`, `importance()`
7. Out-of-bag (OOB) error
8. Cross-validation with `caret` — `trainControl()`, `train()`

```r
library(randomForest); library(caret)

rf <- randomForest(Species ~ ., data = iris, ntree = 500, importance = TRUE)
print(rf)
varImpPlot(rf)

# Cross-validated version via caret
ctrl  <- trainControl(method = "cv", number = 10)
rf_cv <- train(Species ~ ., data = iris, method = "rf", trControl = ctrl)
rf_cv$results
```

---

### Module 07 — Neural Networks

**Datasets:** `iris`
**Packages:** `neuralnet`, `keras` (intro)

**Topics:**
1. Perceptrons — weights, bias, activation
2. Feedforward networks — input, hidden, output layers
3. Backpropagation — how the network learns
4. Why normalization matters before training
5. Building a network — `neuralnet()`
6. Choosing hidden layer size and depth
7. Activation functions — sigmoid, ReLU, softmax
8. Evaluating — loss curves, confusion matrix, intro to `keras`/`torch`

```r
library(neuralnet)

# Normalize to [0, 1]
normalize  <- function(x) (x - min(x)) / (max(x) - min(x))
iris_n     <- as.data.frame(lapply(iris[, 1:4], normalize))

# One-hot encode species
iris_n$setosa     <- as.integer(iris$Species == "setosa")
iris_n$versicolor <- as.integer(iris$Species == "versicolor")
iris_n$virginica  <- as.integer(iris$Species == "virginica")

nn <- neuralnet(
  setosa + versicolor + virginica ~ Sepal.Length + Sepal.Width + Petal.Length + Petal.Width,
  data          = iris_n,
  hidden        = c(5, 3),
  linear.output = FALSE
)
plot(nn)
```

---

## Suggested Packages to Install

```r
install.packages(c(
  "tidyverse",
  "ggplot2",
  "rpart",
  "rpart.plot",
  "randomForest",
  "caret",
  "neuralnet"
))
```

---

## Progression Order

| Module | Concept | Difficulty |
|--------|---------|------------|
| 01 | R basics & data structures | Beginner |
| 02 | Data wrangling (dplyr) | Beginner |
| 03 | Visualization (ggplot2) | Beginner |
| 04 | Linear & multiple regression | Intermediate |
| 05 | Decision trees | Intermediate |
| 06 | Random forests | Intermediate |
| 07 | Neural networks | Advanced |
