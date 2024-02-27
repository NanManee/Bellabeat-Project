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

In this study, we focus on how consumers use non-Bellabeat smart devices. Kaggle Data Set: FitBit Fitness Tracker Data (CC0: Public Domain, dataset made available through Mobius): This data set contains personal fitness tracker from thirty fitbit users, a distributed survey via Amazon Mechanical Turk. The Fitbit users consented to the submission of personal tracker data, including minute-level output for physical activity, heart rate, and sleep monitoring. It includes information about daily activity, steps, and heart rate that can be used to explore users’ habits.

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
![bb1](https://github.com/NanManee/Bellabeat-Project/assets/156528525/15e26c12-3cd2-413f-9166-6b06b8d3f53d)

### Import and name the dataframes

```r
activity <- read.csv("../input/fitbit/Fitabase Data 4.12.16-5.12.16/dailyActivity_merged.csv")

sleep <- read.csv("../input/fitbit/Fitabase Data 4.12.16-5.12.16/sleepDay_merged.csv")

weight <- read.csv("../input/fitbit/Fitabase Data 4.12.16-5.12.16/weightLogInfo_merged.csv")
```

### Preview the dataframes and identify all the columns
```r
head(activity)
head(sleep)
head(weight)
```
![image](https://github.com/NanManee/Bellabeat-Project/assets/156528525/fa5cc769-ab94-43b0-a5a1-6a95f2478455)

![image](https://github.com/NanManee/Bellabeat-Project/assets/156528525/eca1ed5a-1fa2-4fa1-bdc7-ccdbc0e06c4a)


### Key Takeaways from review the dataframes

* DailyActivity Dataframe:
  - Same data - TotalDistance column and TrackerDistance column.
  - Unclear column names:
    - FairlyActiveMinutes and LightlyActiveMinutes are almost the same meaning.
    - Instead of "Fairly", it should changed to "Moderate".
    - Calories column is unclear either calories intake or calories burned.
    - All the columns with distance is not specific if the data are in miles, meters, or kilometers
  - The number range in these columns are questionable, they all start with 0:

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
![image](https://github.com/NanManee/Bellabeat-Project/assets/156528525/c72fe447-d3c7-4461-a27e-c68c5497fca8)
![image](https://github.com/NanManee/Bellabeat-Project/assets/156528525/44ba53af-49ab-462f-8bac-016dd8de2b21)
![image](https://github.com/NanManee/Bellabeat-Project/assets/156528525/48648eb9-a301-4908-ada5-db99c8dfa894)

```r

-- Delete dublicate column: TrackerDistance column (It has the same values as TotalDistance column)

activity = select(activity, -5) 
colnames(activity)
```
![image](https://github.com/NanManee/Bellabeat-Project/assets/156528525/b35766f2-d86d-4a2f-b80b-2926888f655e)

```r
-- To check for how may unique users or Fitbit participants on each dataframe

n_distinct(activity$Id)
n_distinct(sleep$Id)
n_distinct(weight$Id)
```
![image](https://github.com/NanManee/Bellabeat-Project/assets/156528525/7014fade-e217-4bfa-a663-618e49743152)

```r
-- Summary statistics, I have to exempt the weight dataframe because it has only 2 unique users for analysis while Activity and Sleep dataframes has 33 and 24 unique users.

activity %>%
    select(TotalSteps, TotalDistance, SedentaryMinutes) %>%
    summary()

sleep %>%
    select(TotalSleepRecords, TotalMinutesAsleep, TotalTimeInBed) %>%
    summary()
```
![image](https://github.com/NanManee/Bellabeat-Project/assets/156528525/71778ad1-46da-4774-804f-1c51d1cd615e)

```r
-- Combine two dataframes into one (activity and sleep dataframes)

merged_data = merge(activity, sleep, by="Id")
summary(merged_data)
```
![p1](https://github.com/NanManee/Bellabeat-Project/assets/156528525/fff22921-1282-482e-a58d-a87d91c8fc97)


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




### Limitations

- Many file tables do not have enough data for the analysis. I have to exempt the weight dataframe because it does not have enough unique users for analysis.
- This is a 2016 Fitbit dataset for this capstone, it is not up to date. Therefore, it's challenging to provide beneficial insights and recommendations.

### Insights

- The average number of steps for Fitbit users is around 8,000 each day, and the overall step count remained steady throughout an entire month (4/12/2016 - 5/12/2016).
- There were a few days when Fitbit users were very active, walking over 20,000 steps!
- Although the average total steps per day for users are quite high, there are days when users remain sedentary for as long as 1,440 minutes, which is around 24 hours. The average sedentary time is 800 minutes, or around 13 hours a day.
- Fitbit users are likely to become sedentary towards the end of the monthly cycle, as it seems they lose motivation or interest in being active.
- The users' sleep patterns vary, with the shortest sleep duration at 58 minutes and the longest at 13 hours.
- The majority of Fitbit users have an average sleep duration of 419 minutes, or around 7 hours per night.

### Recomendations

- Bellabeat aims to enhance and update more features on the app. It's important to create a user-friendly app or a podcast to connect with users.
  Instructing users about the health benefits of staying active is crucial. According to Medical News Today, walking 9,000 steps a day is likely to reduce the chance of dying early by 60%. For people walking 7,000 steps, the chances of cardiovascular disease are reduced by 51%.
   - Creating an app that lets users connect and share their progress on their social media with friends. This would help inspire each other to stay active.
   - Creating an app that provides great tips about healthy nutrition, exercise tips, positive mottos, or stories, and meditation progression.
   - Creating more themes on the Bellabeat app so that users can enjoy using the app, with many themes to select from.
  
  Instructs and encourages users to get enough sleep. According to VeryWellHealth.com, not getting enough sleep is associated with depression, heart disease, obesity, weight gain, and other health issues. The recommended amount of sleep by age is as follows:
   - Teenagers (14 to 17 years): Should average eight to 10 hours per day
   - Younger adults (18 to 25 years old): Should average seven to nine hours per day
   - Adults (26 to 64): Should average seven to nine hours per day
   - Older adults (age 65 and over): Should average seven to eight hours per day  (I only select the age group 14-65 and over because of the potential Bellabeat users).
    
  Guide users plan for long-term health goals and breaks down goals for every three months or a monthly goal, depending on the user's focus and priorities in their life. Setting a healthy and realistic weight loss goal is crucial for long-term success. A common guideline is aiming for a weight loss of 1-2 pounds per week. For a year-long goal:
  - Level 1 Goal: Aim for a weight loss of 1 pound per week. Over the course of a year, this would amount to approximately 52 pounds.
  - Level 2 Goal: Aim for a weight loss of 1.5 pounds per week. Over the course of a year, this would amount to approximately 78 pounds.
  - Level 3 Goal: Aim for a weight loss of 2 pounds per week. Over the course of a year, this would amount to approximately 104 pounds.
Note: Individual factors, such as starting weight and overall health, can influence weight loss. Individuals should consult with a doctor before starting any weight loss plan to ensure it is safe. Focusing on overall health, including a balanced diet and regular physical activity, is essential for sustainable weight management.

- Available 24/7 Live chat customer support to address issues that users have.
- Partnering with a YouTube health and fitness influencer can be a powerful way to increase product awareness among Bellabeat target audience. Work closely with the influencer to create engaging content that showcases Bellabeat product in an authentic and appealing way. Encourage them to share personal stories or experiences related to Bellabeat product to resonate with their audience.
- Run Paid Advertising Campaigns: Allocate a portion of Bellabeat marketing budget to run targeted advertising campaigns on social media platforms. Utilize features like sponsored posts, influencer collaborations, and targeted ads to reach potential customers interested in health and fitness.
- Engage with Fitness Events: Sponsor or participate in fitness events throughout the year to further establish Bellabeat brand presence in the health and fitness community. Set up booths, offer product demonstrations, and engage with attendees to create memorable experiences.

![Bellabeat12](https://github.com/NanManee/Bellabeat-Project/assets/156528525/e33dbed6-5772-45b2-87d1-51cb0c1c0a0e)

### Additional Recomendations

- I had a chance to look up the reviews on Leaf, smart jewelry from the existing Bellabeat customers on the Amazon website. It shows 1,758 global ratings and an overall rating of 3.5 stars. Forty-four percent of customers love Leaf and give them 5 stars, while about 10% of customers give it 4, 3, and 2 stars. Over 20% of customers are not happy with the products and give them 1 star. Customers mentioned that the mobile app, sleep tracking, accuracy, and especially versatility features need to be improved. I understand that this is only one source from Amazon.com and it cannot represent the whole. Amazon has a great source of data, and Amazon customers trust the reviews, impacting their buying decisions. If Bellabeat can improve its products and decrease the percentage of 1 - 3 stars reviews, it would gain trust from Amazon customers. I suggest Bellabeat focus on further studies to evaluate the products, improve quality, and gain trust from new customers.

- Future hypoallergenic products to consider include smart rings, smart earrings, and a watch version of the Ivy bracelet. As of now, BioSensive Technologies stands out for their product; Ear-O-Smart. It tracks active heart rate, active minutes, and calorie burn, but it does not track sleep. Another company, like Oura Ring, creates a smart ring that tracks heart rate, active minutes, women's health, and additionally, it tracks blood oxygen sensing (SpO2) when asleep and has illness detection to monitor body temperature and heart rate so users can tell when they may be getting sick sooner.

- Expanding into new markets involves introducing a watch version of the Ivy bracelet, targeting new customer groups like working women or young teen girls who want to enhance their style with fitness jewelry that not only looks pretty but also functions well, matching their outfits on a daily basis. This expansion also provides Bellabeat customers with the option to choose between the Ivy bracelet and the Ivy watch.

- Bellabeat has strong ideas of creating unique, beautiful, and hypoallergenic health tracker products that focus on women. I am positive Bellabeat has endless capabilities of creating new products that are beautiful and functional at the same time. I hope this study is helpful, thank you for your time reviewing.


### References and Links

https://ouraring.com/

https://shopjoule.com/

Photos https://bellabeat.com/

Photo https://www.wordfestival.org/suggestions

https://www.verywellhealth.com/what-time-should-you-go-to-sleep-4588298

https://www.medicalnewstoday.com/articles/do-you-really-need-to-walk-10000-steps-to-see-health-benefits

https://www.amazon.com/Bellabeat-Urban-Jewelry-Health-Tracker/dp/B01LB4EUGS/ref=sr_1_3?crid=25JZGJLDK2VEZ&keywords=bellabeat%2Bivy&qid=1705515233&sprefix=bellabeat%2Bivy%2Caps%2C107&sr=8-3&th=1


![Bellabeat11](https://github.com/NanManee/Bellabeat-Project/assets/156528525/d86662c0-675b-4de7-b14b-f9865bf056f8)
