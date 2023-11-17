# Power Outage Trends
Author: Han Hoang

## Project Overview
This data science project explores the caused-by-severe-weather power outage trends, answering curiorsity of the time in the year when power outages that are caused by severe weather are more likely to occur. This project is completed as an assignment for DSC 80 at UCSD.

## Background Information and Introduction
The dataset is sourced from Purdue University's Laboratory for Advancing Sustainable Critical Infrastructure. It includes data of the major outages occurred in different states of the United States of America like geospatial data of power outages, causes of power outages and other power outages characteristics, land-use characteristics, power consumption level as well as economic conditions of the states included in the dataset. This report puts a focus on analyzing the trends in the occurring time of the power outages that happened due to severe weather causes to draw a hopefully convincing conclusion to the question of whether number of power outages due to severe weather during the summer is larger than the number of power outages due to severe weather during the winter. The conclusion to this question can provide valuable insights to compare the degree of extreme bad whether effect has on power outages in summer versus winter.

The dataset has 1534 observations and 53 variables, but the relevant variables to answer our interested question in the dataset are OUTAGE.START (the full timestamp at which the outage occured), MONTH (the month in which the outage occured), and YEAR (the year in which the outage occured).

## Cleaning and EDA

### Data Cleaning
The analysis is done on a dataframe which is made by having Pandas directly read in an excel file downloaded straight from Purdue's website. The read-in dataframe still had junky row and column that needs to be removed, index that needed to be reset, essentially reformatting the dataset into a tidy-data format that is easy to work with. The outage start and restoration time also got combined with their timestamp to a singular, more comprehensive time unit for each.

After the dataset is ready to use, the dataframe will still need to be subset to contain only the power outages caused by severe weather that we are interested in. The season column is also engineered using the MONTH column data and a dictionary of seasonal-monthly mapping.

The first 5 rows of the dataframe is as below:
<iframe src="assets/severe_weather_outages.html" width=800 height=600 frameBorder=0></iframe>

### Univariate Analysis
This bar plot shows the trend of outages by month

*Fun fact:* You can expect about 1.3 outages per month on average according to this data!

<iframe src="assets/outages_by_month.html" width=800 height=600 frameBorder=0></iframe>

### Bivariate Analysis
This line plot shows another perspective of time trend of outages, but by year!

<iframe src="assets/outages_by_year.html" width=800 height=600 frameBorder=0></iframe>

### Interesting Aggregates
This horizontal bar plot shows the trend of outages by season. You can tell summer power outages is pretty bad!

<iframe src="assets/outages_by_season.html" width=800 height=600 frameBorder=0></iframe>

## Assessment of Missingness

## Hypothesis Testing

- Null hypothesis: the number of outages due to severe weather during the summer is the same as the number of outages due to severe weather during the winter
- Alternative hypothesis: the number of outages due to severe weather during the summer is larger than the number of outages due to severe weather during the winter
- Test statistic: number of outages during the summer - number of outages during the winter
- Significance level = 0.05


<iframe src="assets/hypothesis_test.html" width=800 height=600 frameBorder=0></iframe>

Resulting p-value: 0.0

**Conclusion**: Since the p-value cutoff is 0.05, the null hypothesis is rejected and we can conclude that the number of outages due to severe weather during the summer is larger than the number of outages due to severe weather during the winter not due to chance alone