# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data

1. Load the data (i.e. read.csv())

```r
 activity <- read.csv(unz("activity.zip", "activity.csv"))
```


2. Process/transform the data (if necessary) into a format suitable for your analysis

Converting the date column into a date format:

```r
activity$date <- as.Date(activity$date, "%Y-%m-%d")
```

## What is mean total number of steps taken per day?

Calculate the total number of steps taken per day

```r
steps_per_day <- aggregate(steps ~ date, data=activity, FUN=sum)
```


If you do not understand the difference between a histogram and a barplot, research the difference between them. Make a histogram of the total number of steps taken each day

```r
barplot(steps_per_day$steps, xlab="Date", ylab="Total number of steps", names.arg = steps_per_day$date, main="Number of steps taken per day")
```

![](PA1_template_files/figure-html/unnamed-chunk-4-1.png) 

Calculate and report the mean and median of the total number of steps taken per day

Mean:

```r
mean(steps_per_day$steps)
```

```
## [1] 10766.19
```

Median:

```r
median(steps_per_day$steps)
```

```
## [1] 10765
```

## What is the average daily activity pattern?
1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)


```r
steps_per_interval <- aggregate(steps ~ interval, data=activity, FUN=mean)
```


```r
plot(steps ~interval, steps_per_interval, type="l", xlab="Interval", ylab="Average number of steps", main="Average number of steps per interval")
```

![](PA1_template_files/figure-html/unnamed-chunk-8-1.png) 

## Imputing missing values

1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)


```r
sum(!complete.cases(activity))
```

```
## [1] 2304
```

2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.


```r
merged_activity <- merge(activity, steps_per_interval, by="interval")
 merged_activity[is.na(merged_activity$steps.x), 2] <- merged_activity[is.na(merged_activity$steps.x), 4]
```
 
3. Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
 new_activity <- merged_activity[,1:3]
```

4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?


```r
 new_steps_per_day <- aggregate(steps.x ~ date, data=new_activity, FUN=sum)
 barplot(new_steps_per_day$steps, xlab="Date", ylab="Total number of steps", names.arg = new_steps_per_day$date, main="Number of steps taken per day")
```

![](PA1_template_files/figure-html/unnamed-chunk-12-1.png) 

Mean:

```r
mean(new_steps_per_day$steps.x)
```

```
## [1] 10766.19
```

Median:

```r
median(new_steps_per_day$steps.x)
```

```
## [1] 10766.19
```

The mean is the same as before. The median is now equal to the mean.

## Are there differences in activity patterns between weekdays and weekends?

1. Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.


```r
library(lubridate)
```

```
## Warning: package 'lubridate' was built under R version 3.2.2
```

```r
new_activity$dayType <- ifelse(wday(new_activity$date) %in% c(6,7),"Weekend", "Weekday")
```

2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.


```r
new_activity_weekday <- new_activity[new_activity$dayType=="Weekday",]
new_activity_weekend <- new_activity[new_activity$dayType=="Weekend",]

steps_per_interval_weekday <- aggregate(steps.x ~ interval, data=new_activity_weekday, FUN=mean)
steps_per_interval_weekend <- aggregate(steps.x ~ interval, data=new_activity_weekend, FUN=mean)

par(mfrow = c(2,1))

plot(steps.x ~interval, steps_per_interval_weekend, type="l",  main="Weekend")
plot(steps.x ~interval, steps_per_interval_weekday, type="l",  main="Weekday")
```

![](PA1_template_files/figure-html/unnamed-chunk-16-1.png) 
