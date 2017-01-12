---
title: Notes on PCA
excerpt: Principal Components Analysis using the `caret` package

---

# What's PCA doing?

It's a tool to search for the principal components that explain variance of data.
Statistically the most efficienct model is use all of the data and train the engine to resample subsets of it using cross-validation.

Since predictors with high reciprocal correlation are not explaining any variance of data, we may want to remove
* create correlation matrix
* identify which pair has the largest correlation reciprocal
* find average correlation between each of them and all other variables
* flag these for removal
* remove those two predictors

High correlations can be discovered with 

``` r
which(cor(<dataframe>) > threshold)
```

this will return a logical vector with indexes of correlations higher than threshold.

Or, better:

``` r
M <- abs(cor(dataframe))             # save all absolute correlations in M
diag(M) <- 0                         # set the diagonal to zero
which(M > threshold, arr.ind=TRUE)   # return a table with highly correlated
```

then combine those variables into something more meaningful

# Modeling


First we build the model by selecting predictors, and this is done with the preProcess function. We may set a threshold to set the max of variance explained to build the model

``` r
preproc.model <- preProcess(datasetwithonlypredictors, method="pca", thresh=0.8, pcaComp=2)
```

Then we predict
 
``` r
trainPC <- predict(preproc.model, datasetwithonlypredictors)
```

Then we fit the model

``` r
modelFit <- train(outcomevariable ~., method="glm", data=trainPC)
```

We then predict on the test, and verify the accuracy

``` r
testPC <- predict(preproc.model, testingdatasetonlypredictors)
confusionMatrix <- (outcomevariableintesting, predict(modelFit, testPC)
```


