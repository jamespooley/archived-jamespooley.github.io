---
layout: post
title: "nls::selfStart Step-by-Step"
---

Technical details on `nls::selfStart()` functions


The official documentation gives the following example:

```R
initLogis <- function(mCall, data, LHS) {
  xy <- data.frame(sortedXyData(mCall[["input"]], LHS, data))
  if(nrow(xy) < 4)
    stop("too few distinct input values to fit a logistic model")
  z <- xy[["y"]]
  ## transform to proportion, i.e. in (0,1) :
  rng <- range(z); dz <- diff(rng)
  z <- (z - rng[1L] + 0.05 * dz)/(1.1 * dz)
  xy[["z"]] <- log(z/(1 - z))		# logit transformation
  aux <- coef(lm(x ~ z, xy))
  pars <- coef(nls(
    y ~ 1/(1 + exp((xmid - x)/scal)),
    data = xy,
    start = list(xmid = aux[[1L]], scal = aux[[2L]]),
    algorithm = "plinear"
  ))
  setNames(pars[c(".lin", "xmid", "scal")],
           nm = mCall[c("Asym", "xmid", "scal")])
}

SSlogis <- selfStart(~ Asym/(1 + exp((xmid - x)/scal)),
                     initial = initLogis,
                     parameters = c("Asym", "xmid", "scal"))
```


Let's consider the Beverton-Hold model, which has the functional form:

$$
y = \frac{\alpha \cdot x}{1 + \beta\cdot x}
$$


The first edition of Hadley Wickham's [Advanced R](http://adv-r.had.co.nz/Expressions.html#capturing-call) shows how R can capture the current
call using `base::match.call()`.
