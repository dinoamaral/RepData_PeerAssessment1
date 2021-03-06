# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data

```r
unzip("activity.zip")
activity <- read.csv("activity.csv")
str(activity)
```

```
## 'data.frame':	17568 obs. of  3 variables:
##  $ steps   : int  NA NA NA NA NA NA NA NA NA NA ...
##  $ date    : Factor w/ 61 levels "2012-10-01","2012-10-02",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ interval: int  0 5 10 15 20 25 30 35 40 45 ...
```

```r
activity$date <- as.Date(activity$date)
```

## What is mean total number of steps taken per day?
For this part of the assignment, you can ignore the missing values in the dataset.

1. Make a histogram of the total number of steps taken each day


```r
aggregacti <- aggregate(steps ~ date, data = activity, FUN = sum)
barplot(aggregacti$steps, names.arg = aggregacti$date, col = "lightblue",
        xlab = "Date", ylab = "Number of Steps", 
        main="Total numbers of steps taken per day")           
```

![](PA1_files/figure-html/unnamed-chunk-2-1.png) 

2. Calculate and report the mean and median total number of steps taken per day

```r
mean(aggregacti$steps)
```

```
## [1] 10766.19
```


```r
median(aggregacti$steps)
```

```
## [1] 10765
```

## What is the average daily activity pattern?

1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)


```r
aggreg_steps_interval <- aggregate(steps ~ interval, data = activity, FUN = mean)
plot(aggreg_steps_interval, type="l", col = "darkblue")
```

![](PA1_files/figure-html/unnamed-chunk-5-1.png) 

2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?


```r
aggreg_steps_interval$interval[which.max(aggreg_steps_interval$steps)]
```

```
## [1] 835
```

## Imputing missing values
Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.

1. Calculate and report the total number of missing values in the dataset
(i.e. the total number of rows with NAs)

```r
sum(is.na(activity))
```

```
## [1] 2304
```

2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

* For this assessment, it will be use the "mean" for the 5-minute intervals as fillers for missing values.

3. Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
for (i in 1:nrow(activity))
{    if (is.na(activity$steps[i]) == TRUE)
        {activity$steps2[i] <- mean(activity$steps, na.rm = TRUE)} 
      else {activity$steps2[i] <- activity$steps[i]}
}
```

4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

```r
aggreg2 <- aggregate(steps2 ~ date, data = activity, FUN = sum)
barplot(aggreg2$steps, names.arg = aggreg2$date, col = "lightblue",
        xlab = "Date", ylab = "Number of Steps", 
        main="Total numbers of steps(with missing data filled in) taken per day") 
```

![](PA1_files/figure-html/unnamed-chunk-9-1.png) 


```r
mean(aggreg2$steps2)
```

```
## [1] 10766.19
```

```r
median(aggreg2$steps2)
```

```
## [1] 10766.19
```
The impact of the missing data seems to be not significant, at least when comparing the numbers of
steps taken by day..

## Are there differences in activity patterns between weekdays and weekends?

For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.

1. Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.


```r
for (i in 1:nrow(activity))
{    if (weekdays(activity$date)[i] %in% c("Saturday", "Sunday"))
        {activity$daytype[i] <- "weekend"}
     else {activity$daytype[i] <- "weekday"}
}
activity$daytype <- as.factor(activity$daytype)
```

2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). The plot should look something like the following, which was creating using simulated data:


```r
par(mfrow = c(2, 1))
for (daytype in c("weekend", "weekday")) {
    steps.daytype <- aggregate(steps2 ~ interval, data = activity, subset = activity$daytype == 
        daytype, FUN = mean)
    plot(steps.daytype, type = "l", main = daytype)
}
```

![](PA1_files/figure-html/unnamed-chunk-12-1.png) 
