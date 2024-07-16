---
title: "Bike Share Data Analysis"
author: "Abdullahi Faysal"
date: "2024-05-21"
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

# Case Study: Analyzing Bike-Share Data for Marketing Insights

The objective of this document is to analyze bike-share data to gain
insights into customer behavior and preferences, specifically focusing
on differentiating factors between casual riders and annual members.

### Introduction

This document presents an analysis of bike-share data to understand
customer behavior and preferences, focusing on casual riders and annual
members.

The analysis will follow a structured approach, encompassing the key
phases of data analysis: Ask, Prepare, Process, Analyze, Share, and Act.

### Approach

#### Ask

-   Formulate research questions to guide the analysis

-   Define the business problem and objectives

-   Establish metrics for evaluating success

#### Prepare

-   Validate data quality and consistency

-   Merge and clean datasets for analysis

-   Ensure data integrity and reliability

#### Process

-   Cleanse and transform data for analysis

-   Document data processing steps for transparency

#### Analyze

-   Identify trends and patterns in customer behavior

-   Extract meaningful insights from the data

-   Develop data-driven recommendations

#### Share

-   Create visualizations to communicate findings effectively

-   Craft a narrative around the data insights

-   Present actionable recommendations to stakeholders

#### Act

-   Provide strategic recommendations based on analysis -Address
    business challenges and opportunities

-   Propose data-informed strategies for decision-making

<br/>

## 1. Ask

#### Scenario

The marketing team aims to enhance customer retention by converting
casual riders into annual members. Understanding the usage disparities
between these two customer segments is crucial for devising targeted
marketing campaigns.

#### Stakeholders:

-   Marketing Director
-   Executive Team

#### Objective

-   The goal is to analyze how casual riders and annual members utilize
    the bike-share program differently based on available data.

#### Deliverables:

-   Insights on usage patterns of casual riders and annual members
-   Visualizations supporting key findings
-   Recommendations for converting casual riders into annual members

<br/>

## 2. Prepare

#### Data Source

The data for this analysis is sourced from the Divvy bike-share program,
specifically the Divvy 2019 Q2, Divvy 2019 Q3, Divvy 2019 Q4, and Divvy
2020 Q1 datasets. These datasets contain trip details and customer
information that will be used to analyze the behavior of casual riders
and annual members.

<br/>

#### Load Libraries

```{r Loading libraries}
library(tidyverse)
library(ggplot2)
library(lubridate)
library(dplyr)
library(readr)
library(janitor)
library(data.table)
library(tidyr)
```

<br/>

#### Load Divvy Bike-Share Data.

```{r}
q4_2019 <- read_csv("C:\\Users\\Abdullahi77\\OneDrive\\Desktop\\Data analyst Project 1\\Divvy_Trips_2019_Q4.csv")
q3_2019 <- read_csv("C:\\Users\\Abdullahi77\\OneDrive\\Desktop\\Data analyst Project 1\\Divvy_Trips_2019_Q3.csv")
q2_2019 <- read_csv("C:\\Users\\Abdullahi77\\OneDrive\\Desktop\\Data analyst Project 1\\Divvy_Trips_2019_Q2.csv")
q1_2020 <- read_csv("C:\\Users\\Abdullahi77\\OneDrive\\Desktop\\Data analyst Project 1\\Divvy_Trips_2020_Q1.csv")
```

<br/>

## 2. Process

#### Display columns names of each dataframe.

```{r}
colnames(q2_2019)
colnames(q3_2019)
colnames(q4_2019)
colnames(q1_2020)
```

#### Renaming columns to ensure uniformity.

#### Certainly! Ensuring consistent column names across dataframes is crucial for effective data analysis. Let's proceed with the cleaning process by renaming the columns using the rename() function.

```{R}
(q4_2019 <- rename(q4_2019
                   ,ride_id = trip_id
                   ,rideable_type = bikeid 
                   ,started_at = start_time  
                   ,ended_at = end_time  
                   ,start_station_name = from_station_name 
                   ,start_station_id = from_station_id 
                   ,end_station_name = to_station_name 
                   ,end_station_id = to_station_id 
                   ,member_casual = usertype))

(q3_2019 <- rename(q3_2019
                   ,ride_id = trip_id
                   ,rideable_type = bikeid 
                   ,started_at = start_time  
                   ,ended_at = end_time  
                   ,start_station_name = from_station_name 
                   ,start_station_id = from_station_id 
                   ,end_station_name = to_station_name 
                   ,end_station_id = to_station_id 
                   ,member_casual = usertype))


(q2_2019 <- rename(q2_2019
                   ,ride_id = "01 - Rental Details Rental ID"
                   ,rideable_type = "01 - Rental Details Bike ID" 
                   ,started_at = "01 - Rental Details Local Start Time"  
                   ,ended_at = "01 - Rental Details Local End Time"  
                   ,start_station_name = "03 - Rental Start Station Name" 
                   ,start_station_id = "03 - Rental Start Station ID"
                   ,end_station_name = "02 - Rental End Station Name" 
                   ,end_station_id = "02 - Rental End Station ID"
                   ,member_casual = "User Type"))
```

#### Using the str() function, we can notice the datatype for ride_id column and the rideable_type column in 2019 quarters is different than that of the 2020 quarter.

```{r}
str(q2_2019)
str(q3_2019)
str(q4_2019)
str(q1_2020)
```

#### Checking and converting column datatypes

#### column datatype of ride_id

```{r}
str(q2_2019$ride_id)
str(q3_2019$ride_id)
str(q4_2019$ride_id)
str(q1_2020$ride_id)
```

#### column datatype of rideable_type

```{r}
str(q2_2019$rideable_type)
str(q3_2019$rideable_type)
str(q4_2019$rideable_type)
str(q1_2020$rideable_type)
```

#### To change datatypes of columns to character

```{r}
q4_2019 <- mutate(q4_2019, ride_id = as.character(ride_id)
                  ,rideable_type = as.character(rideable_type))

q3_2019 <- mutate(q3_2019, ride_id = as.character(ride_id)
                  ,rideable_type = as.character(rideable_type))

q2_2019 <- mutate(q2_2019, ride_id = as.character(ride_id)
                  ,rideable_type = as.character(rideable_type))
```

#### Combine all the datasets into one single dataframe

```{r}
all_divvy_trips <- bind_rows(q2_2019,q3_2019,q4_2019,q1_2020)
```

#### Deleting unwanted columns

```{r}
all_divvy_trips <- all_divvy_trips %>% 
  select(-c(start_lat,start_lng,end_lat,end_lng,birthyear,gender,
            "01 - Rental Details Duration In Seconds Uncapped",
            "05 - Member Details Member Birthday Year",
            "Member Gender","tripduration"))
```

#### Obtaining the unique values in the 'member_casual' column of the 'all_trips' dataset.

```{r}
unique(all_divvy_trips$member_casual)
```

#### Recode values in the 'member_casual' column to their appropriate alternates

```{r}
all_divvy_trips <- all_divvy_trips %>% 
  mutate(member_casual = recode(member_casual,"Subscriber" = "member", "Customer" = "casual"))

##### Display the resulting unique member types

cat("Unique member types after recoding:", "\n")
cat(unique(all_divvy_trips$member_casual), "\n")
```

#### Insert new month, date, year and day columns

```{r}
##### add a new column that is the date format of 'started_at' column
all_divvy_trips$date <- as.Date(all_divvy_trips$started_at)

##### extract month (01 for Jan, 02 for Feb, 03 for March,....)
all_divvy_trips$month <- format(as.Date(all_divvy_trips$date),"%m")

##### extract day (01,02,03,04,...)
all_divvy_trips$day <- format(as.Date(all_divvy_trips$date),"%d")

##### extract year (2019, 2020)
all_divvy_trips$year <- format(as.Date(all_divvy_trips$date),"%Y")

# extract day_of_week (Sunday, Monday, Tuesday,...)
all_divvy_trips$day_of_week <- format(as.Date(all_divvy_trips$date),"%A")

```

#### Added 'ride_length' column with ride duration in seconds, calculated by subtracting start time from end time. Converted 'ride_length' data type to numeric.

```{r}
all_divvy_trips <- all_divvy_trips %>%
  mutate(ride_length = difftime(ended_at, started_at)) %>%
  mutate(ride_length = as.numeric(as.character(.$ride_length)))
```

#### Filter out rows with negative ride_length values or invalid start_station_name entries.

```{r}
all_divvy_trips_v2 <- all_divvy_trips[!(all_divvy_trips$start_station_name == "HQ QR" | all_divvy_trips$ride_length < 0),]
```

## 3. Analyze

#### Analyzing the ride_length variable.

```{r}

# Mean
mean_result <- all_divvy_trips_v2 %>% 
  group_by(member_casual) %>% 
  summarise(mean_ride_length = mean(ride_length))
print(mean_result)

# Median
median_result <- all_divvy_trips_v2 %>% 
  group_by(member_casual) %>% 
  summarise(median_ride_length = median(ride_length))
print(median_result)

# Maximum
max_result <- all_divvy_trips_v2 %>% 
  group_by(member_casual) %>% 
  summarise(max_ride_length = max(ride_length))
print(max_result)

# Minimum
min_result <- all_divvy_trips_v2 %>% 
  group_by(member_casual) %>% 
  summarise(min_ride_length = min(ride_length))
print(min_result)
```

#### Calculate the mean ride length by membership type and day of the week

```{r}
mean_result <- all_divvy_trips_v2 %>%
  group_by(member_casual, day_of_week) %>%
  summarise(mean_ride_length = mean(ride_length)) %>%
  ungroup()

print(mean_result)
```

#### Create an ordered factor for day_of_week (Sunday to Saturday)

```{r}
#ordered function
all_divvy_trips_v2$day_of_week <- ordered(all_divvy_trips_v2$day_of_week,levels=c("Sunday","Monday","Tuesday",         "Wednesday","Thursday","Friday","Saturday"))
```

#### Analyzing ridership data by member type and week by calculating number of rides and average ride duration

```{r}
result <- all_divvy_trips_v2 %>%
  mutate(weekday = lubridate::wday(started_at, label = TRUE)) %>%
  group_by(member_casual, weekday) %>%
  summarize(number_of_rides = n(), average_duration = mean(ride_length)) %>%
  arrange(member_casual, weekday)

print(result)
```

## 4. Share

#### Calculate the total number of rides per day of the week and create a bar plot to visualize the results

```{r}
result <- all_divvy_trips_v2 %>%
  mutate(weekday = weekdays(started_at, abbreviate = FALSE)) %>%
  mutate(weekday = factor(weekday, levels = c("Sunday", "Monday", "Tuesday",   "Wednesday", "Thursday", "Friday", "Saturday"))) %>%
  group_by(member_casual, weekday) %>%
  summarize(number_of_rides = n(), average_duration = mean(ride_length)) %>%
  arrange(member_casual, weekday)

ggplot(result, aes(x = weekday, y = number_of_rides, fill = member_casual)) +
  geom_col(position = "dodge")+
  labs(title = "Total Number of Rides per Day of the Week",
       x = "Weekday",
       y = "Number of Rides",
       fill = "Member Type")
```

#### Calculate the average duration of rides per day of the week

```{r}
# Define the desired order of weekdays

weekday_order <- c("Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday")

all_divvy_trips_v2 %>% 
  mutate(weekday_num = wday(started_at)) %>%
  mutate(weekday = factor(weekday_num, levels = 1:7, labels = weekday_order)) %>%  
  group_by(member_casual, weekday) %>% 
  summarise(number_of_rides = n(), average_duration = mean(ride_length)) %>% 
  arrange(member_casual, weekday) %>% 
  ggplot(aes(x = weekday, y = average_duration, fill = member_casual)) +
  geom_col(position = "dodge") +
  labs(title = "Average Duration of Rides per Day of the Week",
       x = "Weekday",
       y = "Average Duration",
       fill = "Member Type")
```

#### Median ride length per day of week

```{r}

weekday_order <- c("Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday")

all_divvy_trips_v2 %>% 
  mutate(weekday_num = wday(started_at)) %>%  
  mutate(weekday = factor(weekday_num, levels = 1:7, labels = weekday_order)) %>%
  
  group_by(member_casual, weekday) %>% 
  summarize(number_of_rides = n(), median_duration = median(ride_length)) %>% 
  arrange(member_casual, weekday) %>% 
  ggplot(aes(x = weekday, y = median_duration, fill = member_casual)) +
  geom_col(position = "dodge") +
  labs(title = "Median Duration of Rides Per Day of the Week",
       x = "Weekday",
       y = "Median Duration",
       fill = "member type")
  
```

#### Calculate and visualize the average ride length per month

```{r}

# Define the custom order of months starting with January
custom_month_order <- c("January", "February", "March", "April", "May", "June", 
                        "July", "August", "September", "October", "November", "December")

all_divvy_trips_v2 %>% 
  mutate(month = factor(format(started_at, "%B"), levels = custom_month_order)) %>%
  group_by(member_casual, month) %>% 
  summarize(number_of_rides = n(), average_duration = mean(ride_length)) %>% 
  arrange(member_casual, month) %>% 
  ggplot(aes(x = month, y = number_of_rides, fill = member_casual)) + 
  geom_col(position = "dodge") +
  labs(title = "Average Ride Length per Month",
       x = "Month",
       y = "Number of Rides",
       fill = "Member Type") +
  theme(axis.text.x = element_text(angle = 45, hjust = 1))
```

### Summary

Analyzing historical bike data from 2019 — 2020, we observed how annual
members and casual riders use Cyclistic bikes differently. Key findings
include:

-   Both groups prefer Sunday and Saturday for longer rides, resulting
    in a U-shaped trend with a greater dip in the midweek for casual
    riders.

-   Annual members have consistent ride durations throughout the week,
    while casual riders have varying durations.

-   Annual members take more rides overall compared to casual riders.

-   Annual members ride more on weekdays, while casual riders ride more
    on weekends.

-   The busiest period for bike trips is July, August, and September,
    coinciding with the warmer summer months.

## 6: Act

In the Act phase, we will leverage the insights gained from our analysis
of bike-share data to formulate actionable recommendations for
stakeholders. By acting upon these recommendations, we aim to address
the business problem effectively and facilitate data-driven
decision-making.

#### Three Areas of Focus and Recommendations:

### 1. Seasonality

#### Insight

-   There is an increase in demand for casual riders during Spring and
    Summer, presenting an opportunity for targeted advertising campaigns
    and promotional activities during these seasons.

#### Recommendation

-   Launch a captivating email campaign for casual riders in early
    Spring, extending it throughout the Summer to attract and retain
    customers.

-   Strategically schedule offers and promotions, such as early bird
    sign-ups and discounted memberships, encouraging potential riders to
    join ahead of the busy season. Highlight the advantages of becoming
    an annual member compared to remaining a casual rider.

### 2. Purpose of Bike Usage

#### Insight

-   Annual members are more active mid-week with consistent ride
    lengths, while casual riders use bikes for weekend entertainment.

#### Recommendation

-   Introduce enticing weekend passes tailored to weekend activities and
    entertainment, providing casual riders with flexible options.

-   Leverage the power of digital marketing channels to effectively
    promote the new weekend passes, capturing the attention of potential
    riders.

-   Identify popular spots frequented by casual riders using a visually
    appealing map, establish collaborative partnerships, and offer
    exclusive membership discounts combined with entertainment pairings
    (e.g., restaurant discounts, entry passes to sports venues).
    Emphasize the unique advantages of becoming an annual member.

### 3. Usage Patterns

#### Insight

-   Annual members predominantly use bikes on weekdays, whereas casual
    riders prefer weekends.

#### Recommendation

-   Utilize captivating digital marketing channels to inspire bike usage
    during weekdays, positioning it as a sustainable and convenient
    commuting option. Highlight the benefits of biking for physical
    fitness, reducing traffic congestion, and contributing to a greener
    urban environment.
