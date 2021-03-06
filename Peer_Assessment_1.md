# Reproducible Research Peer Assessment 1

###Loading and preprocessing the data

Load the data


```r
data <- read.csv("activity.csv")
```

###What is mean total number of steps taken per day?

- Calculate the total number of steps taken per day
- Make a histogram of the total number of steps taken each day
- Calculate and report the mean and median of the total number of steps taken per day.


```r
steps_by_day <- aggregate(steps ~ date, data, sum)
hist(steps_by_day$steps, main = paste("Total Steps Each Day"), col="blue", xlab="Number of Steps")
```

![](Peer_Assessment_1_files/figure-html/unnamed-chunk-2-1.png) 

```r
data_mean <- mean(steps_by_day$steps)
data_median <- median(steps_by_day$steps)
print(data_mean)
```

```
## [1] 10766.19
```

```r
print(data_median)
```

```
## [1] 10765
```

mean is data_mean.

median is data_median.

###What is the average daily activity pattern?

- Calculate the average steps for each interval for all days
- Plot the average number of steps per day by interval
- Find interval with maximum number of average steps


```r
steps_by_interval <- aggregate(steps ~ interval, data, mean)

plot(steps_by_interval$interval,steps_by_interval$steps, type="l", xlab="Interval", ylab="Number of Steps",main="Average Number of Steps per Day by Interval")
```

![](Peer_Assessment_1_files/figure-html/unnamed-chunk-3-1.png) 

```r
max_interval <- steps_by_interval[which.max(steps_by_interval$steps),1]
max_interval
```

```
## [1] 835
```

maximum number of average steps is max_interval.

###Imputing missing values

- Calculate the total number of missing values in the dataset
- Create a new dataset with the missing data filled in

New dataset is created by replacing the missing data values with the average of each interval.
Zeros were set for 1st of Oct as it is the first day of the measurement.    


```r
incomplete <- sum(!complete.cases(data))
new_data <- transform(data, steps = ifelse(is.na(data$steps), steps_by_interval$steps[match(data$interval, steps_by_interval$interval)], data$steps))
new_data[as.character(new_data$date) == "2012-10-01", 1] <- 0
```

- Make a histogram of the total number of steps taken each day 
- Calculate the mean and median total number of steps taken per day


```r
steps_by_day_i <- aggregate(steps ~ date, new_data, sum)
hist(steps_by_day_i$steps, main = paste("Total Steps Each Day"), col="blue", xlab="Number of Steps")

#Create Histogram to show difference. 
hist(steps_by_day$steps, main = paste("Total Steps Each Day"), col="red", xlab="Number of Steps", add=T)
legend("topright", c("Imputed", "Non-imputed"), col=c("blue", "red"), lwd=10)
```

![](Peer_Assessment_1_files/figure-html/unnamed-chunk-5-1.png) 

```r
new_mean <- mean(steps_by_day_i$steps)
new_median <- median(steps_by_day_i$steps)
mean_diff <- new_mean - data_mean
med_diff <- new_median - data_median
total_diff <- sum(steps_by_day_i$steps) - sum(steps_by_day$steps)
print(new_mean)
```

```
## [1] 10589.69
```

```r
print(new_median)
```

```
## [1] 10766.19
```

```r
print(mean_diff)
```

```
## [1] -176.4949
```

```r
print(med_diff)
```

```
## [1] 1.188679
```

```r
print(total_diff)
```

```
## [1] 75363.32
```

new mean is new_mean.

new median is new_median.

mean differences is mean_diff.

median differences is med_diff.

total differences is total_diff.

###Are there differences in activity patterns between weekdays and weekends?

-Create a plot to compare the number of steps between the week and weekend.


```r
weekdays <- c("Monday", "Tuesday", "Wednesday", "Thursday", 
              "Friday")
new_data$dow = as.factor(ifelse(is.element(weekdays(as.Date(new_data$date)),weekdays), "Weekday", "Weekend"))

steps_by_interval_i <- aggregate(steps ~ interval + dow, new_data, mean)

library(lattice)

xyplot(steps_by_interval_i$steps ~ steps_by_interval_i$interval|steps_by_interval_i$dow, main="Average Steps per Day by Interval",xlab="Interval", ylab="Steps",layout=c(1,2), type="l")
```

![](Peer_Assessment_1_files/figure-html/unnamed-chunk-6-1.png) 


