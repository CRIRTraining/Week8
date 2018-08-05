---
title: "Week 8"
output:
  html_document: default
  pdf_document: default
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```
Ok let us load the data first
```{r}
set.seed(123)
fideility = rnorm(100, 50, 2)
satSample = c(1:5)
satisfaction = sample(satSample, 100, replace = TRUE)
randomEffectsCorr = matrix(c(1,-.5,-.5, 1), ncol = 2)
library(semTools)
n = 100
randomEffects = mvrnonnorm(n, mu = c(30,30), Sigma = randomEffectsCorr, empirical = TRUE)
colnames(randomEffects) = c("PHQ9Post", "fideility")
genderSamp = c(1,0)
gender = as.factor(sample(genderSamp, 100, prob = c(.5, .5), replace = TRUE))
treatment = as.factor(sample(genderSamp, 100, prob = c(.5, .5), replace = TRUE))
datWeek8 = data.frame(satisfaction, gender, randomEffects)
write.csv(datWeek8, "datWeek8.csv", row.names = FALSE)
datWeek8 = read.csv("datWeek8.csv", header = TRUE)
head(datWeek8)
```
Let us run a multivariate regression as follows below 
```{r}
resultsWeek8 = lm(PHQ9Post ~ fideility + satisfaction + gender, data = datWeek8)
summary(resultsWeek8)
```
We can see that fidelity is negatively related (negative t-value) with PHQ9 posts scores.  With multiple variables, we want o interpret the adjusted r-squared, because it accounts for the lost degrees of freedom with the additional variables.  For a one-unit increase in fidelity there is about a -.5 decrease in PHQ9Post scores holding all other variables constant. 

Now we are going to learn a little more about multivariate linear regression and here are some of the assumptions:

Linear relationship = covariates are additive
Multivariate normality = normality among all variables together
Minimal multicollinearity = when variables are highly correlated
No auto-correlation = observations are independent of each other
homoscedasticity = residuals have equal variance across the regression line

For a linear relationship we assume that the model is additive:

y = b0+b1+b2+e 

Because the model is additive we get the nice interpretation of given a one-unit change we see an (insert number) change in the dependent variable holding all other variables constant. 

This model assumes no interaction effects.  If we assume interaction effects, we need to account for that in our model (b1*b2).  It also assumes no squares (b1^2) or things like that. 


Multivariate normality
Below are some tests to evaluate multivariate normality which means the relationship between each of the variables together is normal.  However, if you have variables that are not continuous then these tests won't be very helpful.  These tests are also sensitive to large samples sizes.  At least for reviewers, this assumption is not usually one people worry about as much.  

The qqplots can also help us understand multivariate normality and outliers as well.
```{r}
#install.packages("kableExtra")
#install.packages("knitr")
#install.packages("MVN")
library(knitr)
library(kableExtra)
library(MVN)
mvn(datWeek8, multivariatePlot = "qq")
```
Minimal multicollinearity

In linear regression, we can evaluate the VIFS, which are the variance inflation factors.  It basically runs a regression for each variable as the dependent variable and evaluates how much 
1/(1-R^2) so higher values of R^2 means that the higher the VIF.  A VIF of five is a general rule of thumb (e.g. R^2 of .8).  One way to reduce multicollinearity is to drop the related variables and see if the R^2 drops. 
```{r}
#install.packages(car)
library(car)
vif(resultsWeek8)
```
No autocorrelation basically means that observations are independent of each other.  The most common case of autocorrelation is when you have repeated measures. You can test for autocorrelation, but if you don't have repeated measures, it probably isn't necessary.
```{r}
library(lmtest)
dwtest(resultsWeek8, alternative = "two.sided")
```
Homoskedasticity is one that you might want to worry about.  We can review the residuals to better understand this assumption. 

Example of heteroskedasticity: https://www.google.com/imgres?imgurl=https://upload.wikimedia.org/wikipedia/commons/a/a5/Heteroscedasticity.png&imgrefurl=https://en.wikipedia.org/wiki/Heteroscedasticity&h=357&w=556&tbnid=VMvXLrt6sw2KkM:&q=example+of+heteroscedasticity&tbnh=135&tbnw=211&usg=AFrqEzcbfxmSYOX2PVk0w5iDNslAAu6nGA&vet=12ahUKEwiI8fOO-tXcAhXRpFkKHY1PC0sQ9QEwAHoECAQQBg..i&docid=0eE_8C4gH8Te9M&sa=X&ved=2ahUKEwiI8fOO-tXcAhXRpFkKHY1PC0sQ9QEwAHoECAQQBg
```{r}
plot(resultsWeek8)
```
