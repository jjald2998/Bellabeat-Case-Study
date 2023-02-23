# Bellabeat Case Study

##### Author: Jose Aldana

##### Date: February, 2023

This repository contains a data analysis for Bellabeat, where we aim to answer some key business questions by using the Ask, Prepare, Process, Analyze, Share, and Act process that is standard with most data analysts.

# **About the Company**

Bellabeat is a high-tech company that empowers women by giving them the tools necessary to track their health, by recording data like their sleep, activity, stress, and reproductive health. The company was founded in 2013 by Urška Sršen and Sando Mur. Urska's background in art has allowed the company to not compromise on the aesthetics of the products, and their entire line consists of beautiful and diegetic designs that are meant to not only serve their functions as health products but also give off a unique and sleek design philosophy. Their main products include their Bellabeat app which work in conjunction with their Leaf(bracelet, neckalce and clip), Time(Smart Watch) and Spring(Water Bottle) products.
- I will be focusing on improving the customer experience with the Time product in conjunction with the Bellabeat app.

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
- <img width="418" alt="Screenshot 2023-02-21 at 9 36 54 PM" src="https://user-images.githubusercontent.com/73331822/220516816-73510354-2dd7-48ca-8b72-3ae0c109d153.png">

Did the same for the sleep day table with this query: 
```
SELECT 
  COUNT(DISTINCT Id) AS Number_of_users,
  COUNT(*) AS Number_of_records,
  COUNT(DISTINCT SleepDay) AS Number_of_dates
FROM
  `bellabeat-case-study-2-376122.fitbit_data.sleep_day`
```
Results:
- <img width="449" alt="Screenshot 2023-02-21 at 9 51 28 PM" src="https://user-images.githubusercontent.com/73331822/220517352-bc15ae38-6da7-487f-9c6c-877e0eb6dae3.png">

[1000steps.org](https://www.10000steps.org.au) states that a sedentary lifestyle is less than 5,000 steps per day, low activity is between 5000 and 7499, somewhat active is between 7500 and 9999 steps and active is above 10000.
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
- Tableau was used to visualize the findings from my SQL queries.
- I wanted to emphasize the difference in calories burned for each of the different activity levels.

<img width="1359" alt="Screenshot 2023-02-21 at 10 13 59 PM" src="https://user-images.githubusercontent.com/73331822/220520226-9a348564-4271-4a43-85dd-9811fc65b93b.png">
This Box and Whiskers plot not only shows the average calories burned for each activity level, it also shows the spread of the total calories burned.

- This plot shows that even though the averages from active and very active are similar, the active section skews much higher than any other activity type.

- When analyzing the average hours asleep and in bed for each activity level, I found that on average the more active the user was, the less they slept, and subsequently, stayed in bed.

<img width="1357" alt="Screenshot 2023-02-21 at 10 21 21 PM" src="https://user-images.githubusercontent.com/73331822/220521192-e9ca1899-8517-4533-bda1-12bf216043e9.png">

More time in bed was also found to have a slight negative correlation to the amount of calories that users burned.

<img width="1354" alt="Screenshot 2023-02-21 at 10 24 53 PM" src="https://user-images.githubusercontent.com/73331822/220521587-2312e963-a9d1-4b8f-9842-25517faf3d61.png">

To reinforce this correlation I found that the more hours in bed users had, the less steps they were taking.

<img width="1348" alt="Screenshot 2023-02-21 at 10 39 10 PM" src="https://user-images.githubusercontent.com/73331822/220523516-d74ecda4-2e44-44af-b2e4-2d1cc4b61b20.png">

I also discovered that the more steps users took, the more calories they burned.

<img width="1361" alt="Screenshot 2023-02-21 at 10 40 39 PM" src="https://user-images.githubusercontent.com/73331822/220523656-7fe3adf1-722c-4f41-b20b-2a831ef9d3a8.png">

- Thus it can be concluded that the more time asleep users are, the less steps they are taking, and subsequently, the less calories they are burning.

- I also measured the total amount of calories burned for each day of the week:

<img width="518" alt="Screenshot 2023-02-21 at 10 33 02 PM" src="https://user-images.githubusercontent.com/73331822/220522655-fe00e2c3-e55d-4f1b-bdf0-5c8cfc9b67d9.png">

I found that the days with the least amount of total calories burned were Monday and Sunday.  Perhaps due to Sunday being most people's day of rest, and Monday being the beginning of the traditional work week.

- I also found that even though, people with less sleep reported higher activity levels.  When looking at the different levels of sleep (Below recommended, recommended and above recommended) the users who burned the most amount of calories on average are people who received the recommended amount of sleep.

<img width="805" alt="Screenshot 2023-02-22 at 12 37 25 PM" src="https://user-images.githubusercontent.com/73331822/220725478-e7f7720b-0bb9-4b00-af34-edeace70d495.png">

It may seem that the less sleep users get, the more activity they will have, and thus, the more calories they will burn. But in reality, the quality of their sleep is extremely important to the amount of calories they will burn.

## 5. SHARE PHASE


## 6. ACT PHASE

Conclusions based on this analysis:

- The data provided was far too small. With a larger amount of users, the findings would be much more concrete and accurate.
- The biggest finding was that, on average, the more active users were the more calories they would burn.  This could be seen with the box and whiskers plot, as well as the relationship between sleep and steps taken.
- While it may seem that the less sleep someone receives the more time they would have to burn calories.  But in reality, both too much and not enough sleep reflected less calories burned when compared to receiving the recommended amount of sleep.
- What should be focused on, is making sure users are more active, while also reinforcing helthy sleeping habits.
- In conclusion, an effective way to incentivize these healthier practices is to remind the users to stay active throughout the day, or whenever the watch detects a lack of activity for a certain amount of time. (See Apple Watch movement reminders for an example).
- This could also be paired with a bedtime feature that will kindly remind you of when to go to bed in order to receive the recommended amount of sleep.
- Another useful feature that has been seen in the Apple Watch, is the ability to use the watch as an alarm by lightly vibrating on the users wrist.  This would be a more effective, and less jarring way of waking a user so that they do not oversleep.
- All of these features could be coupled with rewards in order to encourage users to keep their healthier habits up. Such as a virtual badge, which can be seen with the Apple watch. But since the product does not have a virtual interface, these rewards could be seen directly in the application.


![CBmULikW4AAz-nK](https://user-images.githubusercontent.com/73331822/221049227-e47738a2-40ed-4c18-b414-b568b2ba614d.jpg)

- A more effective incentive for users would be a discount of free membership to their bellabeat app that accompanies their smart devices.  Since it is necessary to use any bellabeat product effectively.

