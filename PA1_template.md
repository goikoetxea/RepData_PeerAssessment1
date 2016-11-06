# Reproducible Research: Peer Assessment 1


```r
library(ggplot2)
library(Hmisc)
```

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

```r
library(knitr)

data <- read.csv("activity.csv")

#Calculate the total number of steps taken per day
steps_per_day <- tapply(data$steps, data$date, sum, na.rm=TRUE)

#Make a histogram of the total number of steps taken each day
qplot(steps_per_day, xlab = "Total steps per day", binwidth = 500)
```

![](PA1_template_files/figure-html/unnamed-chunk-1-1.png)<!-- -->

```r
#Calculate and report the mean and median of the total number of steps taken per day
mean_steps_per_day <- mean(steps_per_day)
median_steps_per_day <- median(steps_per_day)

#Make a time series plot (i.e. 𝚝𝚢𝚙𝚎 = "𝚕") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)
average_steps_per_interval <- aggregate(x=list(mean_steps=data$steps), by=list(interval=data$interval), FUN=mean, na.rm=TRUE)
ggplot(average_steps_per_interval, aes(x=interval, y=mean_steps)) + geom_line() + xlab("time interval")+ ylab("average number of steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-1-2.png)<!-- -->

```r
#Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?
maximum_interval <-average_steps_per_interval[which.max(average_steps_per_interval$mean_steps),]

#Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with 𝙽𝙰s)
number_of_missing_values <- length(which(is.na(data$steps)))

#Devise a strategy for filling in all of the missing values in the dataset
#Create a new dataset that is equal to the original dataset but with the missing data filled in.
data_imputed <- data
data_imputed$steps <- impute(data$steps, mean)

#Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. 
steps_per_day_imputed <- tapply(data_imputed$steps, data_imputed$date, sum, na.rm=TRUE)
qplot(steps_per_day_imputed, xlab = "Total steps per day imputed", binwidth = 500)
```

![](PA1_template_files/figure-html/unnamed-chunk-1-3.png)<!-- -->

```r
mean_steps_per_day_imputed <- mean(steps_per_day_imputed)
median_steps_per_day_imputed <- median(steps_per_day_imputed)

#Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.
data_imputed$day <- ifelse(as.POSIXlt(data_imputed$date)$wday %in% c(0,6), 'weekend', 'weekday')

#Make a panel plot containing a time series plot (i.e. 𝚝𝚢𝚙𝚎 = "𝚕") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). 
average_number_of_steps_per_weekday <- aggregate(steps ~ interval + day, data=data_imputed, mean)
ggplot(average_number_of_steps_per_weekday, aes(x= interval, y= steps)) + 
  geom_line() + facet_grid(day ~ .) +
xlab("5 minute interval") + ylab("average number of steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-1-4.png)<!-- -->