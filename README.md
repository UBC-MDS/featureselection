
<!-- README.md is generated from README.Rmd. Please edit that file -->

# featureselection Package for R

Feature Selection with Machine Learning Models

<!-- badges: start -->

[![R build
status](https://github.com/UBC-MDS/feature-selection-r/workflows/R-CMD-check/badge.svg)](https://github.com/UBC-MDS/feature-selection-r/actions)
[![codecov](https://codecov.io/gh/UBC-MDS/feature-selection-r/branch/master/graph/badge.svg)](https://codecov.io/gh/UBC-MDS/feature-selection-r)
<!-- badges: end -->

## Overview:

If you have encountered a dataset with a myriad number of features, it
could be very difficult to work with them all. Some features may not
even be important or relevant and could even cause optimization bias.
One approach to this problem is to select a subset of these features for
your model. Feature selection will reduce complexity, reduce the time
when training an algorithm, and improve the accuracy of your model (if
we select them wisely). However, this is not a trivial task.

The **featureselection** package for `R` can help you with this task. It
is similar to its companion package [featureselection Package for
Python](https://devtools.r-lib.org).

## Features

In this package, four functions are included for feature selection:

  - `forward_selection` - Function that use the forward selection
    algorithm to choose the number of features in a model. This
    iterative algorithm starts as an empty model, and add the feature
    with the best improvement based on the accuracy of the model. The
    process then is iteratively repeated selecting the features with the
    best improvement in accuracy. This procedure stops when the
    remaining features no longer enhance the accuracy of the model.

  - `recursive_feature_elimination` - Iteratively fit and score an
    estimator for greedy feature elimination. The model initially
    considers all features with the goal of discovering the worst
    performing feature which is then removed from the dataset. This
    process is repeated until the desired number of features are
    attained.

  - `variance_thresholding` - Perform simmulated annealing to select
    features: randomly choose a set of features and determine model
    performance. Then slightly modify the chosen features randomly and
    test to see if the modified feature list has improved model
    performance. If there is improvement, the newer model is kept, if
    not, a test is performed to determine if the worse model is still
    kept based on a acceptance probability that decreases as iterations
    continue and how worse the newer model performs. The process is
    repeated for a set number of iterations.

  - `simulated_annealing` - Perform simmulated annealing to select
    features: randomly choose a set of features and determine model
    performance. Then slightly modify the chosen features randomly and
    test to see if the modified feature list has improved model
    performance. If there is improvement, the newer model is kept, if
    not, a test is performed to determine if the worse model is still
    kept based on a acceptance probability that decreases as iterations
    continue and how worse the newer model performs. The process is
    repeated for a set number of iterations.

## Existing Ecosystems:

Some of the above features already exsist within the R ecosystem but are
provided for feature parity with [Python
edition](https://github.com/UBC-MDS/feature-selection-python) of this
package.

  - [Forward
    Selection](https://www.rdocumentation.org/packages/MXM/versions/0.9.4/topics/Forward%20selection)

  - [Recursive Feature
    Elimination](https://www.rdocumentation.org/packages/caret/versions/6.0-85/topics/rfe)

  - Variance Threshold (None)

  - Simulated Annealing (None)

## Installation:

Make sure you have the `devtools` package installed. You can install it
as follows.

``` r
#Install development version from Github
install.packages("devtools")
```

Then, install the feature selection package.

``` r
devtools::install_github("UBC-MDS/feature-selection-r")
```

## Dependencies

  - [R 3.6](https://www.r-project.org/)
  - [dyplr 0.8.0](https://dplyr.tidyverse.org/)
  - [purrr 0.3.0](https://purrr.tidyverse.org/)
  - [stats 3.6.0](https://stat.ethz.ch/R-manual/R-devel/library/stats/html/00Index.html)

## Usage

To guide you with an example of how to use this package, we will use the
[Friedman
dataset](https://www.rdocumentation.org/packages/tgp/versions/2.4-14/topics/friedman.1.data).

Load dataset:

``` r
data <- dplyr::select(tgp::friedman.1.data(), -Ytrue)
X <- dplyr::select(data, -Y)
y <- dplyr::select(data, Y)
```

Use of feature selection functions:

### forward\_selection

``` r
# Create a 'scorer' that accepts a dataset
# and returns the Mean Squared Error.
custom_scorer <- function(data){
  model <- lm(Y ~ ., data)
  return(mean(model$residuals^2))
}

# use function
featureselection::forward_selection(custom_scorer, X, y, 3, 7)
[1] 4 2 1 5
```

### recursive\_feature\_elimination

``` r
# Create a custom 'scorer' that accepts a dataset and returns
# the name of the column with the lowest coefficient weight.
custom_scorer <- function(data){
  model <- lm(Y ~ ., data)
  names(which.min(model$coefficients[-1]))[[1]]
}

# use function
featureselection::recursive_feature_elimination(custom_scorer, X, y, 4)
[1] "X1" "X2" "X4" "X5" "Y"
```

### simulated\_annealing

``` r
# Create a 'scorer' that accepts a dataset
# and returns the Mean Squared Error.
custom_scorer <- function(data){
  model <- lm(Y ~ ., data)
  return(mean(model$residuals^2))
}

# use function
featureselection::simulated_annealing(custom_scorer, X, y)
[1]  1  2  3  4  5  7  9 10
```

### variance\_threshold\_select

*Note: This function does not use the Friedman dataset.*

    # sample data to test variance
    data <- data.frame(x1=c(1,2,3,4,5), x2=c(0,0,0,0,0), x3=c(1,1,1,1,1))
    
    # use function
    featureselection::variance_threshold_select(data)
    [1] 1

## Documentation

The official documentation is hosted here:
<https://ubc-mds.github.io/feature-selection-r>

## Credits

This package was created with the assistance of the following packages:
[devtools](https://devtools.r-lib.org),
[usethis](https://github.com/r-lib/usethis),
[pkgdown](https://pkgdown.r-lib.org)
