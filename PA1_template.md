---
title: "Reproducible Research: Peer Assessment 1"
output: 
  html_document:
    keep_md: true
---


## Loading and preprocessing the data

Show any code that is needed to

1. Load the data (i.e. read.csv())


```r
data <- read.csv(unz("activity.zip", "activity.csv"), colClasses = c(date = "Date"))
```

2. Process/transform the data (if necessary) into a format suitable for your analysis

## What is mean total number of steps taken per day?

For this part of the assignment, you can ignore the missing values in the dataset.

1. Make a histogram of the total number of steps taken each day


```r
steps.per.day <- with(data, tapply(steps, date, sum, na.rm = TRUE))
hist(steps.per.day, breaks = 10, xlab = "Steps per Day")
```

![](PA1_template_files/figure-html/unnamed-chunk-2-1.png)<!-- -->

2. Calculate and report the mean and median total number of steps taken per day


```r
mean.step <- mean(steps.per.day)
median.step <- median(steps.per.day)
```

The mean steps per day is 9354, and the median is 10395

## What is the average daily activity pattern?

1.Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)


```r
average.daily.steps <- with(data, tapply(steps, interval, mean, na.rm = TRUE))
plot((names(average.daily.steps)), average.daily.steps, type = "l",
     xlab = "5-minute interval",
     ylab = "Average Daily Steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?


```r
max <- names(which.max(average.daily.steps))
```

The maximum interval is 835

## Imputing missing values

Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.

1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)


```r
total.NAs <- sum(is.na(data))
```

There are 2304 missing values.

2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

3. Create a new dataset that is equal to the original dataset but with the missing data filled in.


```r
average.daily.steps <- as.data.frame(average.daily.steps)
average.daily.steps$interval <- as.numeric(rownames(average.daily.steps))

imputed.data <- data
for(i in 1:nrow(data)){
     if(is.na(data$steps[i])){
          imputed.data$steps[i] <- average.daily.steps[average.daily.steps$interval == imputed.data$interval[i], ]$average.daily.steps
     }
}
```

4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?



```r
steps.per.day2 <- with(imputed.data, tapply(steps, date, sum, na.rm = TRUE))
hist(steps.per.day2, breaks = 10, xlab = "Steps per Day")
```

![](PA1_template_files/figure-html/unnamed-chunk-8-1.png)<!-- -->

```r
mean.step2 <- mean(steps.per.day2)
median.step2 <- median(steps.per.day2)
```

The mean steps per day is 10766, and the median is 10766

## Are there differences in activity patterns between weekdays and weekends?

For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.

1. Create a new factor variable in the dataset with two levels -- "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.


```r
aux <- function(date){
     if(weekdays(date) == "Saturday" | weekdays(date) == "Sunday"){
          "weekend"
     }
     else "weekday"
}
imputed.data$day.type <- as.factor(with(imputed.data, sapply(date, aux)))
```

2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis).


```r
average.daily.steps2 <- with(imputed.data, tapply(steps, list(interval, day.type), mean))

average.daily.steps2 <- as.data.frame(average.daily.steps2)

par(mfrow = c(2, 1))
plot((rownames(average.daily.steps2)), average.daily.steps2$weekday,
     type = "l",
     xlab = "5-minute interval",
     main = "Average Daily Steps on Weekday",
     ylab = "Average Daily Steps")

plot((rownames(average.daily.steps2)), average.daily.steps2$weekend,
     type = "l",
     xlab = "5-minute interval",
     main = "Average Daily Steps on Weekday",
     ylab = "Average Daily Steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-10-1.png)<!-- -->
