---
title: R Notes
excerpt: My notes on using R

---

# Generic R commands

List of commands and notes that I found hard to remember at the beginning.

* Get arguments for a function: `args("<function>")`
* Load from url: `load(url(<url>))`
* Get symbol type: `class(<symbol>)`
* Generate frequency tables: `table(<variable>)`
* r objects attributes: names, dimensions, class, length, other user-defined attributes, metadata.
* What is a list? A special type of vector that can contain elements of different classes.
* What is a matrix? It's a vector with a dimension attribute, that is itself an integer vector of length 2 (nrow, ncol).
* Create a matrix with x rows and y columns: `matrix(nrow=x,ncol=y)`
* What's factor? A vector to represent categorical data, ordered or unordered.
* How can you think about a factor? As an integer vector where every integer has a label.
* Set order for factor levels: `factor(<vector>, levels=c(<levels>))`
* Reading in R code files (inverse of dump): `source`
* Reading in R code files (inverse of dput): `dget`
* Reading in saved workspace: `load`
* Reading single R objects in binary form: `unserialize`
* How to pass data objects around in R: using `dput`, and then `dget` 
* What is similar to dget but can be used in more objects: `dump` (write) and `source` (read) 


# Read data and manage files

* Open connection to a file: `file`
* Open connection to a web page: `url`
* Open a connection to file compressed with gzip or bzip2: `gzfile` or: `bzfile`
* Check if directory exists: `file.exist(<directory name>)`
* Create directory if it doesn't exist: `dir.create(<dir>)`
* Download file from web: `download.file(url, destfile, method)`
* List files in a directory: `list.files(<dir>)`
* Read flat files: `read.table(<file>, sep = "<separator>", header=TRUE)`
* Read from excel file use xlsx package, command: `read.xlsx(<file>, sheetIndex=1, header=TRUE)`

## Read xlm / html

* Get the root node in xml rootNode: `xmlRoot(doc)`
* Top level node: `/node`
* Node at any level: `//node`
* Node with attribute name: `node[@attr-name]`
* Node with attribute name attr-name='bob': `node[@attr-name='bob']`
* Import json with pretty formatting: `toJSON(<data>, pretty=TRUE)`
* Read xml doc: `xmlTreeParse(<doc>, useInternal=TRUE)`
* extract or set the contents of a leaf XML node: `xmlValue(<node>)`
* Get value of an xmlnode: `xmlValue`

# Using lists

* Extract elements of a list or data frame: `[[`
* Used to extract elements of a list or data frame by name: `$` 
* Different between single bracket and double bracket operator when subsetting list: `[` returns the object at that position, `[[` returns only the value in that position 
* The `[[` operator can be used with computed indices; $ can only be used with literal names 
* The `[[` can take an integer sequence 

# Data analysis workflow

Raw data -> processing script -> tidy data -> data analysis -> data communication 

Four things required to process data: the raw data, a tidy data set, a code book describing each variable and its values in the tidy data set, recipe on how went from raw data.

Vectorize as many operations as possible to make code more efficient, concise and easier to read.

**Raw data** is the original source of data, hard to use for data analyses; they may only need to be processed only once 

**Processed data** is data ready for analysis, processing can include merging, subsetting..; there may be a standard for processing, and all steps should be recorded 

**Tidy data**: each variable in one column with human-readable name; each observation in a different row; one table, in different files, for each "kind" of variable, all with a column to link one to others; include a row on top with variable names.

The **code book** must contain info about the variables (including units) in the dataset not contained in the tidy data; info on summary choices made; info on the experimental study design made and how data have been collected; preferred format is text.

Instruction list on how data have been processed should be a script or clear and verbose pseudocode.

# Lists and flow control

* return another list with a function applied to each element: `lapply(list, function)`
* loop over a list and evaluate a function over each element: `lapply`
* what does lapply return, regardless of the input: a list
* same as lapply but with output simplified: `sapply`
* create list of integers same length of a vector: `seq_along(<vector>)`
* for loop types: `for (i in 1:4)`, `for (i in seq_along(<vector>))`, `for (letter in x)`
* while loop: `while (condition) {` .. remember to stop the condition.. 
* start infinite loop with repeat: `repeat {.. break.. `
* skip an iteration in for loop: `for (..){ if(..) { next `
* use loop control in interactive mode: use `*apply` function
* create function: `x <- function (x) {.. `
* specify default values in function: `x <- function (x = 10) {... `
* what is returned from a function? the last expression written 
* what class is a function? is an object with class function 
* what are formal arguments of a function? arguments included in the function definition 
* what is lazy evaluation? arguments of a function are evaluated only if needed 
* indicate variable number of arguments passed on to other functions: `x <- function(x,y,...) `
* where are variables searched: `global env, packages, down to base`
* where are searched free variables? values of free variables are searched for in the environment in which the function was defined 
* what is a function + an environment? a closure, or function closure 
* search for a value of free variable? current env --> parent env --> top-level env (usually the global) --> event. in the package --> error 
* in lexical scoping where is looked up the value of a variable? in a function in the environment in which the function was defined 
* in dynamic scoping where the value of a variable is looked up? in the environment in which the function was called (calling environment) 
* format string to have 3 characters even with 1 digit: `%03d`
* round number n to x decimals: `round(n, digits=x)`
* set seed for random numbers generation: `set.seed(<integer>)`

# Tables and data frames

* remove column from data frame set to null: `df$column <- NULL`
* calcolate subtotals aggregating fields in data frame: `aggregate(<obj>, list(fields to aggregate), FUN)`
* generate sample from x to y, no repeat: `sample(x:y, repeat=FALSE)`
* subset from x only records where variable a>3: `x[a > 3,]`
* sort var a from dataframe x: `sort(x$a)`
* order s for var hp, decreasing: `s[order(s$hp,decreasing=T),]`
* add new variable a containing vector b to dataframe x: `x$a <- b`
* have quantile distribution from a variable a in df x: `quantile(x$a)`
* generate frequency table of var a in df x: `table(x$a)`
* generate two dimensional table: `table(x$a, x$b)`
* check if any missing value in var a df x: `any(is.na(x$a))`
* count missing values var a df x: `sum(is.na(x$a))`
* cross tab display frequency, row var a and col var b from dfx: `xtabs(Freq ~ a + b, data=x)`
* get size of object: `object.size(<obj>)`
* print size of object in Mb: `print(object.size(<obj>), units="Mb")`
* create new TRUE/FALSE variable if a condition is satisfied: `x$a = condition` example `x$b %in% c("pre","pro")`
* create new variable with ifelse: `x$a = ifelse(x$b < 0, TRUE, FALSE)`
* create tall skinny dataset putting some vars in row instead of column: `melt(dataframe, id=c(<variable that stay in column>), measure.vars=c(<variables to put in row>))`
* apply a function to each values v in groups of values g: `tapply(x$v, x$g, FUN)`


# Analytics

1. graphics show comparisons 
2. graphics show causality, mechanism, explanation, systematic structure 
3. graphics show multivariate data 
4. graphics integrate evidence 
5. graphics describe and document the evidence with appropriate labels, scales, sources 
6. graphics content is king 

* We use graph in data analysis to understand data properties, to find patterns, to suggest modeling strategies, to "debug" analyses, to communicate results
* Characteristics of exploratory graphs: made quickly, made in large number, goal is personal understanding, axes/legends are generally cleaned up, color/sizes are primarily used for information
* **Exploratory plots** are "quick and dirty", let you summarize the data (usually graphically) and highly any broad features, explore basic questions and hypotheses, suggest modeling strategies for the next steps
* Empirical Variance: sum of differencies between observations and observations mean, and divided by sample size decreased by one
* "Centering" random variables: subtract the sample mean from the data points 
* "Scaling" the data: divide observations by the empirical standard deviation 
* Empirical standard deviation: square root of the empirical variance 
* "Normalizing" the data: centering, then scaling. Subtract the empirical mean, and then divide by empirical standard deviation
* Empirical covariance: sum of products between differencies of the two variables with the two empirical means, then divide by sample size decreased by one 
* Correlation: divide covariance with product of the two standard deviations
* Least square is the physical center of mass of the histrogram.
* Method of least squares: let Y be the value that minimize the sum of differencies squared between Y and population mean
* What's the value that minimize the least squares value? the sample mean
* Regression through the origin? find the slope of the line that pass the origin and minimize the least squares
* How to pass from the origin? subtract the mean from the values, so the origin is the mean of them
* If we only look at horizontal lines, the least squares estimate of the intercept of that line is the average of the outcomes.
* If we only look at lines through the origin, we get the estimated slope is the cross product of the X and Ys divided by the cross product of the Xs with themselves. 
* The slope of the regression line with X as the outcome and Y as the predictor is `Cor(Y, X)Sd(X)/Sd(Y)`

# Probability

* Probability formula: # of possibilities that meet my condition / # of equally likely possibilities 
* Probability is simply how likely something is to happen 
* For any two events, the probability that at least one occurs is the sum of their probabilities minus their intersection.
* If an event `A` implies the occurrence of event `B`,then the probability of `A` occurring is less than the probability that `B` occurs.
* The cumulative distribution function (CDF) of a random variable, `x`, returns the probability that the random variable is less than or equal to the value x.
* The survival function of a random variable `X` is defined as the probability that the random variable is greater than the value `x`.
* A probability mass function evaluated at a value corresponds to the probability that a random variable takes that value.
* A probability density function (pdf), is a function associated with a continuous random variable.

# Machine Learning

* **Supervised learning** when we have a set of variables and an outcome, and we try to predict it
* **Unsupervised learning** when we have a set of variables and we try to predict how these relation to each other, if we can subgroup or visualize in a meaningful way


# Decision Trees

* Split the universe in two parts, then again --> **Recursive partitioning**
* Regions --> *terminal nodes*, or leaves
* *top down*: start from the top of the tree (all observations in a single region)
* *greedy*: at each step we do the best split, rather than look ahead and think a split that will be convenient in the future
* trees will continue to be very complex: we then prune it to avoid overfitting by CV till the error is good
* for classification, the most common class is taken
* classification error rate: fraction of the training observation that doesn't belong to the common class
