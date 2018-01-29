---
title: "Nice Ride Project Data Wrangle Explanation"
author: "Tony Tushar Jr"
date: "1/12/2018"
output: html_document
---

The Nice Ride MN bikeshare datasets required little data wrangling other than renaming a few columns based on preference, formatting the date and time columns to match with the weather data, and a full join to match the dock station data with the trip history data. Dplyr and Tidyr packages were used to accomplish this.

The 2017 hourly weather dataset was more challenging for various reasons outlined in question/answer format below:

1. **Daylight savings time was on Sunday, March 12 and Sunday, November 5 for 2017. What to do about this?** Disregard DST as it only plays into consideration on the final day of the bike season which was Sunday, November 5th, 2017.

1. **What to do about blank precipitation observations and non-numerical entries?** From the LCD Documentation: *Blank/null = no precipitation was observed/reported for the hour ending at that date/time. T = indicates trace amount of precipitation*. Treat blank and "T" observations as “0” precipitation in final dataset.

2. **How to handle hourly weather observations that are blank?** Assign the term "Clear" to all blank weather type observations.

3. **How to handle discrepancies between auto and manual weather type observations?** Most auto and manual weather type observations share the same base character code for a weather type but differ in the secondary, numerical code. In observation of the numerical code the difference is often a redundancy of the same level of a weather type (example "02" and “52” both mean “moderate”). Default to manual observations when available.

4. **How to handle precipitation observations labeled as suspect?** There are 64 levels for precipitation observations, of which nine include an "s" after the numerical value which signals these observations as suspect. Being that nine levels are not the majority of 64, we will consolidate these levels to their numerical equivalents to remove the suspect label (example “0.02s” joins with “0.02”).

5. **How categorical should we make the weather type observation levels?** The raw dataset has 65 levels for weather type observations. Initially, these levels were recategorized down to six (Drizzle, Rain, Thunderstorm, Snow, Fog/Haze, and Clear), however, given there are over 460,000 bike rides for the season, 12 levels seems more appropriate. This was accomplished by breaking down most categories to "light", “moderate”, and “heavy” types. “Fog” and “Haze” are paired together as they often appear in the same auto and manual weather type observations, though this could be scrutinized and approached differently perhaps if the dataset was multi-year.

6. **What should the approach be for joining two time series datasets into one?** Multi-time series datasets can be complex to work with. For simplicity and ease without compromise of the data, all ride and weather observations have been rounded to the nearest hour each day. This will not affect the integrity of the ride share dataset, however,a consequential effect must be addressed for the hourly weather data when multiple observations are recorded in a given hour. **Addressing effects on the weather dataset from rounding observations to the nearest hour:**

    1. **Weather Type** - Apply mode of characters function to the weather type observations, therefore, configuring to the most frequently observed weather type level within a given hour.

    2. **Temperature(F), Humidity, and Wind** - Take the mean for each of the variables within a given hour.

    3. **Precipitation** - Aside from defining blank or "T" observations as “0”, take the max value from the observations within a given hour, as the precipitation value is cumulative for each given hour when observed multiple times.

7. **Joining weather and bikeshare data caused the creation of 61 NA’s?** The last full join to bring the bikeshare and weather data together seemed to create 61 NA values at the end of the dataset. This appears to be a bug and na.omit was applied to remove them.

