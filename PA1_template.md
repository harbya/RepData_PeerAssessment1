---
output: 
  html_document:
  keep_md : true
  ---
##Title: "Reproducible Research: Peer Assessment 1"


## Loading and preprocessing the data

```r
activity <- read.csv("../RepData_PeerAssessment1/activity.csv")
```

## What is the mean total number of steps taken per day?

1. Make a histogram of the total number of steps taken each day


```r
SummaryPerDay <- aggregate(steps ~ date, data=activity, FUN=sum)
barplot(SummaryPerDay$steps, names.arg=SummaryPerDay$date, xlab="Total Steps each Day", ylab="Frequency",col="dark red")
```

![](PA1_template_files/figure-html/unnamed-chunk-2-1.png)<!-- -->

2. Calculate and report the **mean** and **median** total number of
   steps taken per day


```r
mean(SummaryPerDay$steps)
```

```
## [1] 10766.19
```

```r
median(SummaryPerDay$steps)
```

```
## [1] 10765
```

## What is the average daily activity pattern?

1. Make a time series plot (i.e. `type = "l"`) of the 5-minute
   interval (x-axis) and the average number of steps taken, averaged
   across all days (y-axis)


```r
SummaryInterval <- aggregate(steps ~ interval, data=activity, FUN=mean)
plot(SummaryInterval, type="l",col="red")
```

![](PA1_template_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

2. Which 5-minute interval, on average across all the days in the
   dataset, contains the maximum number of steps?


```r
SummaryInterval$interval[which.max(SummaryInterval$steps)]
```

```
## [1] 835
```


## Imputing missing values

1. Calculate and report the total number of missing values in the
   dataset (i.e. the total number of rows with `NA`s)


```r
sum(is.na(activity))
```

```
## [1] 2304
```

2. Devise a strategy for filling in all of the missing values in the
   dataset. The strategy does not need to be sophisticated. For
   example, you could use the mean/median for that day, or the mean
   for that 5-minute interval, etc.


3. Create a new dataset that is equal to the original dataset but with
   the missing data filled in.


```r
ActivityTidy <- activity
ActivityTidy$steps[is.na(ActivityTidy$steps)] <- 
    tapply(ActivityTidy$steps, ActivityTidy$interval, mean, na.rm = TRUE)
```


```r
sum(is.na(ActivityTidy))
```

```
## [1] 0
```

4. Make a histogram of the total number of steps taken each day and
   Calculate and report the **mean** and **median** total number of
   steps taken per day. Do these values differ from the estimates from
   the first part of the assignment? What is the impact of imputing
   missing data on the estimates of the total daily number of steps?

  

```r
SummaryDate <- aggregate(steps ~ date, data=activity, FUN=sum)
MeanValue = round(mean(SummaryDate$steps), 1)
MedianValue = round(median(SummaryDate$steps), 1)
barplot(SummaryDate$steps, names.arg=SummaryDate$date, xlab="Dates", ylab="Frequency of Steps",col="dark red")
abline(h=MedianValue, lwd = 3, col = 'yellow')
```

![](PA1_template_files/figure-html/unnamed-chunk-9-1.png)<!-- -->

	The mean and the median are quite close therefore the yellow line demarks both.



```r
mean(SummaryDate$steps)
```

```
## [1] 10766.19
```

```r
median(SummaryDate$steps)
```

```
## [1] 10765
```

The impact of the missing data seems rather low, at least when
estimating the total number of steps per day.


## Are there differences in activity patterns between weekdays and weekends?

1. Create a new factor variable in the dataset with two levels --
   "weekday" and "weekend" indicating whether a given date is a
   weekday or weekend day.


```r
DayType <- function(date) {
    if (weekdays(as.Date(date)) %in% c("Saturday", "Sunday")) {
        "weekend"
    } else {
        "weekday"
    }
}

ActivityTidy$DayType <- as.factor(sapply(activity$date, DayType))
```

2. Make a panel plot containing a time series plot (i.e. `type = "l"`)
   of the 5-minute interval (x-axis) and the average number of steps
   taken, averaged across all weekday days or weekend days
   (y-axis).


```r
par(mfrow=c(2,1))
for (type in c("weekend", "weekday")) {
    SummaryActivityType <- aggregate(steps ~ interval,
                            data=ActivityTidy,
							subset=ActivityTidy$DayType==type,
                            FUN=mean)
plot(SummaryActivityType, type="l", main=type,col="dark red")
}
```

![](PA1_template_files/figure-html/unnamed-chunk-12-1.png)<!-- -->
