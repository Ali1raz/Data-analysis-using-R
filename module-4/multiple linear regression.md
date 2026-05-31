# Multiple Linear Regression

## What is it?
Same idea as simple, but now you predict y using **more than one x**.

---

## The Formula

```
y = b0 + b1*x1 + b2*x2 + b3*x3 + ...
```

| Symbol | Meaning |
|--------|---------|
| `b0` | intercept (y when all x = 0) |
| `b1, b2...` | slope for each predictor |
| `x1, x2...` | your predictors |

---

## Simple Example

**Scenario:** Predict house price using size, bedrooms, and age.

```
price = 50000 + 200*sqft + 15000*bedrooms - 1000*age
```

- sqft goes up by 1 → price +$200
- 1 extra bedroom → price +$15,000
- 1 year older → price -$1,000

---

## What Changes vs Simple

| | Simple | Multiple |
|--|--------|---------|
| Predictors | 1 | 2+ |
| Formula | y = mx + b | y = b0 + b1x1 + b2x2... |
| R-squared | raw | use **adjusted** R-squared |
| Risk | low | multicollinearity possible |

---

## Use Cases

**Salary prediction**
```
salary = 20000 + 5000*experience + 8000*education_years + 3000*certifications
```

**Sales forecasting**
```
sales = 1000 + 8*ad_spend + 200*store_size - 50*competitor_stores
```

**Medical: predict blood pressure**
```
bp = 80 + 0.5*age + 2*bmi - 5*exercise_hours
```

---

## In R

```r
# Fit the model
model <- lm(price ~ sqft + bedrooms + age, data = df)

# Summary
summary(model)

# Predict
predict(model, newdata = data.frame(sqft = 1500, bedrooms = 3, age = 10))
```

---

## Key Output to Read

```
Coefficients:
              Estimate   p-value
(Intercept)    50000     <0.001   ← b0
sqft             200     <0.001   ← significant
bedrooms       15000      0.03    ← significant
age            -1000      0.21    ← NOT significant (maybe drop)

Adjusted R-squared: 0.85         ← use this, not plain R-squared
```

**p-value < 0.05** → that predictor matters
**p-value > 0.05** → consider dropping it

---

## Adjusted R-squared — Why It Matters

Plain R-squared always goes up when you add variables, even useless ones.
Adjusted R-squared **penalizes** for adding weak predictors.

```r
summary(model)$r.squared          # raw
summary(model)$adj.r.squared      # use this
```

---

## New Problem: Multicollinearity

When two predictors are **too correlated with each other**, the model gets confused about which one is doing the work.

**Example:** including both `sqft` and `number_of_rooms` — they move together.

```r
library(car)
vif(model)   # Variance Inflation Factor
```

| VIF | Meaning |
|-----|---------|
| 1–5 | fine |
| 5–10 | concerning |
| 10+ | drop one of the correlated variables |

---

## Assumptions (same as simple + one more)

1. Linearity
2. Independence
3. Homoscedasticity
4. Normality of residuals
5. **No multicollinearity** ← new one

---

## Check Your Model in R

```r
plot(model)          # 4 diagnostic plots
vif(model)           # multicollinearity check
cor(df)              # correlation matrix between predictors
```

---

## Next Steps to Learn

1. **Feature selection** — which variables to keep (stepwise, AIC)
2. **Interaction terms** — what if sqft effect depends on location?
3. **Categorical variables** — how to include gender, region, etc. (dummy variables)
4. **Regularization** — Ridge, Lasso (when you have too many predictors)
5. **Logistic regression** — when y is yes/no instead of a number
