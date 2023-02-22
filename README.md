# Bellabeat Case Study
My insights into the Activewear brand Bellabeat's data that could help improve customer experience.

 BELLABEAT CASE STUDY

This repository contains a data analysis for Bellabeat, where we aim to answer some key business questions by using the Ask, Prepare, Process, Analyze, Share, and Act process that is standard with most data analysts.

About the Company
Bellabeat is a high-tech company that empowers women by giving them the tools necessary to track their health, by recording data like their sleep, activity, stress, and reproductive health. The company was founded in 2013 by Urška Sršen and Sando Mur. Urska's background in art has allowed the company to not compromise on the aesthetics of the products, and their entire line consists of beautiful and diegetic designs that are meant to not only serve their functions as health products but also give off a unique and sleek design philosophy.

Scenario of the Study
In this study, we plan to select a single Bellabeat product and analyze both it and other smart device data to gain insight into how consumers are using their smart devices. This information will inform and guide the marketing strategy for the company.

Ask Phase
To better understand the needs of the company and solve the problem at hand, we asked some key questions such as:

Who are the main stakeholders?
What are some important business tasks?
We identified the main stakeholders as the co-founders Urška Sršen and Sando Mur, as well as the Bellabeat marketing analytics team. We also aimed to find important trends found in smart devices when using them for health tracking, and how they should influence the company's marketing. The data should help the company see where the users are missing the mark and use their marketing to incentivize the activities. We applied the trends found to pinpoint recommendations for the products.
Prepare Phase
We acquired the data from Mobius through Kaggle, and it is CC0: Public Domain, so anyone is allowed to use and alter this data. The data is stored in a file called usable_datasets_case_study, which contains two out of 18 total files that pertain most to this case study:

dailyActivity_merged.csv
sleepDay_merged.csv
The data is all displayed in wide format. While only a small fraction of the datasets were used, the pair of datasets that were used held a substantial amount of data that would provide ample insight for this study. While also holding much of the data from other, more specific datasets, included in the original file.
We also assessed the reliability, originality, comprehensiveness, currentness, and citability of the data. While this dataset is not the most reliable or original, it is comprehensive and cited, making it useful for providing insights. However, the dataset only has 33 unique users, and out of those, only 24 entered their sleep. Therefore, this dataset is incomplete and not completely comprehensive. The lack of ages makes it difficult to classify the data to the fullest extent, and it fails to display information such as location or any other demographic identifiers.

Process Phase
For this study, we used SQL to clean the data, specifically BigQuery by Google. Tableau was used to visualize the findings from the cleaned data, and Google Sheets was also slightly utilized to clean smaller details in the data. To ensure that the data was clean, we followed these steps:

Attempted to import the data into BigQuery, where we encountered an error.
Decided to go into the file using Sheets/Excel and change the time format to show the date without the time. Which fixed the error.
The data was then imported into BigQuery with no errors.
Analyze Phase
We analyzed the data to identify trends and insights that would inform the company's marketing strategy. We looked at how users are interacting with the product
