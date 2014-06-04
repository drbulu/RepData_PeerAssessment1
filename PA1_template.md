# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data


```r
data <- read.csv("activity.csv", header = T)
```


data subset without NA values (may not need it due to the above)


```r
subsetData <- subset(data, !is.na(data$steps))
head(subsetData)
```

```
##     steps       date interval
## 289     0 2012-10-02        0
## 290     0 2012-10-02        5
## 291     0 2012-10-02       10
## 292     0 2012-10-02       15
## 293     0 2012-10-02       20
## 294     0 2012-10-02       25
```


Summarise the data by the sum, mean and median of the number of steps measured on each day:  


```r
summary.stats <- ddply(data, .(date), summarize, sum.steps = sum(steps, na.rm = T), 
    mean.steps = mean(steps, na.rm = T), median.steps = median(steps, na.rm = T))
```

```
## Error: could not find function "ddply"
```


## What is mean total number of steps taken per day?


Histogram of the total number of steps measured per day:

```r

hist(summary.stats$sum.steps, main = "Histogram: Total number of steps per day", 
    xlab = "Total number of steps in a day", ylab = "Frequency (number of days)")
```

```
## Error: object 'summary.stats' not found
```

```r

meanSteps <- mean(subsetData$steps)
medianSteps <- median(subsetData$steps)
```


The _mean_ number of steps is: **37.3826**.
The _median_ number of steps is: **0**.


## What is the average daily activity pattern?



## Imputing missing values


```r
missingSteps <- length(data$steps[is.na(data$steps)])
```


The total number of missing values is: **2304**.

It is assumed that missing values represent periods of inactivity where 


```r
dataImputed <- data
dataImputed$st <- gsub()
```

```
## Error: argument "x" is missing, with no default
```


## Are there differences in activity patterns between weekdays and weekends?



