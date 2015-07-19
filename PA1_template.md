# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data

Unzip the data if csv file is not available

```r
if(!file.exists("activity.csv")) unzip("activity.zip")
```

1. Load the data from csv file:

```r
activity = read.csv("activity.csv")
```

2. Parse the date using lubridate package

```r
require(lubridate)
activity$date = ymd(activity$date)
```

## What is mean total number of steps taken per day?
1. Using dplyr package, group the activity dataset by date variable

```r
require(dplyr)
activity_date <- group_by(activity,date)
```

2. Summarize the data by taking the sum of steps for each day and plot a histogram of the total number of steps per day

```r
activity_date <- summarize(activity_date,total = sum(steps))
hist(activity_date$total,breaks = 10,xlab = "total number of steps",main="Histogram of total number of steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-5-1.png) 


3. Mean of number of steps taken per day

```r
mean.date <- mean(activity_date$total,na.rm=TRUE)
```

```
## [1] 10766.19
```

4. Median of number of steps taken per day

```r
median.date <- median(activity_date$total,na.rm=TRUE)
```

```
## [1] 10765
```

## What is the average daily activity pattern?
Now, summarize data by interval variable and calculate the mean for each interval for all days. Again, using dplyr. Then, the resulting data ins plotted. 

```r
activity_int <- group_by(activity,interval)
activity_int <- summarize(activity_int,mean = mean(steps,na.rm=TRUE))
plot(activity_int$interval,activity_int$mean,type="l",xlab = "Interval",ylab = "Mean of steps taken")
```

![](PA1_template_files/figure-html/unnamed-chunk-10-1.png) 

Calculating the maximum mean of steps taken and in which interval it is assigned.

```r
maximum.steps <- max(activity_int$mean)
interval.max <- activity_int$interval[which.max(activity_int$mean)]
```
The maximum number the steps taken in an interval was 206.1698113 in the interval identified by 835. 

## Imputing missing values
1. The number of missing values in the activity dataset is 2304.

2. Duplicating the dataset.

```r
activity.imputed <- activity
#activity.imputed[is.na(activity.imputed$steps),1] <- activity_int[activity_int$interval==activity.imputed[is.na(activity.imputed$steps),3],2]
```

3. The strategy for filling NA values is to use the mean for respective interval averaged for all days. First, determine the NA rows in dataset and the respective intervals in these rows.

```r
na.rows <- which(is.na(activity.imputed$steps))
na.intervals <- activity.imputed[na.rows,3]
na.intervals.mean <- activity_int[match(na.intervals,activity_int$interval),2]
```

4. Now, imput the mean values in the new database

```r
activity.imputed[na.rows,1] <- na.intervals.mean
```

5. Grouping dy date and summarizing the imputed dataset

```r
activity.imputed_date <- group_by(activity.imputed,date)
activity.imputed_date <- summarize(activity.imputed_date,total = sum(steps))
hist(activity.imputed_date$total,breaks = 10,xlab = "total number of steps",main="Histogram of total number of steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-15-1.png) 

6. The mean of imputed dataset is: 

```r
mean.imputed.date <- mean(activity.imputed_date$total)
```

```
## [1] 10766.19
```

and the median is:

```r
median.imputed.date <- median(activity.imputed_date$total)
```

```
## [1] 10766.19
```

The imputed mean differs 0% from the original value. And the median differs -0.0110421%.

## Are there differences in activity patterns between weekdays and weekends?
