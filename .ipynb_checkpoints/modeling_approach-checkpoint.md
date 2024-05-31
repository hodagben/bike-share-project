# Modeling Approach

## Train-Test Split

The driving goal of our project is to create models that accurately predict the number of bikeshare trips taken on a given future date.  Our available data spans the years 2017 to present.  To simulate future data, we will train our models only on the first 6 years of data, reserving the final year for testing.

## Baseline Models

Plotting daily bike trips over time reveals clear annual seasonality. Since our response variable changes nonlinearly (but predictably) with respect to time, a kNN ('k nearest neighbors') model makes sense as a baseline.  This model takes a date as input and converts it to day of the year (December 31 is day 365).  It then finds the $k$ rows of training data whose days of the year are closest to this one.  Finally, it looks at the number of bike trips that occurred on those days and returns the average.  The parameter $k$ is selected through cross-validation.

Many of our predictors--notably temperature--exhibit the same seasonality found in daily bike trips.  This means that, while daily bikes trips vary seasonally, they may be moving linearly with respect to certain predictors.  Simple linear regression of bike trips on temperature will serve as our second baseline model.  This model has the advantage that it is easy to interpret and may provide more insight than kNN.

## Other modeling directions

We hope to construct at least one model that outperforms both of our baselines on test data.  A few directions of interest to our group are:
* Time series analysis
* Random forest regression or other tree-based models
* Fourier transform methods

We also have a significant amount of weather data that our modeling has not yet taken into account.  This alone will likely yield some improvement.
