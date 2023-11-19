# An Investigation on The Degree of Extremeness Severe Weather Affects Power Outages
Author: Han Hoang

## Project Overview
This data science project explores the caused-by-severe-weather power outage trends, answering curiorsity of the time in the year when power outages that are caused by severe weather are more likely to occur. This project is completed as an assignment for DSC 80 at UCSD.

## Background Information and Introduction
The dataset is sourced from [Purdue University's Laboratory for Advancing Sustainable Critical Infrastructure](https://engineering.purdue.edu/LASCI/research-data/outages). It includes data of the major outages occurred in different states of the United States of America like geospatial data of power outages, causes of power outages and other power outages characteristics, land-use characteristics, power consumption level as well as economic conditions of the states included in the dataset. This report puts a focus on analyzing the trends in the occurring time of the power outages that happened due to severe weather causes to draw a hopefully convincing conclusion to the question of **whether number of power outages due to severe weather during the summer is larger than the number of power outages due to severe weather during the winter**. The conclusion to this question can provide valuable insights to compare the degree of extreme bad whether effect has on power outages in summer versus winter.

The original dataset has 1534 observations and 53 variables, of which there are 3 we are interested in for our analysis: 

| Column               | Description                                                                                                                                    |
|----------------------|------------------------------------------------------------------------------------------------------------------------------------------------|
| `OUTAGE.START.DATE`  | The date of which the power outages occurred                                                                                                   |
| `OUTAGE.START.TIME`  | The timestamp of which the power outages occurred                                                                                              |
| `MONTH`              | The month in which the power outages occurred                                                                                                  |
| `YEAR`               | The year in which the power outages occurred                                                                                                   |
| `CUSTOMERS.AFFECTED` | The count of customers got affected by the power outages                                                                                       |
| `HURRICANE.NAMES`    | The name of the hurricanes associated with the power outages due to severe weather (this column is needed for our missingness dependency test) |

## Cleaning and EDA

### Data Cleaning
The analysis is done on a dataframe which is made by having Pandas directly read in an excel file downloaded straight from Purdue's website. The read-in dataframe still had junky row and column that needs to be removed, index that needed to be reset, essentially reformatting the dataset into a tidy-data format that is easy to work with. The outage start and restoration time also got combined with their timestamp to a singular, more comprehensive time unit for each, and got renamed `OUTAGE.START` and `OUTAGE.RESTORATION` respectively.

After the dataset is ready to use, the dataframe is subsetted to contain only the power outages caused by severe weather that we are interested in. The season column is also created from the extraction of the month in the data and a dictionary of seasonal-monthly mapping. 

Finally, only the following relevant columns for analysis are kept in the dataset and others are dropped for a more concise dataframe: `OUTAGE.START`, `YEAR`, `MONTH`, `SEASON`, `CUSTOMERS.AFFECTED`, and `HURRICANE.NAMES`.

These cleanings will result in a dataframe whose head() (a.k.a the first 5 rows) is shown below:

| OUTAGE.START        | SEASON   | MONTH   |   YEAR |   CUSTOMERS.AFFECTED |   HURRICANE.NAMES |
|:--------------------|:---------|:--------|-------:|---------------------:|------------------:|
| 2011-05-29 18:30:00 | Spring   | May     |   2011 |               113000 |               nan |
| 2005-09-22 11:00:00 | Fall     | Sep     |   2005 |                53000 |               nan |
| 2003-11-12 17:00:00 | Fall     | Nov     |   2003 |               245000 |               nan |
| 2016-02-19 22:00:00 | Winter   | Feb     |   2016 |               145314 |               nan |
| 2007-07-19 15:00:00 | Summer   | Jul     |   2007 |                60000 |               nan |

### Univariate Analysis
We first want to see the trend of outages by month. From this histogram, power outages happen the least in March, following by November, and the highest power outage rates are in June and July, which makes sense since the weather in March and November tends to be the least severe throughout the year, while it starts to getting hot in June and everyone has an AC on with occassional wildfire happens here and there due to the hot weather.

*Fun fact:* You can expect about 1.3 outages per month on average according to this data!

<iframe src="assets/outages_by_month.html" width=800 height=600 frameBorder=0></iframe>

### Bivariate Analysis
If we then do a line plot to show the trend of outages by year, we can spot from the plot the Great Blackout of 2011 (a US Southeast blackout that largely affected California). Interestingly, we can't spot a peak when there were a much bigger blackout that happens during 2003, the Northeast blackout of 2003, but more data seems to be collected since that year with the increasing number of outages the following decade probably for monitoring and quality control purposes.

<iframe src="assets/outages_by_year.html" width=800 height=600 frameBorder=0></iframe>

### Interesting Aggregates
This horizontal bar plot shows the trend of outages by season. You can tell summer power outages caused by severe weather is pretty common compared to the other season, including winter, which is expected!

<iframe src="assets/outages_by_season.html" width=800 height=600 frameBorder=0></iframe>

## Assessment of Missingness

### NMAR analysis
In our dataset, it is suspected that **OUTAGE.RESTORATION is NMAR** (not missing at random). When a column is said to be NMAR, its missingness is dependent on itself. In other words, the nature of any data goes missing in that column will cause the missingness itself to occur. OUTAGE.RESTORATION is NMAR because if the power outage has yet to end, then naturally there is no timestamp recorded for the restoration time of that power outage.

### Missingness Dependency

**CUSTOMERS.AFFECTED and SEASON**

- Null Hypothesis: The missingness of CUSTOMERS.AFFECTED does not depend on SEASON
- Alternative Hypothesis: The missingess of CUSTOMERS.AFFECTED depends on SEASON

Our central question investigates the effect seasonal weather has on power outages, and it would make sense to evaluate the missingness dependency of CUSTOMERS.AFFECTED on SEASON. If the missingness of CUSTOMERS.AFFECTED depends on SEASON, then the missing values of CUSTOMERS.AFFECTED is likely to correspond with a certain SEASON in terms of the power outages caused by severe weather.

<iframe src="assets/missingness_dependency_yes_stats.html" width=800 height=600 frameBorder=0></iframe>

According to the above graph, the missingness of CUSTOMERS.AFFECTED appears to be correlated with the more extreme weather seasons like summer and winter. It may be the case that the power outages in those seasons happen too frequent to get all the reports back for every outages.

Let's conduct a permutation test and see how this goes.

<iframe src="assets/missingness_dependency_yes_dist.html" width=800 height=600 frameBorder=0></iframe>

With our significance level of 0.05, we reject the null with our permutation test result and conclude that **the missingness of CUSTOMERS.AFFECTED depends on SEASON** at a p-value of *0.03*.

-----

**CUSTOMERS.AFFECTED and HURRICANE.NAMES**

- Null Hypothesis: The missingness of CUSTOMERS.AFFECTED does not depend on HURRICANE.NAMES
- Alternative Hypothesis: The missingness of CUSTOMERS.AFFECTED depends on HURRICANE.NAMES

To test the integrity of our missingness test, we are going to conduct another test to see if the missingness of CUSTOMERS.AFFECTED does not depend on HURRICANE.NAMES as what we expected. But let's check out the distribution of CUSTOMERS.AFFECTED missing values across HURRICANE.NAMES first.

<iframe src="assets/missingness_dependency_no_stats.html" width=800 height=600 frameBorder=0></iframe>

We can see that all of the missing values of CUSTOMERS.AFFECTED corresponds with the power outages caused by a hurricane named Sandy; however, the missingness of CUSTOMERS.AFFECTED does not seem to have a relationship with the property of the hurricane names themselves as the missing values does not spread across different hurricane names and forms a comprehensible pattern in the hurricane names CUSTOMERS.AFFECTED gone missing. This makes sense since how hurricanes are named shouldn't have anything to do with how extreme the weather is, and thus, how bad the corresponding power outages are.

Let's conduct a permutation test to see if we are right!

<iframe src="assets/missingness_dependency_no_dist.html" width=800 height=600 frameBorder=0></iframe>

With our significance level of 0.05, we fail to reject the null with our permutation test result, and indeed, **the missingness of CUSTOMERS.AFFECTED does not depend on HURRICANE.NAMES** at a p-value of *0.18*.

**Conclusion**: While the missingness of CUSTOMERS.AFFECTED does not depend on HURRICANE.NAMES, it depends on SEASON; hence, we say that **CUSTOMERS.AFFECTED is MAR** (missing at random).

## Hypothesis Testing

To answer our question, we would conduct a hypothesis test and set the *null hypothesis* to be that **the number of outages due to severe weather during the summer is the same as the number of outages due to severe weather during the winter**, and **the number of outages due to severe weather during the summer is larger than the number of outages due to severe weather during the winter** as our *alternative hypothesis*. The *test statistic* that we will use in our hypothesis test is decided to be **the difference in the number of outages due to severe weather between summer and winter** (i.e. number of outages due to severe weather during the summer - number of outages due to severe weather during the winter) since we want to see if the difference between the number of outages due to severe weather during the summer and winter lies beyond the range of the expected difference. The significance level of our hypothesis test is conventionally **0.05**.


<iframe src="assets/hypothesis_test.html" width=800 height=600 frameBorder=0></iframe>

As we can see from the histogram, the p-value is 0.0, which is below our cut-off of 0.05.

**Conclusion**: Since the p-value cutoff is 0.05, the **null hypothesis is rejected**, and we can draw a conclusion to our question that **the number of outages due to severe weather during the summer is larger than the number of outages due to severe weather during the winter not due to chance alone.**

### Discussion

Even though we cannot say for sure the alternative hypothesis is true, there are probably some applicable reasons that may explain why the number of outages due to severe weather during the summer is larger than the number of outages due to severe weather during the winter, like the bad weather in the summer is more capable of shutting off or damaging the infrastructure that supports and provides the power in the state. This issue, however, is beyond the scope of this report and remains a further discussion to explore in the future!