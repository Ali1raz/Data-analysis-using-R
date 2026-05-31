## One Rule to Remember

---

> ## "Tell ggplot WHERE the data is, WHAT to plot, and HOW to draw it."

---

```r
ggplot(WHERE,  aes(WHAT)) +
  HOW()
```

That's it. Every plot you ever make follows this.

---

## Applied to mtcars right now

**Question:** "I want to see mpg vs weight"

```r
ggplot(mtcars,        # WHERE — my dataset
  aes(x = wt,        # WHAT  — x axis
      y = mpg)) +    # WHAT  — y axis
  geom_point()       # HOW   — draw as dots
```

Done. That's a scatter plot.

---

## Change only the HOW — get a different chart

```r
# same WHERE and WHAT, just swap HOW
ggplot(mtcars, aes(x = wt, y = mpg)) + geom_point()  # scatter
ggplot(mtcars, aes(x = wt, y = mpg)) + geom_line()   # line
ggplot(mtcars, aes(x = mpg))         + geom_histogram() # histogram — only needs x
ggplot(mtcars, aes(x = factor(cyl), y = mpg)) + geom_boxplot() # box
```

**Remember:** the only thing changing is the last line.

---

## Want color? Add it to WHAT

```r
ggplot(mtcars,
  aes(x = wt,
      y = mpg,
      color = factor(cyl))) +   # color is just another WHAT
  geom_point()
```

---

## Want a title? Add a layer with `+`

```r
ggplot(mtcars, aes(x = wt, y = mpg)) +
  geom_point() +
  labs(title = "Weight vs MPG")   # just another layer
```

---

## The mental checklist before writing any plot

```
1. WHERE is my data?        → first argument
2. WHAT goes on x axis?     → aes(x = ...)
3. WHAT goes on y axis?     → aes(y = ...)
4. HOW do I draw it?        → geom_point / geom_col / geom_histogram etc
```

Answer those 4 questions → you have a plot.

