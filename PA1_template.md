---
title: "assignment 1 week 2"
author: "habiba"
date: "2 October 2017"
output: md_document
---



## Coursera, Reproducable Research Week 2 Assignment  

###1-Load the data (i.e. read.csv()) 


```r
setwd("D:/Coursera data Science/Reproducable Research/week 2assignment/repdata%2Fdata%2Factivity")
library(dplyr)
activity<-read.csv("./activity.csv")
str(activity)
```

```
## 'data.frame':	17568 obs. of  3 variables:
##  $ steps   : int  NA NA NA NA NA NA NA NA NA NA ...
##  $ date    : Factor w/ 61 levels "2012-10-01","2012-10-02",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ interval: int  0 5 10 15 20 25 30 35 40 45 ...
```

```r
head(activity)
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
summary(activity)
```

```
##      steps                date          interval     
##  Min.   :  0.00   2012-10-01:  288   Min.   :   0.0  
##  1st Qu.:  0.00   2012-10-02:  288   1st Qu.: 588.8  
##  Median :  0.00   2012-10-03:  288   Median :1177.5  
##  Mean   : 37.38   2012-10-04:  288   Mean   :1177.5  
##  3rd Qu.: 12.00   2012-10-05:  288   3rd Qu.:1766.2  
##  Max.   :806.00   2012-10-06:  288   Max.   :2355.0  
##  NA's   :2304     (Other)   :15840
```
###2-Process/transform the data (if necessary) into a format suitable for your analysis

```r
activity.complete<-na.omit(activity)
```
## What is mean total number of steps taken per day?
For this part of the assignment, you can ignore the missing values in the dataset.

Calculate the total number of steps taken per day

```r
activity.day<-group_by(activity.complete,date)
activity.day<-summarise(activity.day,steps=sum(steps))
summary(activity.day)
```

```
##          date        steps      
##  2012-10-02: 1   Min.   :   41  
##  2012-10-03: 1   1st Qu.: 8841  
##  2012-10-04: 1   Median :10765  
##  2012-10-05: 1   Mean   :10766  
##  2012-10-06: 1   3rd Qu.:13294  
##  2012-10-07: 1   Max.   :21194  
##  (Other)   :47
```

###If you do not understand the difference between a histogram and a barplot, research the difference between them. Make a histogram of the total number of steps taken each day

```r
library(ggplot2)
qplot(steps,data = activity.day)
```

```
## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4-1.png)
###Calculate and report the mean and median of the total number of steps taken per day


```r
mean(activity.day$steps)
```

```
## [1] 10766.19
```

```r
median(activity.day$steps)
```

```
## [1] 10765
```
###What is the average daily activity pattern
Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```r
activity.interval<-group_by(activity.complete,interval)
activity.interval<-summarise(activity.interval,steps=mean(steps))
summary(activity.interval)
```

```
##     interval          steps        
##  Min.   :   0.0   Min.   :  0.000  
##  1st Qu.: 588.8   1st Qu.:  2.486  
##  Median :1177.5   Median : 34.113  
##  Mean   :1177.5   Mean   : 37.383  
##  3rd Qu.:1766.2   3rd Qu.: 52.835  
##  Max.   :2355.0   Max.   :206.170
```
###Next we plot the average daily steps against the intervals:

```r
ggplot(activity.interval,aes(interval,steps))+geom_line()
```

![plot of chunk unnamed-chunk-7](figure/unnamed-chunk-7-1.png)

###Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
activity.interval[activity.interval$steps==max(activity.interval$steps),]
```

```
## # A tibble: 1 x 2
##   interval    steps
##      <int>    <dbl>
## 1      835 206.1698
```
###Imputing missing values
Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.

Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)
## The total number of rows with NAs is equal to the difference
#between the number of rows in the raw data and 
#the number of rows in the data with only complete cases:


```r
nrow(activity)-nrow(activity.complete)
```

```
## [1] 2304
```

###Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.


```r
names(activity.interval)[2]<-"mean.steps"

activity.impute<-merge(activity,activity.interval)
```

###Create a new dataset that is equal to the original dataset but with the missing data filled in.


```r
activity.impute$steps[is.na(activity.impute$steps)]<-activity.impute$mean.steps[is.na(activity.impute$steps)]
```
###Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

the summary and plots are as follows as well


```r
activity.day.impute<-group_by(activity.impute,date)
activity.day.impute<-summarise(activity.day.impute,steps=sum(steps))

qplot(steps,data = activity.day.impute)
```

```
## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
```

![plot of chunk unnamed-chunk-12](figure/unnamed-chunk-12-1.png)

```r
mean(activity.day.impute$steps)
```

```
## [1] 10766.19
```

```r
median(activity.day.impute$steps)
```

```
## [1] 10766.19
```
###Are there differences in activity patterns between weekdays and weekends?
For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.

Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.


```r
activity.impute$daysofweek<-weekdays(as.Date(activity.impute$date))
activity.impute$weekend<-as.factor(activity.impute$daysofweek=="Saturday"|activity.impute$daysofweek=="Sunday")
levels(activity.impute$weekend)<-c("Weekdays","Weekend")
```

###Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.

the plot is below as well

```r
activity.weekday<-activity.impute[activity.impute$weekend=="Weekdays",]
activity.weekend<-activity.impute[activity.impute$weekend=="Weekend",]
activity.interval.weekday<-group_by(activity.weekday,interval)

activity.interval.weekday<-summarise(activity.interval.weekday,steps=mean(steps))

activity.interval.weekday$weekend<-"Weekday"

activity.interval.weekend<-group_by(activity.weekend,interval)

activity.interval.weekend<-summarise(activity.interval.weekend,steps=mean(steps))

activity.interval.weekend$weekend<-"Weekend"

act.int<-rbind(activity.interval.weekday,activity.interval.weekend)

act.int$weekend<-as.factor(act.int$weekend)
ggplot(act.int,aes(interval,steps))+geom_line()+facet_grid(weekend~.)
```

![plot of chunk unnamed-chunk-14](figure/unnamed-chunk-14-1.png)

