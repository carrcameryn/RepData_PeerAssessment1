---
title: "Reproducible Research: Peer Assessment 1"
output: 
  html_document:
    keep_md: true
---


## Loading and preprocessing the data


```r
library(dplyr)
```

```
## 
## Attaching package: 'dplyr'
```

```
## The following objects are masked from 'package:stats':
## 
##     filter, lag
```

```
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
```

```r
library(ggplot2)
library(chron)

activity <- read.csv("~/Documents/GitHub/RepData_PeerAssignment1/activity.csv")
```


## What is mean total number of steps taken per day?
Calculate the total number of steps taken per day
Make a histogram of the total number of steps taken each day
Calculate and report the mean and median of the total number of steps taken per day

```r
#Calculate total steps per day
total_steps <- activity %>%
        group_by(date) %>%
        summarise(total_steps = sum(steps, na.rm=TRUE))

#histogram of total steps per day
hist(total_steps$total_steps, main="Total Steps Per Day", xlab="Steps Taken")
```

![](PA1_template_files/figure-html/unnamed-chunk-2-1.png)<!-- -->

The mean of total steps per day:

```r
mean(total_steps$total_steps, na.rm=TRUE)
```

```
## [1] 9354.23
```

The median of total  steps per day:

```r
median(total_steps$total_steps, na.rm=T)
```

```
## [1] 10395
```

        
      

## What is the average daily activity pattern?
Make a time series plot of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```r
interval_steps <- activity %>%
        group_by(interval) %>%
        summarise(mean_steps = mean(steps, na.rm=TRUE))

plot(interval_steps$interval, interval_steps$mean_steps,type="l", xlab = "Interval", ylab= "Mean Steps", main = "Mean Number of Steps by Interval")
```

![](PA1_template_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
interval_steps[which.max(interval_steps$mean_steps), ]$interval
```

```
## [1] 835
```



## Imputing missing values
Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)
Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.
Create a new dataset that is equal to the original dataset but with the missing data filled in.
Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

```r
sum(is.na(activity$steps))
```

```
## [1] 2304
```

Fill in NAs with zeros & create histogram 

```r
activity2 <- activity
activity2[is.na(activity2)] <- 0

total_steps2 <- activity2 %>%
        group_by(date) %>%
        summarise(total_steps = sum(steps, na.rm=TRUE))

#histogram of total steps per day
hist(total_steps2$total_steps, main="Total Steps Per Day (NAs replaced)", xlab="Steps Taken")
```

![](PA1_template_files/figure-html/unnamed-chunk-8-1.png)<!-- -->

Mean steps per day

```r
mean(total_steps2$total_steps)
```

```
## [1] 9354.23
```

Median steps per day

```r
median(total_steps2$total_steps)
```

```
## [1] 10395
```


## Are there differences in activity patterns between weekdays and weekends?

```r
weekend <- activity$date[is.weekend(activity$date)]
activity$weekend <- ifelse(activity$date %in% weekend,"Weekend","Weekday")

weekend_steps <- activity %>%
                 group_by(interval, weekend) %>%
                 summarise(mean_steps = mean(steps, na.rm=T))

ggplot(weekend_steps, aes(x= interval, y= mean_steps, color= weekend)) + geom_line() + labs(title = "Mean Steps by Date Type", x = "Interval", y = "Mean Steps") + facet_wrap(~weekend, ncol=1, nrow=2)
```

![](PA1_template_files/figure-html/unnamed-chunk-11-1.png)<!-- -->

