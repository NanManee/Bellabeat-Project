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
    
    
```r
-- Convert data to make date and time columns consistency

activity$ActivityDate <- as.Date(activity$ActivityDate, "%m/%d/%Y")

weight$Date <- as.Date(weight$Date, "%m/%d/%Y")

sleep$SleepDay <- as.Date(sleep$SleepDay, "%m/%d/%Y")

weight$IsManualReport <- as.logical(weight$IsManualReport)
```

```r
-- Make sure of the consistency of date columns

head(activity)

head(sleep)

head(weight)
```
```r

-- Rename,remove N/A and remove duplications

activity <- activity %>%
  distinct() %>%
  drop_na()

sleep <- sleep %>%
  distinct() %>%
  drop_na()

weight <- weight %>%
  distinct() %>%
  drop_na()
```

```r
-- Verifying the duplication has been removed

sum(duplicated(activity))
sum(duplicated(sleep))
sum(duplicated(weight))
```

```r
-- To make sure that column names are unique and consistent to avoid any errors

clean_names(activity)
clean_names(sleep)
clean_names(weight)
```

```r

-- Delete dublicate column: TrackerDistance column (It has the same values as TotalDistance column)

activity = select(activity, -5) 
colnames(activity)
```

```r
-- To check for how may unique users or Fitbit participants on each dataframe

n_distinct(activity$Id)
n_distinct(sleep$Id)
n_distinct(weight$Id)
```

```r
-- Summary statistics, exempt the weight dataframe because it does not have enough unigue users for analysis

activity %>%
    select(TotalSteps, TotalDistance, SedentaryMinutes) %>%
    summary()

sleep %>%
    select(TotalSleepRecords, TotalMinutesAsleep, TotalTimeInBed) %>%
    summary()
```

```r
-- Combine two dataframes into one (activity and sleep dataframes)

merged_data = merge(activity, sleep, by="Id")
summary(merged_data)
```

```r
-- Create Data Visualization

ggplot(data = merged_data, aes(x = TotalSteps))+
    geom_histogram(bins = 20, color = "white", fill = "blue") +
    xlim(0, 25000)+
    labs(title = "Sample of Total Steps from Fitbit Users")+
    theme(plot.title = element_text(hjust = 0.5, size = 15, face = "bold"))
​
ggplot(data = merged_data, aes(ActivityDate, TotalSteps))+
    geom_point(size = 1)+
    geom_line(color = "blue")+
    labs(title = "Total Steps From Fitbit Users over Time")+
    theme(plot.title = element_text(hjust = 0.5, size = 15, face = "bold"))
​
​
ggplot(data = merged_data, aes(x = TotalMinutesAsleep))+
    geom_histogram(bins = 20, color = "white", fill = "lightblue")+
    xlim(0, 950)+
    labs(title = "Sample of Total Minutes Asleep from Fitbit Users")+
    theme(plot.title = element_text(hjust = 0.5, size = 15, face = "bold"))
​
ggplot(data = merged_data, aes(SleepDay, TotalMinutesAsleep))+
    geom_point(size = 2)+
    geom_line(color = "lightblue")+
    labs(title = "Total Minutes Asleep From Fitbit Users Over Time")+
    theme(plot.title = element_text(hjust = 0.5, size = 15, face = "bold"))
​
​
ggplot(data = merged_data, aes(x = SedentaryMinutes))+
    geom_histogram(bins = 20, color = "white", fill = "royalblue")+
    xlim(0, 1600)+
    labs(title = "Sample of Total Minutes Sedentary from Fitbit Users")+
    theme(plot.title = element_text(hjust = 0.5, size = 15, face = "bold"))
    

ggplot(data = merged_data, aes(ActivityDate, SedentaryMinutes))+
    geom_point(size = 2)+
    geom_line(color = "royalblue")+
    labs(title = "Total Minutes Sedentary from Fitbit Users over Time")+
    theme(plot.title = element_text(hjust = 0.5, size = 15, face = "bold"))  
```

Insights and suggestions

References and Links
Photos from Bellabeat website
Photo https://www.wordfestival.org/suggestions
