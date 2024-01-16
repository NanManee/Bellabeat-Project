# Bellabeat_Project

### Introduction

### About Company

A high-tech manufacturer that focuses on creating health and wellness products for women. They are known for their smart jewelry and accessories that track various aspects of women’s health, including activity levels, stress levels, menstrual cycles and sleep patterns. Bellabeat aims to empower women to take control of their well-being by providing them with insights into their health through stylish and wearable technology [Click Here](https://bellabeat.com)

### Business Task

Analyze smart device data to gain insight into how consumers are using their non-Bellabeat smart devices and apply these insights to enhance one specific Bellabeat product. This analysis aims to provide Bellabeat with recommendations on improving its future marketing strategies

### Questions to answers

- What are some trends in smart device usage?
- How could these trends apply to Bellabeat customers?
- How could these trends help influence Bellabeat marketing strategy?

### Data Sources

In this study, we focus on how consumers use non-Bellabeat smart devices. Kaggle Data Set: FitBit Fitness Tracker Data (CC0: Public Domain, dataset made available through Mobius): This data set contains personal fitness tracker from thirty fitbit users. Thirty eligible Fitbit users consented to the submission of personal tracker data, including minute-level output for physical activity, heart rate, and sleep monitoring. It includes information about daily activity, steps, and heart rate that can be used to explore users’ habits.

### Tools 

R Programing Language - Data Cleaning, Data Analysis, and Data visualization


### Installing and Loading packages and libraries

library(tidyverse)
library(lubridate)
library(janitor)
library(dplyr)
library(skimr)
library(ggplot2)
library(tidyr)


### Import and name the dataframes

activity <- read.csv("../input/fitbit/Fitabase Data 4.12.16-5.12.16/dailyActivity_merged.csv")

sleep <- read.csv("../input/fitbit/Fitabase Data 4.12.16-5.12.16/sleepDay_merged.csv")

weight <- read.csv("../input/fitbit/Fitabase Data 4.12.16-5.12.16/weightLogInfo_merged.csv")


### Key Takeaways from review the dataframes

* DailyActivity Dataframe:
  - Same data - TotalDistance column and TrackerDistance column.
  - Unclear column names:
    - FairlyActiveMinutes and LightlyActiveMinutes are almost the same meaning.
    - Instead of "Fairly", it should changed to "Moderate".
    - Calories column is unclear either calories intake or calories burned.
    - All the columns with distance is not specific if the data are in miles, meters, or kilometers
  - The number range in these columns are questionable:

    - VeryActiveDistance       0 - 21.92
    - ModeratelyActiveDistance 0 - 6.48
    - LightActiveDistance      0 - 10.71

    - VeryActiveMenutes        0 - 210
    - FairlyActiveMinutes      0 - 143
    - LightlyActiveMinutes     0 - 518

* SleepDay and WeightLogInfo dataframes:

  - Time data need to be converted to date time format and split to date and time.
    

### Convert data to make date and time columns consistency

activity$ActivityDate <- as.Date(activity$ActivityDate, "%m/%d/%Y")

weight$Date <- as.Date(weight$Date, "%m/%d/%Y")

sleep$SleepDay <- as.Date(sleep$SleepDay, "%m/%d/%Y")

weight$IsManualReport <- as.logical(weight$IsManualReport)


* Make sure of the consistency of date columns

head(activity)

head(sleep)

head(weight)

