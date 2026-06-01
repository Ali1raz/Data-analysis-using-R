# Logistic Regression

(search the web for more)

## What
Predicts a **category**, not a number.

**Output is a probability between 0 and 1.**

---

## Difference from Linear Regression

| | Linear | Logistic |
|---|---|---|
| Predicts | Number | Category |
| Output | Any value | 0 to 1 |
| Example | Salary | Pass/Fail |

---

## Core Idea
Instead of a line, it uses an **S-curve (sigmoid)** to squish any value between 0 and 1.

$$P(Y=1) = \frac{1}{1 + e^{-(\beta_0 + \beta_1X)}}$$

- Output > 0.5 → predict **1** (yes/pass/spam)
- Output < 0.5 → predict **0** (no/fail/not spam)

---

## Scenarios & Use Cases

| X | Y |
|---|---|
| Study hours | Pass / Fail |
| Age + cholesterol | Heart disease yes/no |
| Email content | Spam / Not spam |
| Transaction amount | Fraud / Not fraud |

---

## Build It in R

```r
# Data
hours <- c(1, 2, 3, 4, 5, 6, 7, 8)
pass  <- c(0, 0, 0, 0, 1, 1, 1, 1)

df <- data.frame(hours, pass)

# Fit model
model <- glm(pass ~ hours, data = df, family = binomial)

# Summary
summary(model)

# Predict probability
predict(model, newdata = data.frame(hours = 5), type = "response")
# → 0.73 means 73% chance of passing
```

---

## Reading the Output

```
Coefficients:
             Estimate
(Intercept)   -4.90
hours          1.10
```

- **Intercept -4.90** → log-odds of passing with 0 study hours
- **hours 1.10** → each extra hour increases log-odds by 1.10
- Positive coefficient → more X = more likely to be 1

---

## Key Differences in R

| | Linear | Logistic |
|---|---|---|
| Function | `lm()` | `glm()` |
| Extra argument | none | `family = binomial` |
| Predict | number | probability |

---

## Next Steps
- **Next** → confusion matrix (how to evaluate logistic regression)
- **Or** → what are odds and log-odds (understand the coefficients deeper)
- **Or** → multiple logistic regression (more than one X)
