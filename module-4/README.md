# Simple Linear Regression

**Concept:** Predict Y from one X using a straight line: `Y = β0 + β1*X + ε`

**Scenario:** Predict house price from square footage.

```r
model <- lm(price ~ sqft, data = houses)
summary(model)
```

**Use cases:** Sales vs ad spend, height vs weight, temperature vs ice cream sales.

---

# Residuals & Assumptions

**Residual** = actual - predicted. Good model → residuals are random noise.

**4 assumptions (LINE):**
- **L**inearity
- **I**ndependence of errors
- **N**ormality of residuals
- **E**qual variance (homoscedasticity)

```r
plot(model)          # 4 diagnostic plots
shapiro.test(resid(model))   # test normality
```

**Scenario:** After fitting salary ~ experience, residuals fan out → variance isn't equal → violated homoscedasticity → try log(salary).

---

# Multiple Regression

**Concept:** Predict Y from 2+ variables: `Y = β0 + β1X1 + β2X2 + ... + ε`

```r
model <- lm(price ~ sqft + bedrooms + location, data = houses)
summary(model)
```

**Scenario:** Predict student GPA from study hours + sleep + attendance.

**Key:** Each coefficient = effect of that variable *holding others constant*.

---

# Feature Selection

**Goal:** Keep only variables that matter. Too many → overfitting.

**Methods:**

```r
# Stepwise (AIC-based)
step(model, direction = "both")

# Start from nothing, build up
null <- lm(price ~ 1, data = houses)
full <- lm(price ~ ., data = houses)
step(null, scope = list(upper = full), direction = "forward")
```

**Scenario:** 20 predictors for loan default → stepwise keeps 6 meaningful ones.

---

# R², p-values, AIC

**R²** — % of variance explained by model (0 to 1). Higher = better fit.

```r
summary(model)$r.squared
```
> R² = 0.82 → model explains 82% of price variation.

**p-value** — probability result is due to chance. p < 0.05 → variable is significant.

```r
summary(model)   # shows p-value per coefficient
```
> bedrooms p = 0.41 → not significant → consider removing it.

**AIC** — penalizes complexity. *Lower AIC = better model.* Use to compare models, not interpret alone.

```r
AIC(model1, model2)
```
> model1 AIC=1200, model2 AIC=1150 → prefer model2.

---

# Train/Test Split

**Goal:** Test model on unseen data → honest estimate of performance.

```r
set.seed(42)
idx <- sample(1:nrow(houses), 0.8 * nrow(houses))

train <- houses[idx, ]
test  <- houses[-idx, ]

model <- lm(price ~ sqft + location, data = train)

preds <- predict(model, newdata = test)
# RMSE
sqrt(mean((test$price - preds)^2))
```

**Scenario:** Train on 80% of customer data, test on 20% → if RMSE jumps on test set → model is overfit.

**Next steps:**
- Try `caret` or `rsample` for cleaner splitting
- Use k-fold cross-validation: `trainControl(method = "cv", number = 10)` in `caret`
