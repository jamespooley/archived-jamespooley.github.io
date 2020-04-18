---
layout: post
mathjax: true
title: "Self-Starting NLS Functions Step-by-Step"
---

This note provides a step-by-step guide to the technical details involded
in creating  **`nls::selfStart()`** functions.


#### Setting the Stage

When fitting nonlinear regression models via **`stats::nls()`** in R, you must provide
starting values by binding a vector of initial guesses to this function's
`start` argument.


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

Per the official documentation, **`match.call()`** "returns a call in which all
of the specified arguments are specified by their full names." You can think
of this as R's being incredibly OCD.

The first edition of Hadley Wickham's [Advanced R](http://adv-r.had.co.nz/Expressions.html#capturing-call) shows how R can capture the current
call using `base::match.call()`.


### Self-Starters for Advertising Media Data

#### Gompertz Curve

The Gompertz occasionally shows up in MMM analyses, where it sometimes goes by the name
of "double exponential" curve. MMM modelers like this curve becuase it allows you to get
both "breakthrough" and "saturation" points.

There are a variety of parameterizations of
this function (e.g., see [here](https://en.wikipedia.org/wiki/Gompertz_function)
and [here](https://mathworld.wolfram.com/GompertzCurve.html)), with the parameterization I've
typically seen used by media professionals being:

$$
y = beta ^ ( alpha ^ x),
$$

where $y$ is the response (e.g., revenue) to media intensity $x$ (e.g., spend).

The [`nls::SSgompertz()`](https://www.rdocumentation.org/packages/stats/versions/3.6.2/topics/SSgompertz)
self-starter estimates parameters for the following model:

$$
a*exp(-b\cot c^x).
$$

To convert between the estimates returned by `nls::SSgompertz()` and the parameterization
familiar to your media colleagues, the following rules apply:

* The asymptote $\alpha$ remains as is, and no further transformations are required
* The value of $c$ can remain as-is
* To get the value of $\beta$ familiar to your colleagues in media, exponentiate $-b$: $\exp(-\beta)$

#### Example

Here's an (admittedly contrived) example. Let's generate some data where we know the
ground truth.

```r
library(tidyverse)

dbl_exponential <- function(x, alpha, beta, gamma) {
  alphaa * (beta ^ alpha ^ x)
}

df <- tibble(
  x = seq(5, 15, by = 0.2),
  y = dbl_exponential(x, 100, 0.63, 0.0001) + rnorm(length(x), sd = 5)
)

ggplot(df, aes(x, y)) +
  geom_point()
```

We can fit a Gompertz curve to these data using `nls`'s built-in self-starter and
extract the parameter estimates as follows:

```r
params_dblexp <- nls(y ~ SSgompertz(x, alpha, beta, gamma), data = df) %>% 
  summary() %>%
  purrr::pluck("parameters") %>% 
  as.data.frame() %>% 
  rownames_to_column() %>% 
  rename(parameter = rowname) %>% 
  janitor::clean_names(case = "snake") %>% 
  pluck("estimate")
```
