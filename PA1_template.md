---
title: "Assignment 1; Reproducible Research"
author: "R Hartman"
date: "29 april 2016"
output: html_document
---



###**Loading and preprocessing the data**

Show any code that is needed to

-Load the data (i.e. read.csv())  
-Process/transform the data (if necessary) into a format suitable for your analysis  
The script assumes the file is unzipped in your working directory.  

```r
data <- read.csv("activity.csv")
data$date <- as.Date(data$date, "%Y-%m-%d")
```
What is mean total number of steps taken per day?

For this part of the assignment, you can ignore the missing values in the dataset.

Calculate the total number of steps taken per day:

```r
steps.day <- tapply(data$steps, data$date, sum)
```
Make a histogram of the total number of steps taken each day.
Calculate and report the mean and median of the total number of steps taken per day.

```r
hist(steps.day, breaks = 8, xlab = NULL, main = "Number of steps taken each day")
```

![plot of chunk unnamed-chunk-3](figure/unnamed-chunk-3-1.png)

```r
mean(steps.day, na.rm = TRUE)
```

```
## [1] 10766.19
```

```r
median(steps.day, na.rm = TRUE)
```

```
## [1] 10765
```

###**What is the average daily activity pattern?**

Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis).

```r
mean.steps.interval <- tapply(data$steps, data$interval, mean, na.rm = TRUE)
plot(names(mean.steps.interval), mean.steps.interval, type = "l", xlab = "Minute", ylab = "Average steps", main = "Daily activity pattern")
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4-1.png)

Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?


```r
mean.steps.interval[which.max(mean.steps.interval)]
```

```
##      835 
## 206.1698
```

###**Imputing missing values**

Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.

Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

```r
sum(is.na(data))
```

```
## [1] 2304
```

Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.  
*The mean for the corresponding 5-minute interval will used to impute NA values!*  
Create a new dataset that is equal to the original dataset but with the missing data filled in.


```r
data2 <- data

missingvalues <- is.na(data2$steps)
mean.interval <- tapply(data2$steps, data2$interval, mean, na.rm= TRUE)
data2$steps[missingvalues] <- mean.interval[as.character(data2$interval[missingvalues])]
```

The following will check if there are still NA values present:

```r
sum(is.na(data2))
```

```
## [1] 0
```

Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?


```r
steps.day2 <- tapply(data2$steps, data2$date, sum)
hist(steps.day2, breaks = 8, xlab = NULL, main = "Number of steps taken each day")
```

![plot of chunk unnamed-chunk-9](figure/unnamed-chunk-9-1.png)

```r
mean(steps.day2, na.rm = TRUE)
```

```
## [1] 10766.19
```

```r
median(steps.day2, na.rm = TRUE)
```

```
## [1] 10766.19
```
*So we see that the imputing of data did not impact the overall data much.*

###**Are there differences in activity patterns between weekdays and weekends?**

For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.

Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.

*My R version uses the **Dutch** language for time and dates!! Apologies for the Dutch day names in defining the factor, but they correspond to Monday, Tuesday, Wednesday, Thursday, and Friday respectively!!*


```r
whatday <- factor(weekdays(data2$date) %in% c("maandag", "dinsdag", "woensdag", "donderdag", "vrijdag"), c(TRUE, FALSE), c("Weekday", "Weekendday"))
data2$day <- whatday
a <- as.data.frame(with(data2, tapply(steps, list(interval, day), mean)))
a$interval <- unique(data2$interval)
```

Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.


```r
par(mfrow = c(2,1), mar = c(4,4,2,2))
plot(a$interval, a$Weekday, type = "l", main = "Weekday", xlab= "Minute", ylab = "Average steps")
plot(a$interval, a$Weekendday, type = "l", main = "Weekend", xlab = "Minute", ylab = "Average steps")
```

![plot of chunk unnamed-chunk-11](figure/unnamed-chunk-11-1.png)
