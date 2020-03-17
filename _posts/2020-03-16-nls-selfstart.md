---
layout: post
mathjax: true
title: "Self-Starting NLS Functions Step-by-Step"
---

This note provides a step-by-step guide to the technical details involded
in creating  `nls::selfStart()` functions.


As a working example, let's consider the Beverton-Hold model, which has the functional form:

$$
y = f(x) = \frac{\alpha \cdot x}{1 + \beta\cdot x}
$$


```r
beverton_holt <- function(x, alpha, beta) {
  (alpha * x) / (1 + beta * x)
}
```


<img src="/assets/observed-data.png" width="350" alt="Simulated Beverton-Holt data">


#### `match.call()`

Per the official documentation, `match.call()` "returns a call in which all
of the specified arguments are specified by their full names."

The first edition of Hadley Wickham's [Advanced R](http://adv-r.had.co.nz/Expressions.html#capturing-call) shows how R can capture the current
call using `base::match.call()`.
