---
title: "Reproducible Research: Peer Assessment 1"
author: "Louis"
date: "8/18/2020"
output: html_document
---


## Loading and preprocessing the data
```{r, echo=TRUE}
library(lubridate)
library(ggplot2)
library(dplyr)
data <- read.csv("activity.csv")
data$date <- ymd(data$date)
data$steps <- as.numeric(data$steps)
```
## Part 1 : What is mean total number of steps taken per day?

1. Find the sum of number of total steps taken per day.
```{r, echo = TRUE}
the_sum <- with(data = data,aggregate(steps~date, FUN = sum, na.rm = TRUE))
```

2. Create Histogram
```{r, echo=TRUE}
sum_plot <- ggplot(data = the_sum, aes(steps))
sum_plot + geom_histogram(col = "Blue", fill = "Light Green") + labs(title = "Number of steps per day(Missing data ignored)", x = "Total steps taken per day", y = "The frequency of steps")
```

3. Find the Mean and Median
```{r,echo=TRUE}
sum_mean <- mean(the_sum$steps)
print(sum_mean)

sum_median <- median(the_sum$steps)
print(sum_median)
```

## Part 2 : What is the average daily activity pattern?

1. Mean of daily total steps taken
```{r, echo = TRUE}
total_mean <- with(data,aggregate(steps~interval, FUN = mean, na.rm = TRUE))

```
2. Plot the daily mean of data
```{r, echo = TRUE}
with(total_mean, plot(interval, steps, type = "l", main = "Average daily number of steps", xlab = "Interval of Steps", ylab = "Average number of steps"))
```

3. Maximum interval of mean
```{r,echo=TRUE}
total_mean[which.max(total_mean$steps),1]
```
## Part 3 : Imputing missing values

1. Sum of data with NA
```{r,echo=TRUE}
sum(is.na(data))
```
2. Mean by interval
```{r,echo=TRUE}
total_mean <- with(data,aggregate(steps~interval, FUN = mean, na.rm = TRUE))
per_mean <- mean(total_mean$steps)
```
3. Made the NA values equal to the mean per interval and created an identical dataset so that any changes made do not affect the original one.
```{r,echo=TRUE}
data1 <- data
data1$steps[is.na(data1$steps)] <- per_mean
```
4. Created a sum
```{r,echo=TRUE}
the_sum3 <- with(data1, aggregate(steps~date, FUN = sum))
```
5. Make a histogram with the changed NA values
```{r,echo=TRUE}
sum_plot3 <- ggplot(data = the_sum3,aes(steps))
sum_plot3 + geom_histogram(col = "Blue", fill = "Black") + labs(main = "Total number of steps per day (NA included)"
, x = "Total Steps per Day", y = "Frequency of steps taken per day")
ggsave("plot3.png")
```
6. Mean of the sum
```{r,echo=TRUE}
mean(the_sum3$steps)
```
7. Median of the sum
```{r,echo=TRUE}
median(the_sum3$steps)
```
Seems like both the mean and median are equal to one another now


## Part 4 : Are there differences in activity patterns between weekdays and weekends?

1. Made another identical dataset to the original as to ensure that any changes made do not affect the original.
```{r,echo=TRUE}
data2 <- data
```
2. Used lubridate to find which days was in the weekend or weekdays and created a new variable to insert the new data
after which I used cbind to add it into the cloned dataset.
```{r,echo=TRUE}
data2$weekday <- wday(data2$date, label = TRUE, abbr = TRUE)

weekends <- which(data2$weekday == "Sat" | data2$weekday == "Sun")
weekdays <- which(data2$weekday != "Sat" & data2$weekday != "Sun")

days_week <- c(rep("l", length(data2)))
days_week[weekends] <- "weekend"
days_week[weekdays] <- "weekday"
data3 <- cbind(data2, days_week)
```
3. Found the total mean for the data
```{r,echo=TRUE}
total_mean2 <- with(data3, aggregate(steps~interval + days_week, FUN = mean, na.rm = TRUE))
```
4. Plotted the total mean daily according to either the weekend or weekday.
```{r,echo=TRUE}
day_plot <- ggplot(total_mean2, aes(interval, steps))
day_plot + geom_line(col = "Blue") + facet_grid(days_week ~ .) + labs(main = "Weekday vs Weekend : Average daily number of steps", x = "Intervals", y = "Average number of steps per day")
```

