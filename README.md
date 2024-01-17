# Bellabeat_Project

### Introduction
![Bellabeat1](https://github.com/NanManee/Bellabeat-Project/assets/156528525/3f151db0-5230-4606-9327-4052ef684a97)

### About Company

A high-tech manufacturer that focuses on creating health and wellness products for women. They are known for their smart jewelry and accessories that track various aspects of women’s health, including activity levels, stress levels, menstrual cycles and sleep patterns. Bellabeat aims to empower women to take control of their well-being by providing them with insights into their health through stylish and wearable technology [Click Here](https://bellabeat.com)
![Bellabeat2](https://github.com/NanManee/Bellabeat-Project/assets/156528525/55cda1f2-15b8-4749-b368-fa75794fba07)

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

```r
library(tidyverse)
library(lubridate)
library(janitor)
library(dplyr)
library(skimr)
library(ggplot2)
library(tidyr)
```

### Import and name the dataframes

```r
activity <- read.csv("../input/fitbit/Fitabase Data 4.12.16-5.12.16/dailyActivity_merged.csv")

sleep <- read.csv("../input/fitbit/Fitabase Data 4.12.16-5.12.16/sleepDay_merged.csv")

weight <- read.csv("../input/fitbit/Fitabase Data 4.12.16-5.12.16/weightLogInfo_merged.csv")
```

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


### Prepare, Clean and transform data    
    
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
![image](https://github.com/NanManee/Bellabeat-Project/assets/156528525/9f4f649c-66ea-4bd4-9db1-9266da251c71)


![image](https://github.com/NanManee/Bellabeat-Project/assets/156528525/a74c2d7b-ce6d-47a5-b1cd-c0624384f82f)


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
-- Summary statistics, I have to exempt the weight dataframe because it does not have enough unique users for analysis

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
![p1](https://github.com/NanManee/Bellabeat-Project/assets/156528525/fff22921-1282-482e-a58d-a87d91c8fc97)

### Limitations

- This ia a 2016 Fitbit dataset, which is not up to date. Therefore, it's chalenging to provide beneficial insights and recommendations.
- Many file tables do not have enough data for the analysis. I have to exempt the weight dataframe because it does not have enough unique users for analysis.

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

​![p2](https://github.com/NanManee/Bellabeat-Project/assets/156528525/186b06ca-0051-4f0d-97c8-ba34ee70887a)

​![p3](https://github.com/NanManee/Bellabeat-Project/assets/156528525/d06e3114-dcb1-434d-9ee4-e7740e396ab3)

![p4](https://github.com/NanManee/Bellabeat-Project/assets/156528525/258f2f3d-993a-41ec-9247-d4e7bb013fcf)




### Insights

- The average number of steps for Fitbit users is around 8,000 each day, and the overall step count remained steady throughout an entire month (4/12/2016 - 5/12/2016).
- There were a few days when Fitbit users were very active, walking over 20,000 steps!
- Although the average total steps per day for users are quite high, there are days when users remain sedentary for as long as 1,440 minutes, which is around 24 hours. The average sedentary time is 800 minutes, or around 13 hours a day.
- Fitbit users are likely to become sedentary towards the end of the monthly cycle, as it seems they lose motivation or interest in being active.
- The users' sleep patterns vary, with the shortest sleep duration at 58 minutes and the longest at 13 hours.
- The majority of Fitbit users have an average sleep duration of 419 minutes, or around 7 hours per night.

### suggestions
- I had a chance to look up the reviews from the existing Bellabeat customers on Amazon website. It shows 1,758 global ratings with 44% customers love Bellabeat products and give it 5 stars, about 10% customers give it 4,3,and 2 stars. 21% customers are not happy with the products and give it 1 star [Click Here](https://www.amazon.com/Bellabeat-Urban-Jewelry-Health-Tracker/dp/B01LB4EUGS/ref=sr_1_1?crid=E6WXJ8R3CR9F&keywords=bellabeat&qid=1705504792&sprefix=bellabe%2Caps%2C104&sr=8-1&th=1) This is only one souce from Amazon, so it cannot replesent as a whole. This is just some ideas that Bellabeat may need to evaluate the product quality.  improve and ensure the future happy customers.

- 
- I would recommend to Bellabeat to update friendly user Bellabeat app to connect with the users. Giving great tips about helthy nutrition food, exercise tips,
- App that help users plan for a log term goal health and breakdown for every three month or a moth goal depending on what are the users focus on and what are priority in thier life. For example, there's a feature and funtion for users who want to lose weight set thier  a year goal for losie 30
- 
Setting a healthy and realistic weight loss goal is crucial for long-term success. A common guideline is aiming for a weight loss of 1-2 pounds (approximately 0.5-1 kg) per week. This is considered a safe and sustainable rate. Therefore, for a year-long goal:

Conservative Goal: Aim for a weight loss of 1 pound per week. Over the course of a year, this would amount to approximately 52 pounds.

Moderate Goal: Aim for a weight loss of 1.5 pounds per week. Over the course of a year, this would amount to approximately 78 pounds.

Aggressive Goal: Aim for a weight loss of 2 pounds per week. Over the course of a year, this would amount to approximately 104 pounds.



It's important to note that individual factors, such as starting weight, metabolism, and overall health, can influence weight loss. Always consult with a healthcare professional or a registered dietitian before starting any weight loss plan to ensure it is safe and tailored to your specific needs. Additionally, focusing on overall health, including a balanced diet and regular physical activity, is essential for sustainable weight management.
- 
- As today the year of 2024, I believe that Bellabeat has already improved it's product features to the best yet.
- 
- According to Medical News Today, walking at 9,000 steps a day is likely to reduce a chance of dying early by 60%. For people walking 7,000 steps will reduce the chances of cardiovascular disease by 51%. 
- 

![Bellabeat12](https://github.com/NanManee/Bellabeat-Project/assets/156528525/e33dbed6-5772-45b2-87d1-51cb0c1c0a0e)

### References and Links
Medical News Today - https://www.medicalnewstoday.com/articles/do-you-really-need-to-walk-10000-steps-to-see-health-benefits
Photos from Bellabeat website
Photo https://www.wordfestival.org/suggestions



![Bellabeat11](https://github.com/NanManee/Bellabeat-Project/assets/156528525/d86662c0-675b-4de7-b14b-f9865bf056f8)
