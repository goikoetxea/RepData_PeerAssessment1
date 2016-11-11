# Reproducible Research: Peer Assessment 1


```
## Loading required package: lattice
```

```
## Loading required package: survival
```

```
## Loading required package: Formula
```

```
## 
## Attaching package: 'Hmisc'
```

```
## The following objects are masked from 'package:base':
## 
##     format.pval, round.POSIXt, trunc.POSIXt, units
```
#Loading and preprocessing the data
## 1. Load the data (i.e. read.csv())

```r
data <- read.csv("activity.csv")
```
#What is mean total number of steps taken per day?
## 1. Calculate the total number of steps taken per day

```r
steps_per_day <- tapply(data$steps, data$date, sum, na.rm=TRUE)
```

## 2. Make a histogram of the total number of steps taken each day

```r
qplot(steps_per_day, xlab = "Total steps per day", binwidth = 500)
```

![](PA1_template_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

## 3. Calculate and report the mean and median of the total number of steps taken per day

```r
mean_steps_per_day <- mean(steps_per_day)
median_steps_per_day <- median(steps_per_day)
```
Mean: 9354.23
Median: 10395

#What is the average daily activity pattern?
## 1. Make a time series plot of the 5-minute interval and the average number of steps taken, averaged across all days

```r
average_steps_per_interval <- aggregate(x=list(mean_steps=data$steps), by=list(interval=data$interval), FUN=mean, na.rm=TRUE)
ggplot(average_steps_per_interval, aes(x=interval, y=mean_steps)) + geom_line() + 
xlab("time interval")+ ylab("average number of steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-6-1.png)<!-- -->

## 2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
maximum_interval <-average_steps_per_interval[which.max(average_steps_per_interval$mean_steps),]
```
Interval 835, mean_steps:206.1698

# Imputing missing values
## 1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with 𝙽𝙰s)

```r
number_of_missing_values <- length(which(is.na(data$steps)))
```
2304

## 2. Devise a strategy for filling in all of the missing values in the dataset
substitute the missing value by the mean

## 3. Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
data_imputed <- data
data_imputed$steps <- impute(data$steps, mean)
```

## 4. Make a histogram of the total number of steps taken each day and report the mean and median total number of steps taken per day.

```r
steps_per_day_imputed <- tapply(data_imputed$steps, data_imputed$date, sum, na.rm=TRUE)
qplot(steps_per_day_imputed, xlab = "Total steps per day imputed", binwidth = 500)
```

![](PA1_template_files/figure-html/unnamed-chunk-10-1.png)<!-- -->

```r
mean_steps_per_day_imputed <- mean(steps_per_day_imputed)
median_steps_per_day_imputed <- median(steps_per_day_imputed)
```
Mean: 10766.19
Median: 10766.19

#Are there differences in activity patterns between weekdays and weekends?
## 1. Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.

```r
data_imputed$day <- ifelse(as.POSIXlt(data_imputed$date)$wday %in% c(0,6), 'weekend', 'weekday')
```

## 2. Make a panel plot containing a time series plot (i.e. 𝚝𝚢𝚙𝚎 = "𝚕") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis).

```r
average_number_of_steps_per_weekday <- aggregate(steps ~ interval + day, data=data_imputed, mean)
ggplot(average_number_of_steps_per_weekday, aes(x= interval, y= steps)) + 
  geom_line() + facet_grid(day ~ .) +
xlab("5 minute interval") + ylab("average number of steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-12-1.png)<!-- -->
