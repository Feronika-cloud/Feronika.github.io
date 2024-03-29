# Feronika' Portfolio

# Case Study 2: How Can a Wellness Technology
Company Play It Smart?

# About:
This capstone project was conducted in RStudio as part of my Google Data Analytics Professional Certificate course.

# Scenario:
As a junior data analyst on Bellabeat's marketing team, tasked with analyzing smart device data, I aim to uncover insights to drive marketing strategy for the company's products. Urška Sršen, Bellabeat's cofounder and Chief Creative Officer, believes that analyzing smart device fitness data could unlock growth opportunities for the company.

# Business Task:
Analyze smart device usage data to understand consumer behavior with non-Bellabeat devices and apply these insights to Bellabeat's products.

# Stakeholders:
Urška Sršen: Bellabeat’s cofounder and Chief Creative Officer
Sando Mur: Mathematician and Bellabeat’s cofounder
Bellabeat marketing manager and their analytics team

# Data Source:
The data consists of Fitbit Fitness Tracker data obtained from Kaggle. About 30 Fitbit users allowed collection of personal tracker data on their active time, sleep, and heart rate for one month (04/12/2016-05/12/2016). A total of 18 CSV files are available. 
[Link Text](https://www.kaggle.com/datasets/arashnic/fitbit)


# How does the data help in answering the business questions?
The data's reliability is limited due to its small sample size and lack of gender specificity.

It is sourced from a third party, collected via Amazon Mechanical Turk, reducing its originality.

The data provides moderately comprehensive insights into daily activity intensity, calories burned, steps taken, sleep time, and weight.

However, its relevance may be impacted as it was collected in 2016, potentially affecting the representation of users' current habits.
Data citation is limited as it lacks key descriptive information about its collection.

## Install packages and loading library 
```{r install packages}
install.packages("tidyverse")
library(tidyverse)
install.packages("here")
library(here)
install.packages("skimr")
library(skimr)
install.packages("janitor")
library(janitor)
```

## Importing dataset 

```{r}
DailyActivity <- read.csv("dailyActivity_merged.csv")
HeartRate <- read.csv("heartrate_seconds_merged.csv")
SleepDay <- read.csv("sleepDay_merged.csv")
```

## Inspecting and cleaning data

```{r}
skim_without_charts(DailyActivity)
skim_without_charts(HeartRate)
skim_without_charts(SleepDay)
```
```{r}
glimpse(DailyActivity)
glimpse(HeartRate)
glimpse(SleepDay)
```

## Convert date to appropriate format

```{r}
DailyActivity$ActivityDate <- mdy(DailyActivity$ActivityDate)
HeartRate$Time  <- mdy_hms(HeartRate$Time)
SleepDay$SleepDay <- mdy(SleepDay$SleepDay)
```

```{r}
DailyActivity <- clean_names(DailyActivity)
HeartRate  <- clean_names(HeartRate)
SleepDay  <- clean_names(SleepDay)
```


## Check for unique IDs count

n_distinct(DailyActivity$id)
n_distinct(HeartRate$id)
n_distinct(SleepDay$id)

## Statistical summary for distance category

```{r}
DailyActivity %>%  
  select(very_active_distance,
         moderately_active_distance,
         light_active_distance,
         sedentary_active_distance) %>%
  summary()
```


Maximum distance: 28.030 and  Mean distance: 5.490

## Statistical summary for minutes category

```{r}
DailyActivity %>% 
  select(total_steps,
         very_active_minutes,
         fairly_active_minutes,
         lightly_active_minutes,
         sedentary_minutes,
         calories) %>% 
  summary()  
```


Max steps: 36019 (avg. 7638). Mean calories burned: 2304, max: 4900. (Note: Most female adults aim for 1,600–2,200 daily calories burnt and 6,000 to 10,000 steps)

## Statistical summary and visualization

```{r}
avg_intensity_levels <- DailyActivity %>%
            summarize(very_active_distance = mean(very_active_distance),
                      moderately_active_distance = mean(moderately_active_distance),
                      light_active_distance = mean(light_active_distance),
                      sedentary_active_distance = mean(sedentary_active_distance))

```

## Pivot from wide to long

```{r}
avg_intensity_levels_long <- avg_intensity_levels %>% 
        pivot_longer(cols = c(very_active_distance,
                               moderately_active_distance,
                              light_active_distance,
                               sedentary_active_distance ),
                      names_to = "avg_intensity_levels",
                      values_to = "kilometer")

avg_intensity_levels_long
```

## Visualization of avg_intensity_levels
### Install and loading ggplot

```{r}
install.packages("ggplot2")
library(ggplot2)

ggplot(data = avg_intensity_levels_long, 
       aes (x = avg_intensity_levels, y=kilometer)) +
       geom_col(fill= '#2e8540', color ='black') +
       xlab('Intensity Distance (category)') +
       ylab('Distance (km)') +
       labs(title = '"Daily Intensity vs. Distance',
       caption = 'Data FitBit from Kaggle',
       tag = 'Fig.1') + 
       theme(plot.tag.position = 'bottomleft')
```

![](https://github.com/Feronika-cloud/Feronika.github.io/blob/main/fig.%201%20Daily%20intensity%20vs.%20Distance.png)

According to the figure, people labeled as "light active" cover more distance in kilometers on average compared to those labeled as "very active."


## Visualization of avg_minutes_levels

```{r}
avg_minutes_levels <- DailyActivity %>%
  summarize(very_active_minutes = mean(very_active_minutes),
            fairly_active_minutes = mean(fairly_active_minutes),
            lightly_active_minutes = mean(lightly_active_minutes),
            sedentary_minutes = mean(sedentary_minutes))
avg_minutes_levels
```

## Pivot from wide to long

```{r}
avg_minutes_levels_long <- avg_minutes_levels %>% 
  pivot_longer(cols = c(very_active_minutes,
                        fairly_active_minutes,
                        lightly_active_minutes,
                        sedentary_minutes ),
               names_to = "avg_active_duration",
               values_to = "minutes")
avg_minutes_levels_long
```

## Visualization of  avg_minutes_levels

```{r}
ggplot(data = avg_minutes_levels_long, 
       aes (x = avg_active_duration, y=minutes)) +
  geom_col(fill= '#2e8540',color='black' ) +
  xlab('Minutes Distance (category)') +
  ylab('Time (minutes)') +
  labs(title = 'Daily Intensity vs. Minutes',
       caption = 'Data FitBit from Kaggle',
       tag = 'Fig.2') + 
  theme(plot.tag.position = 'bottomleft')
```
![](https://github.com/Feronika-cloud/Feronika.github.io/blob/main/fig.%202%20Daily%20Intensity%20vs.%20Minutes.png)

![](https://91b8cc23196046c99440157f83b4ab08.app.posit.cloud/file_show?path=%2Fcloud%2Fproject%2Ffig.+2+Daily+Intensity+vs.+Minutes.png)

Most individuals spend a significant amount of time engaged in sedentary activities, followed by lightly active pursuits. Encouraging more moderate and vigorous activity could promote better wellness.

## Discovering the relationship between  total steps and calories burnt per weeek
### Understanding the beginning and end of the observation.


```{r}
max_date <- max(DailyActivity$activity_date)
max_date
min_date <- min(DailyActivity$activity_date)
min_date
```

```{r}
daily_activity <- DailyActivity %>% 
  mutate(week = case_when(
    between(activity_date, as.Date('2016-04-12'), as.Date('2016-04-19'))~ 'week_1',
    between(activity_date, as.Date('2016-04-20'), as.Date('2016-04-27'))~ 'week_2',
    between(activity_date, as.Date('2016-04-28'), as.Date('2016-05-05'))~ 'week_3',
    between(activity_date, as.Date('2016-05-06'), as.Date('2016-05-13'))~ 'week_4',
    TRUE ~ 'NA'))
```

```{r}
calories_per_step <- daily_activity %>% 
  select(activity_date, total_steps, week,calories) %>%
  filter(week == 'week_1'|week == 'week_2'|week == 'week_3'|week == 'week_4')
```
```{r}
ggplot(data = calories_per_step, aes(x = total_steps, y =calories)) +
  geom_point(color = 'black') +
  geom_smooth(color = 'blue') +
  facet_wrap(~week) +
  xlab('Average daily steps') +
  ylab ('Calories burnt (kcals)') +
  labs(title = 'Calories burnt per daily steps',
      caption = 'Data FitBit from Kaggle',
      tag = 'Fig. 3') +
      theme(plot.tag.position = 'bottomleft')
```
![](https://github.com/Feronika-cloud/Feronika.github.io/blob/main/fig.%203%20Calories%20burnt%20per%20daily%20steps.png)

![](https://91b8cc23196046c99440157f83b4ab08.app.posit.cloud/file_show?path=%2Fcloud%2Fproject%2Ffig.+3+Calories+burnt+per+daily+steps.png)

There's a positive correlation between daily steps and calories burned. While the trendline initially dips towards the peak in the 3rd week, overall, the trend continues to move forward into the 4th week.

##  Visualization between total time in bed and total minutes sleep

```{r}
ggplot(data = SleepDay, aes(x = total_time_in_bed , y =total_minutes_asleep)) +
  geom_point(color = 'black') +
  geom_smooth(color = 'blue') +
  xlab('Total time in bed') +
  ylab ('Total minutes asleep') +
  labs(title = 'Total time in bed vs. Total minutes asleep',
       caption = 'Data FitBit from Kaggle',
       tag = 'Fig. 4') +
  theme(plot.tag.position = 'bottomleft')
```
![](https://github.com/Feronika-cloud/Feronika.github.io/blob/main/fig.%204%20Total%20time%20in%20bed%20vs.%20Total%20minutes%20asleep.png)

![](https://91b8cc23196046c99440157f83b4ab08.app.posit.cloud/graphics/f7d1d812-8d88-4b04-ba13-534b8a6d059e.png)

More time spent in bed correlates with a higher total duration of sleep, suggesting a positive relationship between time in bed and total sleep duration.


## Insight
Users cover more distance while lightly active (3.3km) compared to very active (1.5km), indicating that slow and steady activity yields greater distance. Additionally, users spend an average of 21 minutes very active per day and over 16 hours inactive

As daily steps increase, so do calories burned, suggesting a potential avenue for achieving desired weight goals.

## Recommendations to stakeholders:

-Offer free trial days to allow users to experience the app's benefits before committing to a subscription.

-Implement app notifications to encourage users to be more active, thus reducing inactive time.


