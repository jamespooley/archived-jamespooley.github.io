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


#### `match.call()`

The first edition of Hadley Wickham's [Advanced R](http://adv-r.had.co.nz/Expressions.html#capturing-call) shows how R can capture the current
call using `base::match.call()`.
