# Reproducible Research Week 2 Project


## Loading and preprocessing the data

Read activity data in to R, total the number of steps taken each day, and
convert date column from factor to date variable type

```r
activitydata <- read.csv("./activity.csv")
activitydata <- transform(activitydata, date = as.Date(date))
activitytable <- aggregate(activitydata$steps, by=list(activitydata$date), sum)
colnames(activitytable) <- c("date", "TotalStepsTaken")
```


## Mean and median number of steps taken each day

Report mean and median of total steps taken each day

```r
mean(activitytable$TotalStepsTaken, na.rm=TRUE)
```

```
## [1] 10766.19
```

```r
median(activitytable$TotalStepsTaken, na.rm=TRUE)
```

```
## [1] 10765
```

Create a histogram of the total number of steps taken each day

```r
hist(activitytable$TotalStepsTaken)
```

![plot of chunk unnamed-chunk-3](figure/unnamed-chunk-3-1.png)


## Average daily activity pattern

Create a time series plot of the average number of steps taken during each
interval

```r
intervaltable <- aggregate(activitydata$steps, by=list(activitydata$interval), 
                           mean, na.rm=TRUE)
colnames(intervaltable) <- c("interval", "AverageNumberofSteps")
plot(intervaltable$interval, intervaltable$AverageNumberofSteps, type="l")
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4-1.png)

Identify interval with the highest average number of steps

```r
intervaltable[intervaltable[,2]==max(intervaltable$AverageNumberofSteps), 1]
```

```
## [1] 835
```


## Input missing values

Calculate number of rows with missing data

```r
table(is.na(activitydata$steps))
```

```
## 
## FALSE  TRUE 
## 15264  2304
```

Substitute missing data with average number of steps taken, rounded down, 
   for the recorded interval

```r
activitydata[is.na(activitydata$steps),1] <- floor(intervaltable[which(activitydata[is.na(activitydata$steps),3]==intervaltable[1]), 2])
```

Create new table and histogram detailing the total number of steps taken
according to the updated data set

```r
activitynoNA <- aggregate(activitydata$steps, by=list(activitydata$date), sum)
colnames(activitynoNA) <- c("date", "TotalStepsTaken")
hist(activitynoNA$TotalStepsTaken)
```

![plot of chunk unnamed-chunk-8](figure/unnamed-chunk-8-1.png)

Calculate mean and median number of steps taken each day for the new data set

```r
mean(activitynoNA$TotalStepsTaken)
```

```
## [1] 10749.77
```

```r
median(activitynoNA$TotalStepsTaken)
```

```
## [1] 10641
```

As a result of replacing the missing values with the average number of steps 
taken during the recorded interval, the mean and median number of steps taken
each day both slightly decreased.  


## Differences between weekdays and weekendsset
Create a new factor variable called DayType that is assigned the value
"Weekend" if the weekdays() function returns the value "Saturday" or 
"Sunday" or is assigned the value "Weekday"

```r
activitydata$DayType <- factor(NA, c("Weekday","Weekend"))
i=1
for (i in 1:nrow(activitydata)){
      if(weekdays(activitydata[i,2])=="Sunday"||
         weekdays(activitydata[i,2])=="Saturday"){
            activitydata[i,4]<-"Weekend"} 
      
      else {activitydata[i,4] <- "Weekday"}}
```

Calculate average number of steps taken for each interval during a "Weekday"
and a "Weekend"

```r
DayTypeTable <- aggregate(activitydata$steps, 
                          by=list(activitydata$DayType,activitydata$interval), 
                          mean)
colnames(DayTypeTable) <- c("DayType", "Interval", "AverageStepsTaken")
```

Create a time series plot of the average number of steps taken during each
measured interval for "Weekday" days and "Weekend" days

```r
    library(lattice)
    xyplot(AverageStepsTaken ~ Interval | DayType, data=DayTypeTable, type="l")
```

![plot of chunk unnamed-chunk-12](figure/unnamed-chunk-12-1.png)
