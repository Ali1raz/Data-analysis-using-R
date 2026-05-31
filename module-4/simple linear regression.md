# Simple Linear Regression

(short description, for more ask AI)

## What is it?
A way to predict one thing using one other thing, by drawing the **best straight line** through data points.

---

## The Formula: y = mx + b

| Symbol | Name | Meaning |
|--------|------|---------|
| `y` | dependent variable | what you're predicting |
| `x` | independent variable | what you're using to predict |
| `m` | slope | how much y changes per 1 unit of x |
| `b` | intercept | value of y when x = 0 |

---

## Simple Example

**Scenario:** Predict salary from years of experience.

```
salary = 5000 * years_experience + 30000
```

- 0 years → $30,000 (base)
- 3 years → $45,000
- 10 years → $80,000

---

## What, How, When, Why

**What** — fits a line that minimizes the error between predicted and actual values

**How** — uses *Ordinary Least Squares (OLS)*: minimizes the sum of squared vertical distances from each point to the line

**When** — use it when:
- you have one numeric predictor
- the relationship looks linear (not curved)
- outcome is continuous (not yes/no)

**Why** — simple, interpretable, fast — you can explain *exactly* how x affects y

---

## Use Cases

**House price prediction**
```
price = 200 * sqft + 50000
```
Every extra sqft adds $200.

**Ad spend → Revenue**
```
revenue = 8 * ad_dollars + 1000
```
Each $1 spent on ads returns $8.

**Temperature → Ice cream sales**
```
sales = 3 * temp_celsius + 10
```
Hotter day = more sales.

---

## In R

```r
# Fit the model
model <- lm(salary ~ years_experience, data = df)

# Summary
summary(model)

# Predict new values
predict(model, newdata = data.frame(years_experience = 5))
```

`lm()` = linear model. Output gives you `m` (coefficient) and `b` (intercept) directly.

---

## Key Output to Read

```
Coefficients:
                   Estimate
(Intercept)         30000       ← this is b
years_experience     5000       ← this is m

R-squared: 0.89               ← 89% of variance explained
p-value: < 0.001              ← relationship is statistically real
```

---

## Assumptions (must hold)

1. **Linearity** — relationship is actually a straight line
2. **Independence** — observations don't affect each other
3. **Homoscedasticity** — error spread is constant across x
4. **Normality of errors** — residuals are roughly normal

---

## Next Steps to Learn

1. **Residual analysis** — check if your assumptions hold (`plot(model)`)
2. **Multiple linear regression** — add more than one predictor
3. **R-squared vs adjusted R-squared** — how well does the model fit?
4. **Overfitting** — when the model memorizes noise
5. **Train/test split** — test if your model generalizes
