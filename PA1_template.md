# Assignment1
#Peer Assessment 1


##Loading and preprocessing the data:


```r
#script should already be in the appropriate working directory
data <- read.csv("activity.csv")
#changing the date feature to date format
library(lubridate)
data$date <- ymd(as.character(data$date))
```

##What is mean total number of steps taken per day?

This is a histogram of the total number of steps taken each day

```r
#sum the steps for each day
totalperday <- tapply(data$steps, data$date,sum, na.rm=T)
hist(totalperday, xlab="Number of steps", main="histogram of the total number of steps taken each day")
```

![](./PA1_template_files/figure-html/hist1-1.png) 

The mean and median total number of steps taken per day are reported below respectively:


```r
mean(totalperday, na.rm=T)
```

```
## [1] 9354.23
```

```r
median(totalperday,na.rm=T)
```

```
## [1] 10395
```

##What is the average daily activity pattern?
A time series plot of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis) is shown below:


```r
#this gives me the average number of steps taken acorss all days, with the names being the x-axis values
timeseries <- tapply(data$steps,data$interval,mean, na.rm=T)
#to convert timeseries to a vector for better plotting
plot(names(timeseries), timeseries, type='l', ylab="Average steps", xlab="5min intervals")
```

![](./PA1_template_files/figure-html/timeseries-1.png) 

The 5-minute interval, on average across all the days in the dataset, which contains the maximum number of steps is:


```r
names(timeseries)[timeseries==max(timeseries)]
```

```
## [1] "835"
```


##Imputing missing values
The total number of missing values in the dataset is:


```r
length(which(is.na(data)))
```

```
## [1] 2304
```

Devise a strategy for filling in all of the missing values in the dataset.I decided to use the mean for that 5-min interval.


```r
#check which interval has missing information for its corresponding steps
intervalmissing <- data$interval[is.na(data$steps)]

#Create a new dataset that is equal to the original dataset but with the missing data filled in.
newdata <- data
newdata$steps[is.na(newdata$steps)] <- timeseries[as.character(intervalmissing)]
```

Make a histogram of the total number of steps taken each day:

```r
#finding total steps per day
newtotalperday <- tapply(newdata$steps, newdata$date,sum)
hist(newtotalperday, xlab="Number of steps", main="histogram of the total number of steps taken each day after imputation")
```

![](./PA1_template_files/figure-html/hist2-1.png) 

The mean and median total number of steps taken per day respectively are:

```r
mean(newtotalperday, na.rm=T)
```

```
## [1] 10766.19
```

```r
median(newtotalperday,na.rm=T)
```

```
## [1] 10766.19
```
These values differ from the estimates from the first part of the assignment and imputing missing data on the estimates will cause the distribution to go towards the imputed values if there are too many missing data.

##Are there differences in activity patterns between weekdays and weekends?
To create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.

```r
#to create the new variable called weekend
weekend <- rep(0,length(newdata$date))
#set those with Sundays or Saturdays to be weekend
weekend[weekdays(newdata$date)=="Sunday"|weekdays(newdata$date)=="Saturday"] <- "weekend"
#set the rest to be weekdays
weekend[weekend==0] <- "weekday"
weekend <- as.factor(weekend)
newdata <- cbind(newdata, weekend)
```

Make a panel plot containing a time series plot of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis).

```r
#now we group according to both weekend/weekday and interval
newtimeseries <- tapply(newdata$steps, list(newdata$interval,newdata$weekend),mean)
par(mfrow=c(2,1))
plot(names(newtimeseries[,1]), newtimeseries[,1], type="l", xlab="Interval", ylab="Number of steps", main="weekday")
plot(names(newtimeseries[,2]), newtimeseries[,2], type="l", xlab="Interval", ylab="Number of steps", main="weekend")
```

![](./PA1_template_files/figure-html/newtimeseries-1.png) 
