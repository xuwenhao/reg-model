MPG of Different Transimissions
========================================================

### Brief

#### Exploration Analysis

Take a look at the data first


```r
data(mtcars)
head(mtcars, n = 3)
```

```
##                mpg cyl disp  hp drat    wt  qsec vs am gear carb
## Mazda RX4     21.0   6  160 110 3.90 2.620 16.46  0  1    4    4
## Mazda RX4 Wag 21.0   6  160 110 3.90 2.875 17.02  0  1    4    4
## Datsun 710    22.8   4  108  93 3.85 2.320 18.61  1  1    4    1
```

```r
mtcars[, c("cyl", "vs", "am", "gear", "carb")] <- lapply(mtcars[, c("cyl", "vs", 
    "am", "gear", "carb")], factor)
```


The manual transimission(am=1) is more fuel-efficient by box plot(See Apprendix. Simple Box Plot), so we fit transmissiob with the mpg in a linear model.

```r
fit <- lm(mpg ~ am, data = mtcars)
fit$coefficients
```

```
## (Intercept)         am1 
##      17.147       7.245
```

By average manual car could drive 7.2449 mpg more.
But by residual plot (See Apprendix. Residual Plot with Only Tranmission factor) it distributed all in left and right side, which makes the prediction didn't looks good.
 
Pairs plot(See Apprendix. Pairs Plot) told us most of the factor looks have some kind relationship with the mpg. 

#### Model Selection

So we do a both direction(backward/forward) search of the regression model.

```r
slm <- step(lm(data = mtcars, mpg ~ .), direction = "both")
summary(slm)$coeff
```


We found cyl(cylinder), hp(horsepower), wt(weight), and am(transmission) are the factors used in fitting the model, only hp and wt has a P value less than 0.05.
Then only use hp and wt in the model and check if am could have enough confidence in model.

```r
modelX <- lm(mpg ~ hp + wt, data = mtcars)
modelUpdated <- update(modelX, mpg ~ hp + wt + am, data = mtcars)
diffA <- anova(modelX, modelUpdated)
```


P value 0.1413 told us to reject the hypothesis, the transmission factor didn't help in model.

But there is a strong relationship between hp/wt with tranmission.(See Apprendix. Horsepower ~ Transmission and Weight ~ Transmission ), and some outliers in the hp ~ am interaction.

Then we fit a model consider the interaction between weight and transmission.

```r
finalModel <- lm(mpg ~ wt + hp + wt * am + am, data = mtcars)
diffB <- anova(modelX, finalModel)
summary(finalModel)$coeff
```

```
##             Estimate Std. Error t value  Pr(>|t|)
## (Intercept) 30.94733   2.723411  11.363 8.547e-12
## wt          -2.51559   0.844497  -2.979 6.052e-03
## hp          -0.02695   0.009796  -2.751 1.048e-02
## am1         11.55481   4.023277   2.872 7.845e-03
## wt:am1      -3.57791   1.442796  -2.480 1.968e-02
```


P value of all the factors are less than 0.05 in the final model. And p value to add new feature is 0.0217 told us new features are valuable.
The manual transmission is more fuel-efficient. In average, it drive 11.5548 longer if the weight and horsepower of the car is constant.

The redisual plot(See Apprendix. Final Model Residual) of the final model is more like normal distributed now.

### Executive Summary

* Based on my analysis and regression of the data of 1974 Motor Trend magazine, the manual transmission will be better for MPG.
* If we consider this factor independently, it could drive 7.2449 miles more per galon.
* This factor has an interaction with the weight of the car, most of the auto transmission cars are weighter or have more horsepower.
* If we consider the interaction between the weight and the tranmission of the car. We could still find manaul transmission car could drive  11.5548 miles more per galon.
* The standard error of the mpg of manualy transmission is 4.0233 and the p value of it is 0.0078.

### Apprendix

##### Simple Box Plot

![plot of chunk unnamed-chunk-6](figure/unnamed-chunk-6.png) 


##### Residual Plot with Only Tranmission factor

![plot of chunk unnamed-chunk-7](figure/unnamed-chunk-7.png) 


##### Pairs Plot

![plot of chunk unnamed-chunk-8](figure/unnamed-chunk-8.png) 


##### Horsepower ~ Transmission and Weight ~ Transmission

![plot of chunk unnamed-chunk-9](figure/unnamed-chunk-9.png) 


##### Final Model Residual Plot

![plot of chunk unnamed-chunk-10](figure/unnamed-chunk-10.png) 



