---
title: "Reproducible Research: Peer Assessment 1"
output: html_document
keep_md: true
---


## Loading and preprocessing the data

```{r echo = TRUE}

data <- read.csv("C:\\Users\\rache\\Downloads\\repdata_data_activity\\activity.csv")

```



## What is mean total number of steps taken per day?


```{r echo = TRUE}
library(dplyr)
stepsSum <- data %>%
  filter(!is.na(steps)) %>%
  group_by(date) %>%
  summarise(steps_sum = sum(steps))

```
 *histogramme
```{r echo = TRUE}
hist(stepsSum$steps_sum)
```
  *mean of steps per day
```{r echo = TRUE}
mean(stepsSum$steps_sum)
```
  *median of steps per day
```{r echo = TRUE}
median(stepsSum$steps_sum)
```



## What is the average daily activity pattern?

```{r echo = TRUE}
step5 <- data %>%
  filter(!is.na(steps)) %>%
  group_by(interval) %>%
  summarise(steps_mean = mean(steps))


plot(step5$interval,step5$steps_mean,type = "l", xlab = "interval", ylab = "steps mean")

max_interval <- step5 %>%
  filter(steps_mean==steps_mean)
```
  * maximum interval
```{r echo = TRUE}
max_interval$interval


```



## Imputing missing values

```{r echo = TRUE}

missing_data <- data %>%
  filter(is.na(steps))
```
  * number of missing data
```{r echo = TRUE}
length(missing_data[1])
```

```{r echo = TRUE}
data_complet <- data
iMeans<-by(data$steps,data$interval,mean,na.rm=TRUE)

data_complet[!complete.cases(data_complet),1]<-
  
  iMeans[as.character(data_complet[!complete.cases(data_complet),3])]
```
  * histogramme
```{r echo = TRUE}

hist(data_complet$steps)
```
  * mean of steps per day
```{r echo = TRUE}
mean(data_complet$steps)

```

  * median of stepsper day
```{r echo = TRUE}
median(data_complet$steps)

```


## Are there differences in activity patterns between weekdays and weekends?

```{r echo = TRUE}

day_of_week <- data$date %>%
  as.Date(format="%Y-%m-%d" ) %>%
  weekdays()

data_week <- cbind(data_complet, day_of_week)

weekend <- data_week %>%
  filter(day_of_week == "samedi" | day_of_week == "dimanche") %>%
  group_by(interval) %>%
  summarise(steps_mean = mean(steps))


week <- data_week %>%
  filter(day_of_week != "samedi" & day_of_week != "dimanche") %>%
  group_by(interval) %>%
  summarise(steps_mean = mean(steps))


par(mfrow=c(2,1))
plot(weekend$interval, weekend$steps_mean , type = "l")
plot(week$interval, week$steps_mean , type = "l")

```

