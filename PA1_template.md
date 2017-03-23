# Reproducible research course project week 2
Laternocode  
20 maart 2017  

This assignment will be described in multiple parts. I will write a report that answers the questions detailed below. Ultimately, the entire assignment is written in a single R markdown document that is processed by knitr and transformed into an HTML file.

#Assignment 1
- Load the data (i.e. read.csv())
- Process/transform the data (if necessary) into a format suitable for your analysis

###First of all we're going to read the data.


Unzip the data.

```r
unzip("repdata%2Fdata%2Factivity.zip")
```

Read the data.

```r
repdata <- read.csv(file = "activity.csv")
```

###Explor, clean and tidy the data.

```r
head(repdata)
```

```
##   steps       date interval
## 1    NA 2012-10-01        0
## 2    NA 2012-10-01        5
## 3    NA 2012-10-01       10
## 4    NA 2012-10-01       15
## 5    NA 2012-10-01       20
## 6    NA 2012-10-01       25
```

```r
str(repdata)
```

```
## 'data.frame':	17568 obs. of  3 variables:
##  $ steps   : int  NA NA NA NA NA NA NA NA NA NA ...
##  $ date    : Factor w/ 61 levels "2012-10-01","2012-10-02",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ interval: int  0 5 10 15 20 25 30 35 40 45 ...
```

```r
dim(repdata)
```

```
## [1] 17568     3
```

The class of date is "factor" and the class of interval is "integer" and we have some missing values.

Check how many NA's there are.

```r
repdata.missing <- repdata[is.na(repdata$steps),]
str(repdata.missing)
```

```
## 'data.frame':	2304 obs. of  3 variables:
##  $ steps   : int  NA NA NA NA NA NA NA NA NA NA ...
##  $ date    : Factor w/ 61 levels "2012-10-01","2012-10-02",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ interval: int  0 5 10 15 20 25 30 35 40 45 ...
```

Remove the missing values.

```r
repdata <- repdata[!is.na(repdata$steps),]
dim(repdata)
```

```
## [1] 15264     3
```

Change class date to "date".

```r
library(lubridate)
```

```
## 
## Attaching package: 'lubridate'
```

```
## The following object is masked from 'package:base':
## 
##     date
```

```r
repdata$date <- as.Date(repdata$date, format = "%Y-%m-%d")
str(repdata)
```

```
## 'data.frame':	15264 obs. of  3 variables:
##  $ steps   : int  0 0 0 0 0 0 0 0 0 0 ...
##  $ date    : Date, format: "2012-10-02" "2012-10-02" ...
##  $ interval: int  0 5 10 15 20 25 30 35 40 45 ...
```

#Assignment 2
What is mean total number of steps taken per day?

For this part of the assignment, you can ignore the missing values in the dataset.

1. Calculate the total number of steps taken per day
2. If you do not understand the difference between a histogram and a barplot, research the difference between them. Make a histogram of the total number of steps taken each day
3. Calculate and report the mean and median of the total number of steps taken per day

calculate the steps per day

```r
repdata.steppd <- aggregate(steps~date, repdata, sum)
head(repdata.steppd)
```

```
##         date steps
## 1 2012-10-02   126
## 2 2012-10-03 11352
## 3 2012-10-04 12116
## 4 2012-10-05 13294
## 5 2012-10-06 15420
## 6 2012-10-07 11015
```

Make a histogram for steps per day.

```r
hist(repdata.steppd$steps, main = "Histogram of the total steps per day taken", xlab = "Steps per day", ylab = "Frequency", col = "purple")
abline(v = mean(repdata.steppd$steps), col = "red")
abline(v = median(repdata.steppd$steps), col = "blue")
```

![](PA1_template_files/figure-html/unnamed-chunk-9-1.png)<!-- -->

Calculate the mean and median for total steps per day.

```r
mean(repdata.steppd$steps)
```

```
## [1] 10766.19
```

```r
median(repdata.steppd$steps)
```

```
## [1] 10765
```

#Assignment 3

What is the average daily activity pattern?

1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)
2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

Prepare data for plotting

```r
library(dplyr)
```

```
## 
## Attaching package: 'dplyr'
```

```
## The following objects are masked from 'package:lubridate':
## 
##     intersect, setdiff, union
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
repdata.steppd <- repdata %>%
  group_by(interval) %>%
  summarise(steps = mean(steps))
head(repdata.steppd)
```

```
## # A tibble: 6 × 2
##   interval     steps
##      <int>     <dbl>
## 1        0 1.7169811
## 2        5 0.3396226
## 3       10 0.1320755
## 4       15 0.1509434
## 5       20 0.0754717
## 6       25 2.0943396
```

Plot time series

```r
with(repdata.steppd, plot(interval, steps, type = "l", xlab = "Interval", ylab = "Steps", main = "Avarage daily steps by interval"))
```

![](PA1_template_files/figure-html/unnamed-chunk-12-1.png)<!-- -->

Which interval contains the maximun number of steps?

```r
which.max(repdata.steppd$steps)
```

```
## [1] 104
```
The 104th interval contains the maximum number of steps.

#Assignment4

Imputing missing values

Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.

1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)
2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.
3. Create a new dataset that is equal to the original dataset but with the missing data filled in.
4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

Read the data (again because I cleaned it earlier on)

```r
repdata.new <- read.csv(file = "activity.csv")
```

Check how many NA's there are.

```r
repdata.missing <- repdata.new[is.na(repdata.new$steps),]
str(repdata.missing)
```

```
## 'data.frame':	2304 obs. of  3 variables:
##  $ steps   : int  NA NA NA NA NA NA NA NA NA NA ...
##  $ date    : Factor w/ 61 levels "2012-10-01","2012-10-02",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ interval: int  0 5 10 15 20 25 30 35 40 45 ...
```

Replace Na's by the mean of the five minute interval

```r
for (i in repdata.steppd$interval) {
    repdata.new[repdata.new$interval == i & is.na(repdata.new$steps), ]$steps <- 
        repdata.steppd$steps[repdata.steppd$interval == i]
}
str(repdata.new)
```

```
## 'data.frame':	17568 obs. of  3 variables:
##  $ steps   : num  1.717 0.3396 0.1321 0.1509 0.0755 ...
##  $ date    : Factor w/ 61 levels "2012-10-01","2012-10-02",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ interval: int  0 5 10 15 20 25 30 35 40 45 ...
```

calculate the steps per day

```r
repdata.steppd.new <- aggregate(steps~date, repdata.new, sum)
head(repdata.steppd.new)
```

```
##         date    steps
## 1 2012-10-01 10766.19
## 2 2012-10-02   126.00
## 3 2012-10-03 11352.00
## 4 2012-10-04 12116.00
## 5 2012-10-05 13294.00
## 6 2012-10-06 15420.00
```

Make a histogram for steps per day.

```r
hist(repdata.steppd.new$steps, main = "Histogram of the total steps per day taken", xlab = "Steps per day", ylab = "Frequency", col = "purple")
abline(v = mean(repdata.steppd.new$steps), col = "red")
abline(v = median(repdata.steppd.new$steps), col = "blue")
```

![](PA1_template_files/figure-html/unnamed-chunk-18-1.png)<!-- -->

Calculate the mean and median for total steps per day.

```r
mean(repdata.steppd.new$steps)
```

```
## [1] 10766.19
```

```r
median(repdata.steppd.new$steps)
```

```
## [1] 10766.19
```
The mean of both calculations is the same but the median is a little bit different.

#Assignmet 5

Are there differences in activity patterns between weekdays and weekends?

For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.

1. Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.
2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.

Create factor variable column "weekdays".

```r
library(chron)
```

```
## 
## Attaching package: 'chron'
```

```
## The following objects are masked from 'package:lubridate':
## 
##     days, hours, minutes, seconds, years
```

```r
repdata.new$date <- as.Date(repdata.new$date, format = "%Y-%m-%d")
repdata.new$weekdays <- ifelse(weekdays(repdata.new$date) %in% c("zaterdag", "zondag"), "weekend", "weekday")
repdata.new$weekdays <- as.factor(repdata.new$weekdays)
head(repdata.new)
```

```
##       steps       date interval weekdays
## 1 1.7169811 2012-10-01        0  weekday
## 2 0.3396226 2012-10-01        5  weekday
## 3 0.1320755 2012-10-01       10  weekday
## 4 0.1509434 2012-10-01       15  weekday
## 5 0.0754717 2012-10-01       20  weekday
## 6 2.0943396 2012-10-01       25  weekday
```

```r
table(repdata.new$weekdays)
```

```
## 
## weekday weekend 
##   12960    4608
```

Create a panel plot 

```r
library(lattice)
repdata.new.agg <- aggregate(steps ~ interval + weekdays, data=repdata.new, mean)
head(repdata.new.agg)
```

```
##   interval weekdays      steps
## 1        0  weekday 2.25115304
## 2        5  weekday 0.44528302
## 3       10  weekday 0.17316562
## 4       15  weekday 0.19790356
## 5       20  weekday 0.09895178
## 6       25  weekday 1.59035639
```

```r
with(repdata.new.agg,
     xyplot(steps ~ interval | weekdays, type="l", xlab = "Interval", ylab = "Number of steps", layout = c(1, 2)))
```

![](PA1_template_files/figure-html/unnamed-chunk-21-1.png)<!-- -->

