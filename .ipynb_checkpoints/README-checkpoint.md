# Predicting Bikeshare Usage From Weather

Many cities have bicycle-sharing systems, or “bikeshares,” where individuals can check out bicycles for a short period of time before returning them to a docking station. Modeling bikeshare demand is important for providing reliable access to bikes, scheduling maintenance and repairs, and planning future bike infrastructure.

Our goal is to quantify the impact of weather and climate on bikeshare usage in Vancouver, British Columbia, and predict bikeshare usage and demand given specific daily weather conditions.

# Table of Contents

0. [Introduction](#Predicting-Bikeshare-Usage-From-Weather)
1. [Stakeholders and Key Performance Indicators](#Stakeholders-and-Key-Performance-Indicators)
2. [Data Collection and Cleaning](#Data-Collection-and-Cleaning)
3. [Exploratory Data Analysis](#Exploratory-Data-Analysis)
4. [Modeling Approach and Conclusions](#Modeling-Approach-and-Conclusions)
5. [Future Directions](#Future-Directions)
6. [Navigating the Repository](#Navigating-the-Repository)

## Stakeholders and Key Performance Indicators

1. Mobi (Vancouver's bike share): Mobi could use the predictions of our project given daily weather data to predict overall demand for bicycles in the city, and understand how many bicycles will be needed in circulation for a given day (or extended time period with predicted similar weather conditions). For example, this allows Mobi to know how many bicycles can be safely removed from use for maintenance purposes or otherwise.

2. The City of Vancouver: an understanding of bikeshare usage given weather conditions, and an analysis of changes in bikeshare ridership over time, can help Vancouver make decisions about bicycle infrastructure improvements, and time periods when it is safer to make changes to bicycle infrastructure.

Since we aimed to predict the number of bicycles used on a given day given its weather conditions, we used root mean squared error ("RMSE") as our performance metric. How we compared performance using RMSE is discussed further below.

## Data Collection and Cleaning

We collected data from three sources, all within the time window of January 2017 to May 2024:

1. Vancouver Bike Share, Inc., “Mobi”: Data concerning bike ridership in this time window. The relevant data provided by Mobi includes, for each bike ride, statistics about the bike ride timing and distance and temperature during the ride.

2. Environmental and Climate Change Canada (ECCC) database, the Government of Canada: Historical weather data from this time window, measured at the weather station at Vancouver International Airport, which takes more weather measurements than other weather stations in the Vancouver area (both in terms of frequency and measured quantities).

3. Timeanddate.com: The amount of sunlight (hours, minutes, seconds) in Vancouver, Canada for each day in our time window.

To clean the data, we first aggregated the data so it was on the same daily scale (Mobi provided data by the hour, and ECCC provided different datasets for daily or hourly weather), and then either remove or fill in missing values depending on the missing values. We filled in missing temperature values using linear interpolation from surrounding temperature values, and missing precipitation values from a combination of averaging approaches and deduction approaches from other non-missing values.

## Exploratory Data Analysis

The usage data can be viewed in two major ways: the first is the number of bike trips plotted against the date, and the second is the number of bike trips plotted against various weather features.

When viewing the data by date, as a time series, it is easy to see both seasonality and an increasing trend, with a major decline in 2020 looking like a "reset" of the trend, due to the COVID-19 pandemic, followed by faster recovery and growth after this period. This trend likely occurs due to an increase in base bikeshare demand. The seasonality component is explained quite well by daily maximum temperature.

We tried several ways of modeling the seasonality and trend. In the end, what worked best for us was a multiplicative time series model with the seasonality component explained best by daily maximum temperature, and a piecewise linear function capturing the trend, with pieces coming from before February 2020, between February 2020 and April 2020, and after April 2020. We removed this trend to make an "adjusted" number of trips, and aimed to model to predict this adjusted number.

To find features of importance, we plotted the (adjusted) number of trips against various weather features, looked at correlation matrices, and performed lasso regression. The features that remained of interest from these processes were daily maximum temperature (`max_temp`), difference between daily minimum and maximum temperatures (`temp_diff`), the amount of time between sunrise and sunset (`day_length`), daily total precipitation (`total_precip`), how much snow was on the ground (`snow_on_ground`), and a measure of how windy the day was (given by the maximum wind gust of the day) (`max_gust`).

## Modeling Approach and Conclusions

We used two models for our baseline. The first baseline model was $k$-Neighbors Regression on the day of the year, with $k=60$ (selected through cross-validation). A second model, something between a baseline model and a more informed model--and became our "model to beat"--was a linear regression on a subset of the features found to be important during exploratory data analysis.

For each model we tested we tuned either a hyperparameter or a subset of features through five-fold cross-validation.

The subset of features was selected via cross-validated best subsets regression, and turned out to be the daily maximum temperature, length of time between sunrise and sunset, an interaction term between these two features, difference between maximum and minimum daily temperature, total daily precipitation, and the maximum wind gust.

Thus the full list of models we tested were
* $k$-Neighbors Regression on the day of the year,
* Linear Regression on the best subset of features,
* $k$-Neighbors Regression on the features selected during EDA,
* Decision Tree Regression, and
* Random Forest Regression with XGBoost.

After hyperparameter tuning or feature selection, this list of models was
* $k$-Neighbors Regression on the day of the year with $k=60$,
* Linear Regression on the `max_temp`, `day_length`, an interaction term between these two, `temp_diff`, `total_precip`, and `max_gust`
* $k$-Neighbors Regression on `[max_temp, day_length, temp_diff, total_precip, snow_on_ground, max_gust]`,  with $k=16$ and the Manhattan metric,
* Decision Tree Regression on `[max_temp, total_precip]` (and some other hyperparameters)
* Random Forest Regression with XGBoost on `[max_temp, total_precip, day_length]`

Our performances were

| **Modes** | **Training RMSE** | **CV RMSE** | **Testing RMSE** |
| --- | --- | --- | --- |
| Regression | 495 | 457 | 731 |
| KNR on dates | 605 | 540 | 1010 |
| **KNR on weather features** | **420** | **410** | **677** |
| Decision tree regressor | 415 | 476 | 682 |
| XGBoost | 421 | 440 | 684 |

$k$-neighbors regression performed the best, with the decision tree models not far behind. Most of our error was concentrated during the summer months with peak usage, where daily usage is around 4000 bikes. This error also aligns with where our bikeshare trend adjustment errors were concentrated, and we found that our models predicted the "adjusted" number of trips (trips with trend removed) much better than the actual number of trips. These two facts suggest that all of our models would benefit from a more accurate trend removal that we carried out during exploratory data analysis.

## Future Directions

As mentioned above, this project could be improved by producing a more accurate trend model. We could also incorporate our hourly bike and weather data to improve accuracy, although we note that both the hourly bike and weather datasets will require much more cleaning. Similarly, we could have tried weighting certain data more heavily, such as bikeshare usage from docking stations with the most demand.

This project could also be extended to other cities with bikeshare programs and different climates; while a given model is likely only useful on its own city, insights from a project like this can be used to analyze and explain different cities' attitudes towards weather patterns and impact from different weather factors on bikeshare usage behaviors.

## Navigating the Repository

Our repository is organized into four main folders for the four main stages of our project:
1. The `data` folder contains all of the content for data collection.
  
Within this folder, `bike data` contains notebooks for downloading bike data and combining it into a single `.csv`; `weather_data` has the same but for weather and sunlight data as well as combination of bike and weather data. The cleaned data is produced in the preprocessing notebook.

The file `dataset_description.md` has a description of each feature extracted during data collection.

2. The `EDA` folder contains all of our work for exploratory data analysis.

Many of the notebooks and files in this notebook did not make it into our final report, such as three of the four attempts at de-trending the data, or some preliminary time series analysis. The content in this folder that was ultimately used is that in `EDA.ipynb`, `detrending_piecewise_tempseason_mult.ipynb`, and the files used for modeling were `bikeshare_[train OR test]_data_pcwsdetrend_mult.csv`. The other notebooks were part of the decision process but are not necessary for following the project.

3. The `models` folder contains our work on modeling.

Most of this work made it into our final report, with the exception of `Time-Series.ipynb` and `Tree_Based_Models_Add.ipynb`; the first turned out to decrease performance so it was removed from our final analysis, while the second was part of the decision process to use the multiplicative model for our time series.

4. The `final_report` folder contains our final report, and is a more in-depth summary of the entire project than this README.

One can simply view the notebook ``final_report.ipynb`` for a condensed version of the modeling approaches with some minor initial data visualization. This skips data collection, cleaning, and all exploratory data analysis (except for a shorter description of our de-trending approach) and skips straight to modeling and model performance comparisons. For more in-depth discussion about each model, and more visualizations, refer to the notebooks in `models`.