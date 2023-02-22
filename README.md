# Bellabeat Case Study

##### Author: Jose Aldana

##### Date: February, 2023

This repository contains a data analysis for Bellabeat, where we aim to answer some key business questions by using the Ask, Prepare, Process, Analyze, Share, and Act process that is standard with most data analysts.

# **About the Company**

Bellabeat is a high-tech company that empowers women by giving them the tools necessary to track their health, by recording data like their sleep, activity, stress, and reproductive health. The company was founded in 2013 by Urška Sršen and Sando Mur. Urska's background in art has allowed the company to not compromise on the aesthetics of the products, and their entire line consists of beautiful and diegetic designs that are meant to not only serve their functions as health products but also give off a unique and sleek design philosophy.

**Scenario of the Study**

In this study, we plan to select a single Bellabeat product and analyze both it and other smart device data to gain insight into how consumers are using their smart devices. This information will inform and guide the marketing strategy for the company.

## 1. ASK PHASE

To better understand the needs of the company and solve the problem at hand, we asked some key questions such as:

- Who are the main stakeholders?

We identified the main stakeholders as the co-founders ___Urška Sršen___ and ___Sando Mur___, as well as the ___Bellabeat marketing analytics team___. 

- What are some important business tasks?

The data and the insights it provides should help the company see where the users are missing the mark and use their marketing to incentivize the activities. We applied the trends found to pinpoint recommendations for the products.

We will then apply the trends found to pinpoint recommendations for the products.

## 2. PREPARE PHASE

We acquired the data from [Mobius through Kaggle](https://www.kaggle.com/datasets/arashnic/fitbit), and it is CC0: Public Domain, so anyone is allowed to use and alter this data. The data is stored in a file called usable_datasets_case_study, which contains two out of 18 total files that pertain most to this case study:

-dailyActivity_merged.csv

-sleepDay_merged.csv

(This data is all displayed in wide format.)

While only a small fraction of the datasets were used, the pair of datasets that were used held a substantial amount of data that would provide ample insight for this study.  While also holding much of the data from other, more specific datasets, included in the original file.

We also assessed the reliability, originality, comprehensiveness, currentness, and citability of the data(ROCCC).

- __Reliability__- This data is not the most reliable, since it is from a sample of 30 people, which might be considered too small to show any significant discoveries.
 - __Originality__- The originality of this dataset is not the highest, since it is a third party dataset that is collected from an external site.
- __Comprehensive__- The comprehensiveness of this dataset is low, since it is extremely thorough with certain files even tracking vitals    down to the second. But there are certain datasets that have a smaller number of users.  Creating an imbalance that can cause bias.
 - __Current__- While the dataset is a couple of years old, the data for movement will stay consistent for the most part.
 - __Cited__- This data is from a well cited data collector, and the documentation that accompanies it is extremely comprehensive.

## 3. PROCESS PHASE

For this study, we used SQL to clean the data, specifically BigQuery by Google. Tableau was used to visualize the findings from the cleaned data, and Google Sheets was also slightly utilized to clean smaller details in the data. To ensure that the data was clean, we followed these steps:

- Attempted to import the data into BigQuery, where we encountered an error, due to the time format being included in the date.
- I Decided to go into the file using Sheets/Excel and change the time format to show the date without the time. Which fixed the error.
- The data was then imported into BigQuery with no errors.
- Noticed that there was a Total SleepRecords that show only 1 for the majority of the table. So I removed it using this query:
```
    ALTER TABLE
     `bellabeat-case-study-2-376122.fitbit_data.sleep_day`
    DROP COLUMN
     TotalSleepRecords
```
Counted the number of users, records and dates from the daily activity table using this query: 
```
SELECT 
 COUNT(DISTINCT Id) AS Number_of_users,
 COUNT(*) AS Number_of_records,
 COUNT(DISTINCT ActivityDate) AS Number_of_dates
FROM
 `bellabeat-case-study-2-376122.fitbit_data.daily_activity`
```
Results:
- Number_of_users = 33
- Number_of_records = 940
- Number_of_dates = 31

- Did the same for the sleep day table with this query: 
```
SELECT 
  COUNT(DISTINCT Id) AS Number_of_users,
  COUNT(*) AS Number_of_records,
  COUNT(DISTINCT SleepDay) AS Number_of_dates
FROM
  `bellabeat-case-study-2-376122.fitbit_data.sleep_day`
```
Results:
- Number_of_users = 24
- Number_of_records = 413
- Number_of_dates = 31
- [1000steps.org](https://www.10000steps.org.au) states that a sedentary lifestyle is less than 5,000 steps per day, low activity is between 5000 and 7499, somewhat active is between 7500 and 9999 steps and active is above 10000.
- In order to reflect this, user activity will be classified into these ranges by creating a new column called ActivityLevel.
- I will also add the day of the week to analyze the time more extensively.
- This is the query used to create the column ActivityLevel:
```
ALTER TABLE
 `bellabeat-case-study-2-376122.fitbit_data.daily_activity`
ADD COLUMN
 ActivityLevel String;
```
- The table was then updated to pull the amount of steps that that entry had, and categorized it into the respective categories using this query:
```
UPDATE `bellabeat-case-study-2-376122.fitbit_data.daily_activity` 
SET ActivityLevel = 
CASE 
 WHEN Totalsteps < 5000 THEN 'Sedentary' 
 WHEN Totalsteps BETWEEN 5000 AND 7499 THEN 'Somewhat Active' 
 WHEN Totalsteps BETWEEN 7500 AND 9999 THEN 'Active' 
 ELSE 'Very Active' 
END
WHERE 1 = 1
```
The column DayofWeek was added to show the day of the week that pertains to the date in the ActivityDate column using this query:
```
ALTER TABLE
 `bellabeat-case-study-2-376122.fitbit_data.daily_activity`
ADD COLUMN
 DayofWeek String;
```
The table was then updated to fill in the column with the pertaining days using this query:
```
UPDATE `bellabeat-case-study-2-376122.fitbit_data.daily_activity`
SET DayofWeek = 
    CASE
      WHEN FORMAT_DATE('%w', ActivityDate) = '0' THEN 'Sunday'
      WHEN FORMAT_DATE('%w', ActivityDate) = '1' THEN 'Monday'
      WHEN FORMAT_DATE('%w', ActivityDate) = '2' THEN 'Tuesday'
      WHEN FORMAT_DATE('%w', ActivityDate) = '3' THEN 'Wednesday'
      WHEN FORMAT_DATE('%w', ActivityDate) = '4' THEN 'Thursday'
      WHEN FORMAT_DATE('%w', ActivityDate) = '5' THEN 'Friday'
      WHEN FORMAT_DATE('%w', ActivityDate) = '6' THEN 'Saturday'
      END
WHERE `bellabeat-case-study-2-376122.fitbit_data.daily_activity` IS NOT NULL;
```
The CDC recommends around 7 hours of sleep.
- In order to reflect that a column called SleepQuality will be created to show if users are receiving "below recommended", "recommended" and "above recommended" amounts of sleep using this query: 
```
SELECT 
 CAST(Id AS STRING) AS Id,
 CAST(SleepDay AS DATE) AS SleepDate,
 ROUND(TotalTimeInBed/60,2) AS HourInBed,
 ROUND(HourAsleep,2) AS HourAsleep,
 CASE 
  WHEN HourAsleep< 6 THEN 'Below Recommended'
  WHEN HourAsleep BETWEEN 6 AND 8 THEN 'Recommended'
  ELSE 'Above Recommended'
 END AS SleepQuality 
FROM
 (WITH result AS 
    (SELECT *,
      TotalMinutesAsleep/60 AS HourAsleep
    FROM `bellabeat-case-study-2-376122.fitbit_data.sleep_day`) 
    SELECT * FROM result)
```
These results were then imported into BigQuery under the name sd_query
- The sd_query table and sleep_day table were then joined by the Id and SleepDay column using this query:
```
SELECT
  *
FROM
  `bellabeat-case-study-2-376122.fitbit_data.sd_query`
JOIN
  `bellabeat-case-study-2-376122.fitbit_data.sleep_day`
ON
  `bellabeat-case-study-2-376122.fitbit_data.sd_query`.Id =    `bellabeat-case-study-2-376122.fitbit_data.sleep_day`.Id
AND
  `bellabeat-case-study-2-376122.fitbit_data.sd_query`.SleepDate = `bellabeat-case-study-2-376122.fitbit_data.sleep_day`.SleepDay
```
This table was named Joined_sleep and was then joined with the DailyActivity table to give me a more comprehensive and all encompassing table.
- These tables will also be joined by the Id and Date columns using this query:
```
SELECT
*
FROM
  `bellabeat-case-study-2-376122.fitbit_data.daily_activity`
JOIN
  `bellabeat-case-study-2-376122.fitbit_data.joined_sleep`
ON
  `bellabeat-case-study-2-376122.fitbit_data.daily_activity`.Id =    `bellabeat-case-study-2-376122.fitbit_data.joined_sleep`.Id
AND
  `bellabeat-case-study-2-376122.fitbit_data.daily_activity`.ActivityDate = `bellabeat-case-study-2-376122.fitbit_data.joined_sleep`.SleepDate
```
## 4. ANALYZE PHASE

We analyzed the data to identify trends and insights that would inform the company's marketing strategy. We looked at how users are interacting with the product
