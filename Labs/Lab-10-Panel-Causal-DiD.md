---
title: "Lab 10: Panel Data, Causal Inference and Difference-in-Differences"
subtitle: "Econometrics II - YTU"
author:
  name: "Prof. Dr. Hüseyin Taştan"
  affiliation: "Yıldız Technical University"
# date: "07 June 2026"
date: 2021 Spring
output:
  html_document:
    number_sections: true
    theme: lumen
    highlight: haddock
    # code_folding: show
    toc: yes
    toc_depth: 3
    toc_float: yes
    keep_md: true
---

<style type="text/css">
body{
  background-color: #FAFAFA;
  font-size: 18px;
  line-height: 1.8;
}
code.r{
  font-size: 12pt;
}
</style>
<br>



In this lab we use the `wooldridge` package (which ships the textbook data sets) and
the `plm` package for panel-data estimation. The empirical examples reproduce
Wooldridge's *Introductory Econometrics* (Examples 14.4, 13.9, 13.3 and 13.4).
Install the packages once if needed:


```r
install.packages(c("wooldridge", "plm", "lmtest", "sandwich", "stargazer"))
```


```r
library(wooldridge)
library(plm)
library(lmtest)
library(sandwich)
library(stargazer)
```

# Part I: Panel Data

## Pooled OLS, Fixed Effects, and Random Effects (Wooldridge, Example 14.4)

We use the `wagepan` data set: a balanced panel of 545 men observed over
1980--1987 (`nr` = person id, `year` = time). The outcome is the log hourly
wage (`lwage`). This reproduces Wooldridge's **Example 14.4**, a log wage
equation with education, race, labor-market experience, marital and union
status, and a full set of year dummies (`d81`--`d87`).


```r
data("wagepan")
# declare the panel structure: individual index = nr, time index = year
pdat <- pdata.frame(wagepan, index = c("nr", "year"))
pdim(pdat)
```

```
## Balanced Panel: n = 545, T = 8, N = 4360
```

We estimate the same specification three ways. Define the formula once:


```r
f <- lwage ~ educ + black + hisp + exper + expersq + married + union +
     d81 + d82 + d83 + d84 + d85 + d86 + d87
```

* **Pooled OLS** ignores the panel structure (treats every observation as independent).
* **Random effects (RE)** keeps the unobserved effect $a_i$ but assumes it is *uncorrelated* with the regressors.
* **Fixed effects (FE, within)** allows $a_i$ to be correlated with the regressors by demeaning within each person.


```r
pooled <- plm(f, data = pdat, model = "pooling")
re     <- plm(f, data = pdat, model = "random")
fe     <- plm(f, data = pdat, model = "within")
```

Compare the three estimators side by side:


```r
stargazer(pooled, re, fe, type = "text",
          column.labels = c("Pooled", "RE", "FE"),
          keep = c("educ", "black", "hisp", "exper", "expersq", "married", "union"),
          keep.stat = c("n", "rsq"))
```

```
## 
## ==========================================
##                   Dependent variable:     
##              -----------------------------
##                          lwage            
##               Pooled      RE        FE    
##                 (1)       (2)       (3)   
## ------------------------------------------
## educ         0.091***  0.092***           
##               (0.005)   (0.011)           
##                                           
## black        -0.139*** -0.139***          
##               (0.024)   (0.048)           
##                                           
## hisp           0.016     0.022            
##               (0.021)   (0.043)           
##                                           
## exper        0.067***  0.106***  0.132*** 
##               (0.014)   (0.015)   (0.010) 
##                                           
## expersq      -0.002*** -0.005*** -0.005***
##               (0.001)   (0.001)   (0.001) 
##                                           
## married      0.108***  0.064***   0.047** 
##               (0.016)   (0.017)   (0.018) 
##                                           
## union        0.182***  0.106***  0.080*** 
##               (0.017)   (0.018)   (0.019) 
##                                           
## ------------------------------------------
## Observations   4,360     4,360     4,360  
## R2             0.189     0.181     0.181  
## ==========================================
## Note:          *p<0.1; **p<0.05; ***p<0.01
```

Reading the table (these match Wooldridge's Table 14.4):

* The return to education is about **9.1%** per year in pooled OLS and RE, but it **cannot be estimated** under FE -- `educ` is constant over time for each man, so it is absorbed by the individual effect $a_i$. The same happens to `black` and `hisp`.
* The **marriage premium** falls from **0.108** (pooled) to **0.064** (RE) to **0.047** (FE).
* The **union premium** falls from **0.182** (pooled) to **0.106** (RE) to **0.080** (FE).

The cross-sectional (pooled) premia overstate the causal effect: married/union
men differ in stable, unobserved ways (e.g. ability, job attachment) that are
correlated with wages. FE differences those traits away, leaving a smaller --
and more credible -- estimate.

## Time-invariant regressors drop out under FE

Education does not change over the sample for these men, so under FE it is
perfectly absorbed by the individual effect $a_i$ and is not estimable:


```r
coef(fe)["educ"]   # NA: educ is time-invariant and is differenced away
```

```
## <NA> 
##   NA
```

(Note that `exper` is also dropped from the FE model: since experience increases
by exactly one each year for everyone, its within-person variation is collinear
with the year dummies.)

## Hausman test: FE vs RE

The Hausman test compares the FE and RE estimates. Under $H_0$ the unobserved
effect is uncorrelated with the regressors (RE is consistent and efficient);
under $H_1$ only FE is consistent.


```r
phtest(fe, re)
```

```
## 
## 	Hausman Test
## 
## data:  f
## chisq = 31.707, df = 10, p-value = 0.000448
## alternative hypothesis: one model is inconsistent
```

The test statistic is about $\chi^2 = 31.7$ ($p \approx 0.0004$), so we **reject**
$H_0$: the unobserved individual effect is correlated with the regressors and the
**fixed effects** estimator is preferred over random effects.

## Clustered standard errors

Idiosyncratic errors are typically serially correlated within a person, so we
cluster standard errors at the individual level:


```r
coeftest(fe, vcov = vcovHC(fe, type = "sss", cluster = "group"))
```

```
## 
## t test of coefficients:
## 
##            Estimate  Std. Error t value  Pr(>|t|)    
## exper    0.13214642  0.01200666 11.0061 < 2.2e-16 ***
## expersq -0.00518550  0.00081015 -6.4007 1.735e-10 ***
## married  0.04668036  0.02100141  2.2227 0.0262929 *  
## union    0.08000186  0.02274049  3.5180 0.0004398 ***
## d81      0.01904479  0.02272406  0.8381 0.4020334    
## d82     -0.01132198  0.02121426 -0.5337 0.5935825    
## d83     -0.04199552  0.02050633 -2.0479 0.0406354 *  
## d84     -0.03847088  0.02116972 -1.8173 0.0692559 .  
## d85     -0.04324982  0.01759294 -2.4584 0.0140015 *  
## d86     -0.02738194  0.01621620 -1.6886 0.0913866 .  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

## Two-period first differencing (Wooldridge, Example 13.9)

With only two periods, the fixed-effects estimator is identical to first
differencing. We reproduce Wooldridge's **Example 13.9** using `crime2`: 46
cities observed in 1982 and 1987.

First, a pooled regression in **levels** (with a year dummy `d87`) finds
essentially no relationship between unemployment and the crime rate:


```r
data("crime2")
summary(lm(crmrte ~ d87 + unem, data = crime2))
```

```
## 
## Call:
## lm(formula = crmrte ~ d87 + unem, data = crime2)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -53.474 -21.794  -6.266  18.297  75.113 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept)  93.4202    12.7395   7.333 9.92e-11 ***
## d87           7.9404     7.9753   0.996    0.322    
## unem          0.4265     1.1883   0.359    0.720    
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 29.99 on 89 degrees of freedom
## Multiple R-squared:  0.01221,	Adjusted R-squared:  -0.009986 
## F-statistic: 0.5501 on 2 and 89 DF,  p-value: 0.5788
```

The unemployment coefficient (about 0.43) is small and statistically
**insignificant** -- an implausible result. Cities with high and low
unemployment differ in many unobserved, time-constant ways (the omitted city
effect), which contaminates the levels comparison.

`crime2` already stores the changes on its 1987 rows (`ccrmrte` = $\Delta$ crime
rate, `cunem` = $\Delta$ unemployment). Regressing the change in the crime rate
on the change in unemployment removes any time-constant city characteristics:


```r
fd <- lm(ccrmrte ~ cunem, data = subset(crime2, year == 87))
summary(fd)
```

```
## 
## Call:
## lm(formula = ccrmrte ~ cunem, data = subset(crime2, year == 87))
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -36.912 -13.369  -5.507  12.446  52.915 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)   
## (Intercept)  15.4022     4.7021   3.276  0.00206 **
## cunem         2.2180     0.8779   2.527  0.01519 * 
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 20.05 on 44 degrees of freedom
## Multiple R-squared:  0.1267,	Adjusted R-squared:  0.1069 
## F-statistic: 6.384 on 1 and 44 DF,  p-value: 0.01519
```

Now the effect is **positive and significant**: a one-point rise in the
unemployment rate is associated with about **2.2 more crimes per 1,000 people**
(the intercept, $\approx 15.4$, captures the secular increase in crime between
1982 and 1987). Differencing out the time-constant city heterogeneity uncovers
the relationship that the levels regression missed.

# Part II: Causal Inference & Difference-in-Differences

## Difference-in-Differences: the incinerator example (Wooldridge, Examples 13.3 & 13.4)

Did the rumor and construction of a garbage incinerator in North Andover, MA
depress nearby house prices? We use repeated cross sections of house sales in
**1978** (before) and **1981** (after).

* Treatment group: houses **near** the incinerator site (`nearinc = 1`).
* Control group: houses farther away (`nearinc = 0`).
* `y81 = 1` for the after period (1981).

The $2\times 2$ table of average **real** prices (1978 dollars):


```r
data("kielmc")
aggregate(rprice ~ y81 + nearinc, data = kielmc, FUN = mean)
```

```
##   y81 nearinc    rprice
## 1   0       0  82517.23
## 2   1       0 101307.51
## 3   0       1  63692.86
## 4   1       1  70619.24
```

**Example 13.3** -- the simple DiD is the coefficient on the **interaction**
`y81:nearinc`:


```r
did <- lm(rprice ~ nearinc + y81 + y81:nearinc, data = kielmc)
summary(did)
```

```
## 
## Call:
## lm(formula = rprice ~ nearinc + y81 + y81:nearinc, data = kielmc)
## 
## Residuals:
##    Min     1Q Median     3Q    Max 
## -60678 -17693  -3031  12483 236307 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept)    82517       2727  30.260  < 2e-16 ***
## nearinc       -18824       4875  -3.861 0.000137 ***
## y81            18790       4050   4.640 5.12e-06 ***
## nearinc:y81   -11864       7457  -1.591 0.112595    
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 30240 on 317 degrees of freedom
## Multiple R-squared:  0.1739,	Adjusted R-squared:  0.1661 
## F-statistic: 22.25 on 3 and 317 DF,  p-value: 4.224e-13
```

The point estimate is about **-\$11,864**: houses near the incinerator lost
value relative to the control group after 1981. On its own, however, the
estimate has a $t$-statistic around $-1.6$ and is **not** statistically
significant.

**Example 13.4** -- adding house characteristics (so that parallel trends needs
to hold only *conditionally*) sharpens the estimate. Following the textbook we
use the log of price:


```r
did_ctrl <- lm(lprice ~ nearinc + y81 + y81:nearinc +
                 age + agesq + log(intst) + log(land) + log(area) +
                 rooms + baths, data = kielmc)
coeftest(did_ctrl)["nearinc:y81", ]
```

```
##    Estimate  Std. Error     t value    Pr(>|t|) 
## -0.13151383  0.05197130 -2.53050870  0.01188438
```

The interaction is now about **-0.132** and statistically significant: after the
incinerator, houses near the site fell roughly **13%** in value relative to
comparable houses farther away.

## Card and Krueger (1994): minimum wage and employment

The original Card--Krueger micro data are not in the `wooldridge` package, but
the famous result is just a $2\times 2$ difference-in-differences. We reproduce
it from the published group--period averages of full-time-equivalent (FTE)
employment (Card and Krueger, 1994, Table 3):


```r
ck <- data.frame(
  state  = c("PA", "PA", "NJ", "NJ"),
  period = c("before", "after", "before", "after"),
  fte    = c(23.33, 21.17, 20.44, 21.03)
)
ck
```

```
##   state period   fte
## 1    PA before 23.33
## 2    PA  after 21.17
## 3    NJ before 20.44
## 4    NJ  after 21.03
```

Compute the two changes over time and the difference-in-differences:


```r
d_PA <- ck$fte[ck$state=="PA" & ck$period=="after"] -
        ck$fte[ck$state=="PA" & ck$period=="before"]
d_NJ <- ck$fte[ck$state=="NJ" & ck$period=="after"] -
        ck$fte[ck$state=="NJ" & ck$period=="before"]

d_PA          # change in control group (PA)
d_NJ          # change in treatment group (NJ)
d_NJ - d_PA   # difference-in-differences estimate
```

```
## [1] -2.16
## [1] 0.59
## [1] 2.75
```

The DiD estimate is about **+2.75 FTE workers**: despite the minimum-wage
increase in New Jersey, employment did not fall (it rose slightly relative to
Pennsylvania) -- the result that helped launch the modern "credibility
revolution" in empirical economics.

## A visual check of parallel trends

Plotting the group means makes the logic transparent. The DiD effect is the
gap between New Jersey's observed point and its counterfactual (the Pennsylvania
trend applied to New Jersey):


```r
t <- c(0, 1)                                   # 0 = before, 1 = after
PA <- c(23.33, 21.17)                          # control
NJ <- c(20.44, 21.03)                          # treatment (observed)
NJ_cf <- c(20.44, 20.44 + (PA[2]-PA[1]))       # counterfactual (parallel to PA)

plot(t, NJ, type="b", pch=19, col="red", ylim=c(18,24),
     xaxt="n", xlab="", ylab="FTE employment",
     main="Card-Krueger: DiD")
axis(1, at=t, labels=c("Before", "After"))
lines(t, PA, type="b", pch=19, col="blue")
lines(t, NJ_cf, type="b", pch=1, lty=2, col="red")
legend("bottomleft", bty="n",
       legend=c("NJ (treatment)", "PA (control)", "NJ counterfactual"),
       col=c("red","blue","red"), pch=c(19,19,1), lty=c(1,1,2))
```

![](Lab-10-Panel-Causal-DiD_files/figure-html/unnamed-chunk-17-1.png)<!-- -->

## Exercises

1. In the `wagepan` example, try to add `exper` back to the FE model on its own.
   Why does experience cause a problem once the year dummies are included?
2. Re-estimate Example 13.4 using `log(rprice)` (the *real* price) instead of
   `lprice`. Does the interaction coefficient change much? Why or why not?
3. Using `crime2`, look at `?crime2` and add another differenced control (a
   variable starting with `c`, e.g. the change in police per capita) to the
   first-differenced model. Does the change in unemployment remain significant?
4. For the Card--Krueger numbers, set up a small "long" data frame with a
   treatment dummy, a post dummy, and their interaction, and recover the DiD
   estimate (+2.75) from an OLS regression.
