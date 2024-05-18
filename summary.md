# Project Goals
Many cities have bicycle-sharing systems, or “bikeshares,” where individuals can check out bicycles for a short period of time before returning them to a docking station. Our goal is to quantify the impact of weather and climate on bikeshare usage in Vancouver, British Columbia, and predict bikeshare usage and demand given specific weather conditions. We aim to accomplish this goal on both hourly and daily scales.

# Description of Dataset
1. Vancouver Bike Share, Inc., “Mobi”: Data concerning bike ridership in this time window. The relevant data provided by Mobi includes, for each bike ride, the
  * Departure and return times (precision to the hour’s ‘floor’),
  * Distance of the ride (m),
  * Duration of the ride (sec),
  * Departure and return temperatures (in degrees C),
  * Whether or not the ride was on an electric bike (“e-bike”)


2. Environmental and Climate Change Canada (ECCC) database, the Government of Canada: Historical weather data from this time window, measured at the weather station at Vancouver International Airport, which takes more weather measurements than other weather stations in the Vancouver area. The measurements included in each observation from the data we examine
  * The date and hour,
  * Temperature (degrees C),
  * Dew point temperature (degrees C),
  * Relative humidity percentage, which is the percentage of water vapor in the air present relative to the maximum amount possible,
  * The direction of the maximum wind gust (in tens of degrees, measured from true north), recorded only if this gust exceeds 29 km/hr
  * The wind speed of the maximum wind gust (km/hr), recorded only if exceeding 29 km/hr,
  * Visibility (km),
  * Wind chill temperature (degrees C), an index combining temperature and wind velocity to describe how cold the weather feels to the average person,
  * The atmospheric pressure (in kPa) at the weather station’s elevation,
  * A description of the weather; e.g., “Clear,” “Cloudy,” “Rain”, taken every three hours,
  * Minimum, maximum, and mean temperature of that day (degrees C),
  * Heating degree days (degrees C) of that day, which is the number of degrees the day’s mean temperature is below 18 degrees Celsius,
  * Cooling degrees day (degrees C) of that day, which is the number of degrees the day’s mean temperature is above 18 degrees Celsius,
  * The total rain (mm) in that day,
  * The total snow (cm) in that day,
  * The total precipitation (mm) in that day,
  * The amount of snow on the ground that day (cm),
  * The speed of the maximum gust of wind for that day (km/hr), recorded only if this gust exceeds 31 km/hr
  * For further information on the definition of these weather and climate terms, see [ECCC's Glossary](https://climate.weather.gc.ca/glossary_e.html).


3. Timeanddate.com: The amount of sunlight (hours, minutes, seconds) in Vancouver, Canada for each day in our time window.


# Stakeholders
Our project’s stakeholders are primarily Mobi and secondarily the City of Vancouver. The results of this project are most relevant to Mobi, who can use the predictions of our project given daily weather data to predict overall demand for bicycles in the city, and understand how many bicycles will be needed in circulation for a given day (or extended time period with predicted similar weather conditions). For example, this allows Mobi to know how many bicycles can be safely removed from use for maintenance purposes or otherwise.

The City of Vancouver is our secondary stakeholder since an understanding of bikeshare usage given weather conditions, and an analysis of changes in bikeshare ridership over time, can help Vancouver make decisions about bicycle infrastructure improvements, and time periods when it is safer to make changes to bicycle infrastructure improvements.

To a smaller extent, the actual Mobi customers are also a stakeholder in this project because a knowledge of the bikeshare demand given current weather conditions can help users plan how much extra time they may or may not need to find a bicycle to use based on the overall demand for bicycles at that time.


# Key Performance Indicators

The success of this project hinges on the quality of the models we produce.  Our models will be evaluated based on two key indicators:

1. Accuracy:  The performance of the models on test data.  Our models should accurately predict bikeshare usage over a time interval (day or hour), given relevant weather and climate information.  Standard metrics such as mean squared error will be used to assess accuracy.

2. Interpretability:  Our models should offer insight into the relationship between bikeshare usage and weather/climate.  Ideally, model coefficients and parameters should have simple, intuitive interpretations (e.g. as rates of change).
