---
title: "Lab 01: Multiple Regression Model in Matrix Form"
subtitle: "Econometrics II - YTU"
author: 
  name: "Prof. Dr. Hüseyin Taştan"
  affiliation: "Yıldız Technical University"
# date: "16 Mart 2021"
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



# Classical Regression Model
The regression model with _k_ parameters can be written for each observation _i_:
$$y_i=\beta_1+\beta_2 x_{i2}+\beta_3 x_{i3}+\beta_k x_{ik}+u_i,\,\,\,\,i=1,...,n$$
where $y_i$ is the dependent variable for observation _i_, and $x_{ij}$ , $j = 2, 3 . . . , k$, is $i$th observed value for $j$th independent variable. $u_i$ is the random error term. We can also write a system of _n_ equations with _k_ unknowns:
$$
\left[\begin{array}
{c}
y_1\\y_2\\ \vdots\\y_n
\end{array}\right]=
\left[\begin{array}
{cccc}
1&x_{12}&\dots&x_{1k}\\
1&x_{22}&\dots&x_{2k}\\
\vdots&\vdots&\ddots&\vdots\\
1&x_{n2}&\vdots&x_{nk}
\end{array}\right]
\left[\begin{array}
{c}
\beta_1\\\beta_2\\ \vdots\\\beta_k
\end{array}\right]+
\left[\begin{array}
{c}
u_1\\u_2\\ \vdots\\u_n
\end{array}\right]
$$
Let us define the following vectors and matrices:
$$
\mathbf{y} = \left[\begin{array}
{c}
y_1\\y_2\\ \vdots\\y_n
\end{array}\right],\,\,\,
\mathbf{X} =\left[\begin{array}
{cccc}
1&x_{12}&\dots&x_{1k}\\
1&x_{22}&\dots&x_{2k}\\
\vdots&\vdots&\ddots&\vdots\\
1&x_{n2}&\vdots&x_{nk}
\end{array}\right],\,\,\,
\mathbf{\beta} =\left[\begin{array}
{c}
\beta_1\\\beta_2\\ \vdots\\\beta_k
\end{array}\right],\,\,\,
\mathbf{u} =\left[\begin{array}
{c}
u_1\\u_2\\ \vdots\\u_n
\end{array}\right]
$$
Then we can write the model as
$$
\underbrace{y}_\text{n × 1}=\underbrace{X}_\text{n × k}\underbrace{\beta}_\text{k × 1}+\underbrace{u}_\text{n × 1}
$$
Another way of writing this model is based on collecting the $i$th observed values for independent variables in a $k \times 1$ vector:
$$
{x_i} = \left[\begin{array}
{ccccc}
1&x_{i2}&x_{i3}& \dots&x_{ik}
\end{array}\right]^\top
$$
Using this notation, the multiple linear regression model can be written as
$$
y_i=x_i^\top\beta+u_i,\,\,\,i=1,...,n
$$

**Assumptions of the (neo)-Classical Linear Regression Model**

1. The model is linear in parameters: $\mathbf{y = X\beta + u}$\
2. $\mbox{rank}(\mathbf{X})=k$, (No perfect collinearity, i.e. The columns of $\mathbf{X}$ are independent of each other.)\
3. $\mbox{E}[\mathbf{u}|\mathbf{X}]=\mathbf{0}_{n \times 1}$, (Zero conditional mean)\
4. $\mbox{Var}[\mathbf{u}|\mathbf{X}]= \mbox{E}[\mathbf{uu^\top}|\mathbf{X}]=\sigma^2 \mathbf{I}_n$, (Homoscedasticity and no serial correlation\
5. $\mathbf{u}|\mathbf{X}~\sim~N(\mathbf{0}_{n}, ~\sigma^2 \mathbf{I}_n)$, (the random error is distributed as multivariate normal conditional on $\mathbf{X}$.)


# Ordinary Least Squares (OLS) Estimator

The Sample Regression Function (SRF) can be written as 

\[
y = X\hat{\beta} + \hat{u}
\]

where $\hat{\beta}$ is $k\times1$ vector of OLS estimators, $\hat{u}$ is a $n\times1$ vector of residuals. The OLS method chooses the vector $\hat{\beta}$ by minimizing the sum of squared residuals (SSR):

$$\hat{\beta}=\underset{b}{\text{arg min }}SSR(b)$$
The OLS estimator is defined as the solution that minimizes the SSR. Using the summation notation we can also write
$$SSR(\hat{\beta})=\sum_{i=1}^{n}\hat{u}_i^2=\hat{u}^\top\hat{u}$$
or,
$$SSR(\hat{\beta})=\sum_{i=1}^{n}\hat{u}_i^2=\sum_{i=1}^{n}\left(y_i-x_i^\top\hat{\beta}\right)^2$$
With the first notation, the optimization problem is:
$$\underset{\hat{\beta}}{\text{min }}SSR(\hat{\beta})=\hat{u}^\top\hat{u}$$
or with the second notation:
$$\underset{\hat{\beta}}{\text{min }}SSR(\hat{\beta})=\sum_{i=1}^{n}\left(y_i-x_i^\top\hat{\beta}\right)^2$$
Let us write explicitly the sum of squared residuals to obtain the first order conditions (FOCs) using the first notation.
$$\begin{aligned}
SSR(\hat{\beta}) &=\hat{u}^\top\hat{u} \\
&=\left(y-X\hat{\beta} \right)^\top \left(y-X\hat{\beta} \right)\\
&=y^\top y-2\hat{\beta}^\top X^\top y + \hat{\beta}^\top X^\top X\hat{\beta}
\end{aligned}$$
Then the first order conditions of OLS problem can be written as
$$
\frac{\partial SSR(\hat{\beta})}{\partial\hat{\beta}}=-2X^\top y+2X^\top X \hat{\beta}=0_k
$$
from which we obtain the normal equations:
$$X^\top X \hat{\beta}=X^\top y$$

When we multiply the both sides of the normal equations by $(X^\top X)^{-1}$, we obtain the OLS estimator
$$\hat{\beta}=(X^\top X)^{-1}X^\top y$$

## Example: Intercept only 

Suppose that there is only an intercept in the model with no independent variables:
$$y_i=\beta_1+u_i,\,\,\,\,i=1,...,n$$
In this case _$\textbf{X}$_ matrix is a $n \times 1$ vector in which all elements are ones. Let’s call this vector _$\textbf{ı}$_:
$$
\textit {ı} = \left[\begin{array}
{cccc}
1&1&\dots&1
\end{array}\right]^\top=\textbf{X}
$$
The OLS estimator of $\beta_1$ can be written as:
$$\hat{\beta}=(X^\top X)^{-1}X^\top y=(\textit{ı}^\top \textit{ı})^{-1}\textit{ı}^\top y$$
$$\textit{ı}^\top \textit{ı}=
\left[\begin{array}
{cccc}
1&1&\dots&1
\end{array}\right]
\left[\begin{array}
{cccc}
1\\ 1\\ \vdots\\1
\end{array}\right]=n
$$
and
$$\textit{ı}^\top y=
\left[\begin{array}
{cccc}
1&1&\dots&1
\end{array}\right]
\left[\begin{array}
{cccc}
y_1\\ y_2\\ \vdots\\y_n
\end{array}\right]=\sum_{y=1}^{n}y_i
$$
then the OLS estimator is
$$\hat{\beta}_1=n^{-1}\sum_{y=1}^{n}y_i \equiv \bar{y}$$
which is the sample mean. 

## Example: Single Dummy regressor

Let’s consider a regression model with a binary (dummy) variable and
an intercept:
$$y_i=\delta_0+\delta_1D_i+u_i,\,\,\,\,i=1,...,n$$
For the sake of simplicity, suppose that the dependent variable consists of just 5
observations and the dummy variable is defined as follows:

$$y=\left[\begin{array}
{cccc}
1\\ 2\\ 3\\4 \\5
\end{array}\right],\,\,\,\,\,
D_i=
\left\{\begin{array}
{ccc}
1, & if\,\,\,\,\, y_i\leq 3\\
0, &otherwise
\end{array} \right.
\iff D=\left[\begin{array}
{cccc}
1\\ 1\\ 1\\0 \\0
\end{array}\right]$$

In this case, __X__:
$$
X=\left[\begin{array}
{cccc}
1&1\\ 1&1\\ 1&1\\1&0 \\1&0
\end{array}\right]
$$
Now let us find the OLS estimator $\hat{\beta}=[\begin{array} {cc} \hat{\delta}_0 & \hat{\delta}_1 \end{array}]^\top$


```r
# Install required package
# install.packages('matlib')
# Note: matlib may require rgl package: 
# install.packages("rgl", repos="http://R-Forge.R-project.org")

library(matlib)

# Generate X and y matrices
X <- cbind(c(1,1,1,1,1), c(1,1,1,0,0))
X
```

```
##      [,1] [,2]
## [1,]    1    1
## [2,]    1    1
## [3,]    1    1
## [4,]    1    0
## [5,]    1    0
```

```r
y <- c(1:5)
y
```

```
## [1] 1 2 3 4 5
```

```r
# Direct matrix multiplication "%*%"
beta_hat <- inv( t(X) %*% X ) %*% t(X) %*% y
beta_hat
```

```
##      [,1]
## [1,]  4.5
## [2,] -2.5
```

```r
# or using the solve function: 
beta_hat1 <- solve( t(X)%*%X, t(X)%*%y )
beta_hat1
```

```
##      [,1]
## [1,]  4.5
## [2,] -2.5
```
The estimated regression is $$y_i=4.5-2.5D_i$$
In our example, we can also find the fitted (estimated) values for the dependent variable and the vector of residuals.


```r
# Fitted values: y_hat = X*beta_hat
y_hat <- X%*%beta_hat
y_hat
```

```
##      [,1]
## [1,]  2.0
## [2,]  2.0
## [3,]  2.0
## [4,]  4.5
## [5,]  4.5
```

```r
# residuals: u_hat = y-y_hat
u_hat <- y-y_hat
round(u_hat, digits=2)
```

```
##      [,1]
## [1,] -1.0
## [2,]  0.0
## [3,]  1.0
## [4,] -0.5
## [5,]  0.5
```
Another way of including the dummy variable in the model is to add separately the
dummies for each category and excluding the intercept term. Consider the below model
in matrix notation form:
$$y_i=\gamma_0D_{i1}+\delta_0D_{i2}+u_i,\,\,\,\,i=1,...,n$$
Now __X__:
$$
X=\left[\begin{array}
{cccc}
1&0\\ 1&0\\ 1&0\\0&1 \\0&1
\end{array}\right]
$$

```r
# Install required package
# install.packages('matlib')
library(matlib)

# Generate X and y matrices
X <- cbind(c(1,1,1,0,0), c(0,0,0,1,1))
X
```

```
##      [,1] [,2]
## [1,]    1    0
## [2,]    1    0
## [3,]    1    0
## [4,]    0    1
## [5,]    0    1
```

```r
y <- c(1:5)
y
```

```
## [1] 1 2 3 4 5
```

```r
#Solve beta_hat by using matrix multiplication "%*%"
beta_hat <- inv(t(X)%*%X) %*% t(X)%*%y
beta_hat
```

```
##      [,1]
## [1,]  2.0
## [2,]  4.5
```

```r
# using solve function
beta_hat1 <- solve( t(X)%*%X, t(X)%*%y )
beta_hat1
```

```
##      [,1]
## [1,]  2.0
## [2,]  4.5
```

The estimated regression function is
$$\hat{y_i}=2D_{i1}+4.5D_{i2}$$
If we added an intercept term in this model, we would be in a dummy variable trap.
In this case, the model and corresponding data matrix are
$$y_i=\beta_0+\gamma_0D_{i1}+\delta_0D_{i2}+u_i,\,\,\,\,i=1,...,n$$
$$
X=\left[\begin{array}
{cccc}
1&1&0\\ 1&1&0\\ 1&1&0\\1&0&1 \\1&0&1
\end{array}\right]
$$
Clearly, the sum of the second and third column of __X__ matrix is the first
column of this matrix. The classical assumption about Rank condition is violated: _rank(__X__)_ < 3. Another way of detecting this violation is to calculate cross product matrix:
$$
X^\top X=\left[\begin{array}
{ccc}
5&3&2\\ 3&3&0\\ 2&0&2
\end{array}\right],\,\,\,\,\,
\left|X^\top X \right|=0
$$
The sum of the second and third column of this matrix is its first column and the
sum of the second and third row of this matrix is its first row. The model can not be
estimated in this form, because this matrix is singular and its determinant is 0. The
OLS estimator is undefined. So one of the columns in this matrix is redundant, it
should be excluded.



## A Numerical Example 

The data set is 
$$
y=\left[\begin{array}
{c}
3\\ 8\\ 18\\3 \\2 \\6\\6 \\11 \\6 \\6 \\1\\16\\10\\20\\12\\10\\18\\10\\5\\15
\end{array}\right],\,\,\,\,\,
X=\left[\begin{array}
{cc}
1&1\\ 1&4\\ 1&8\\1&0 \\1&1 \\1&2\\1&2 \\1&6 \\1&3 \\1&2 \\1&0\\1&7\\1&4\\1&9\\1&5\\1&4\\1&8\\1&5\\1&2\\1&
7\end{array}\right]
$$

To find the OLS estimator, first calculate the required quantities:
$X^\top X$, $(X^\top X)^{-1}$, $X^\top y$

```r
library(matlib)

# Generate X and y matrices
X <- cbind(rep(1,20), c(1,4,8,0,1,2,2,6,3,2,0,7,4,9,5,4,8,5,2,7))
X
```

```
##       [,1] [,2]
##  [1,]    1    1
##  [2,]    1    4
##  [3,]    1    8
##  [4,]    1    0
##  [5,]    1    1
##  [6,]    1    2
##  [7,]    1    2
##  [8,]    1    6
##  [9,]    1    3
## [10,]    1    2
## [11,]    1    0
## [12,]    1    7
## [13,]    1    4
## [14,]    1    9
## [15,]    1    5
## [16,]    1    4
## [17,]    1    8
## [18,]    1    5
## [19,]    1    2
## [20,]    1    7
```

```r
y <- c(3,8,18,3,2,6,6,11,6,6,1,16,10,20,12,10,18,10,5,15)
y
```

```
##  [1]  3  8 18  3  2  6  6 11  6  6  1 16 10 20 12 10 18 10  5 15
```

```r
#Solve beta_hat
XtX <- t(X)%*%X
XtX
```

```
##      [,1] [,2]
## [1,]   20   80
## [2,]   80  468
```

```r
invXtX <- inv(XtX)
invXtX
```

```
##             [,1]        [,2]
## [1,]  0.15810811 -0.02702703
## [2,] -0.02702703  0.00675676
```

```r
Xty <- t(X)%*%y
Xty
```

```
##      [,1]
## [1,]  186
## [2,] 1042
```

```r
beta.hat <- invXtX%*%Xty
beta.hat
```

```
##          [,1]
## [1,] 1.245943
## [2,] 2.013516
```

The sample regression function: $$\hat{y} = 1.2459 +2.0135x$$
$$SSR=\hat{u}^\top \hat{u}=20.173$$

```r
library(matlib)
uhat <- y - X%*%beta.hat
SSR <- t(uhat)%*%uhat
SSR
```

```
##          [,1]
## [1,] 20.17297
```
$$s^2=\frac{1}{n-k}SSR=\frac{1}{18}20.173=1.1207$$

```r
dof = dim(X)[1] - dim(X)[2]
dof
```

```
## [1] 18
```

```r
s2 <- SSR/dof
s2
```

```
##          [,1]
## [1,] 1.120721
```

The variance-covariance matrix estimate is
$$
\widehat{\mbox{Var}(\hat{\beta})}=s^2(X^\top X)^{-1}=1.1207
\left[\begin{array}
{cc}
0.1581 & -0.0270\\ -0.0270 & 0.0068
\end{array}\right]
=\left[\begin{array}
{cc}
0.1772 & -0.03031\\-0.0303 & 0.0076\end{array}\right]
$$

```r
s2 <- as.numeric(s2)
V <- s2 * invXtX
V
```

```
##             [,1]         [,2]
## [1,]  0.17719504 -0.030289753
## [2,] -0.03028975  0.007572441
```

Then $V(\hat{\beta_1})$:

```r
V.beta1 <- V[1,1]
V.beta1
```

```
## [1] 0.177195
```

$V(\hat{\beta_2})$:

```r
V.beta2 <- V[2,2]
V.beta2
```

```
## [1] 0.007572441
```
$Cov(\hat{\beta_1},\hat{\beta_2})$:

```r
cov <- V[1,2]
cov
```

```
## [1] -0.03028975
```

Standard Errors:
$$se(\hat{\beta_1})=\sqrt {V(\hat{\beta_1})} $$

```r
se.beta1 <- sqrt(V.beta1)
se.beta1
```

```
## [1] 0.4209454
```
$$se(\hat{\beta_2})=\sqrt {V(\hat{\beta_2})} $$

```r
se.beta2 <- sqrt(V.beta2)
se.beta2
```

```
## [1] 0.08701977
```
t-ratios:
$$t_{\hat{\beta_1}}=\frac{\hat{\beta_1}}{se(\hat{\beta_1})}$$

```r
t.beta1 <- beta.hat[1,1]/se.beta1
t.beta1
```

```
## [1] 2.959869
```
$$t_{\hat{\beta_2}}=\frac{\hat{\beta_2}}{se(\hat{\beta_2})}$$

```r
t.beta2 <- beta.hat[2,1]/se.beta2
t.beta2
```

```
## [1] 23.13861
```

## Example: Wage equation

This example uses the data set `wage1` from Wooldridge's text. 


```r
# Generate X and y matrices.
library(wooldridge)
data("wage1")
X <- cbind(rep(1,526),wage1$educ,wage1$exper,wage1$tenure,wage1$female,wage1$married)


colnames(X) <- c("intercept","educ","exper","tenure","female","married")
head(X)

y <- as.matrix(log(wage1$wage))
colnames(y) <- c("lwage")
head(y)
```

```
##      intercept educ exper tenure female married
## [1,]         1   11     2      0      1       0
## [2,]         1   12    22      2      1       1
## [3,]         1   11     2      0      0       0
## [4,]         1    8    44     28      0       1
## [5,]         1   12     7      2      0       1
## [6,]         1   16     9      8      0       1
##         lwage
## [1,] 1.131402
## [2,] 1.175573
## [3,] 1.098612
## [4,] 1.791759
## [5,] 1.667707
## [6,] 2.169054
```

Alternatively, one can use `model.matrix()` function to get the design matrix: 

```r
Xmatrix <- model.matrix(lm(log(wage) ~ educ + exper + tenure + female + married, data = wage1))
head(Xmatrix)
```

```
##   (Intercept) educ exper tenure female married
## 1           1   11     2      0      1       0
## 2           1   12    22      2      1       1
## 3           1   11     2      0      0       0
## 4           1    8    44     28      0       1
## 5           1   12     7      2      0       1
## 6           1   16     9      8      0       1
```

The OLS solution is

```r
# Solve beta_hat.
beta_hat <- inv(t(X)%*%X) %*% t(X)%*%y
t(beta_hat)
```

```
##            [,1]       [,2]        [,3]       [,4]       [,5]      [,6]
## lwage 0.4900248 0.08382038 0.003069741 0.01678847 -0.2854683 0.1257859
```

Variance-covariance matrix:

```r
# Get SSR and s^2
u_hat <- y - X%*%beta_hat
SSR <- t(u_hat)%*%u_hat
dof <- dim(X)[1] - dim(X)[2]
dof
```

```
## [1] 520
```

```r
s2 <- SSR/dof
s2 <- as.numeric(s2)
V <- s2*inv(t(X)%*%X)
format(V, scientific = F)
```

```
##                                                                             
## [1,] " 0.010223278756" "-0.000649263168" "-0.000072397812" " 0.000010857545"
## [2,] "-0.000649263168" " 0.000048622476" " 0.000003972480" "-0.000001740406"
## [3,] "-0.000072397812" " 0.000003972480" " 0.000002829222" "-0.000002323943"
## [4,] " 0.000010857545" "-0.000001740406" "-0.000002323943" " 0.000008734352"
## [5,] "-0.001000668587" " 0.000015066503" "-0.000004663198" " 0.000020500379"
## [6,] "-0.000143563900" "-0.000045233534" "-0.000019015165" "-0.000006357669"
##                                         
## [1,] "-0.001000668587" "-0.000143563900"
## [2,] " 0.000015066503" "-0.000045233534"
## [3,] "-0.000004663198" "-0.000019015165"
## [4,] " 0.000020500379" "-0.000006357669"
## [5,] " 0.001388688874" " 0.000198579772"
## [6,] " 0.000198579772" " 0.001598924090"
```

Standard errors:

```r
# se.beta_hat <- c(sqrt(V[1,1]),sqrt(V[2,2]),sqrt(V[3,3]),sqrt(V[4,4]),sqrt(V[5,5]),sqrt(V[6,6]))
# square root of the diagonal entries are the standard errors. 
se.beta_hat <- sqrt(diag(V))
se.beta_hat
```

```
## [1] 0.101110231 0.006972982 0.001682029 0.002955394 0.037265116 0.039986549
```
t-ratios:

```r
t.beta_hat <- beta_hat/se.beta_hat
t.beta_hat
```

```
##          lwage
## [1,]  4.846441
## [2,] 12.020737
## [3,]  1.825022
## [4,]  5.680621
## [5,] -7.660471
## [6,]  3.145705
```

Make a table displaying results from matrix algebra:

```r
results_mat <- cbind(beta_hat, se.beta_hat, t.beta_hat)
colnames(results_mat) <- c("estimate","se","t-ratio")
rownames(results_mat) <- c("intercept","educ","exper","tenure","female","married")
results_mat
```

```
##               estimate          se   t-ratio
## intercept  0.490024776 0.101110231  4.846441
## educ       0.083820379 0.006972982 12.020737
## exper      0.003069741 0.001682029  1.825022
## tenure     0.016788472 0.002955394  5.680621
## female    -0.285468326 0.037265116 -7.660471
## married    0.125785887 0.039986549  3.145705
```
Results from __lm()__ function:

```r
summary(lm(log(wage) ~ educ + exper + tenure + female + married, data = wage1)) 
```

```
## 
## Call:
## lm(formula = log(wage) ~ educ + exper + tenure + female + married, 
##     data = wage1)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -1.87254 -0.27256 -0.03779  0.25349  1.23666 
## 
## Coefficients:
##              Estimate Std. Error t value Pr(>|t|)    
## (Intercept)  0.490058   0.101108   4.847 1.66e-06 ***
## educ         0.083905   0.006973  12.033  < 2e-16 ***
## exper        0.003134   0.001682   1.863  0.06300 .  
## tenure       0.016867   0.002955   5.707 1.93e-08 ***
## female      -0.285530   0.037264  -7.662 9.00e-14 ***
## married      0.125739   0.039986   3.145  0.00176 ** 
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.4125 on 520 degrees of freedom
## Multiple R-squared:  0.4036,	Adjusted R-squared:  0.3979 
## F-statistic: 70.38 on 5 and 520 DF,  p-value: < 2.2e-16
```


<br>
<div class="tocify-extend-page" data-unique="tocify-extend-page" style="height: 0;"></div>

