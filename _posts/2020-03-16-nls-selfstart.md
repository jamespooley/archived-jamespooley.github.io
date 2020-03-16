---
layout: post
mathjax: true
title: "`nls::selfStart` Functions Step-by-Step"
---

Technical details on [`nls::selfStart()`](https://stat.ethz.ch/R-manual/R-devel/library/stats/html/selfStart.html) functions


Let's consider the Beverton-Hold model, which has the functional form:

$$
y = f(x) = \frac{\alpha \cdot x}{1 + \beta\cdot x}
$$


### `match.call()`

The first edition of Hadley Wickham's [Advanced R](http://adv-r.had.co.nz/Expressions.html#capturing-call) shows how R can capture the current
call using `base::match.call()`.
