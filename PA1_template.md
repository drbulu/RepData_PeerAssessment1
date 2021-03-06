# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data

Load the data into R. Be careful to make sure that the **header=T** option is specified to make sure that the column headings are imported properly. This ensures that the structure of the entire imported dataset is intact.  

The data transformations required for each step of the analysis will be performed when needed.  



```r
data <- read.csv("activity.csv", header = T)
```


## What is mean total number of steps taken per day?

The first step to create the histogram is to summarise the data to represent the **total number** of steps **measured per day**.  

This is different from the original data, which represents the number _(subtotal)_ of steps measured during ***each interval*** of each day.  

The summary is created as follows:


```r
library(plyr)
dailySummaryStats <- ddply(data, .(date), summarize, sum.steps = sum(steps, 
    na.rm = T), mean.steps = mean(steps, na.rm = T), median.steps = median(steps, 
    na.rm = T))
```


Then, the histogram of the total number of steps measured per day can be created as follows:


```r
# We summarise the data using ddply() to process the data by the date column
hist(dailySummaryStats$sum.steps, main = "Histogram: Total number of steps per day", 
    xlab = "Total number of steps in a day", ylab = "Frequency (number of days)")
```

![plot of chunk getMeanAndMedian](figure/getMeanAndMedian.png) 

```r

# Calculating the mean and median number of steps per day for inline use to
# within the final HTML document.
meanDailySteps <- mean(dailySummaryStats$sum.steps)
medianDailySteps <- median(dailySummaryStats$sum.steps)
```


The _mean_ number of steps is: **9354.2**.  
The _median_ number of steps is: **10395**.

## What is the average daily activity pattern?

Creating the time series plot of the **5-minute interval** (x-axis) and the **average** number of steps taken, averaged across all days (y-axis)


```r
# We summarise the data like we did for the histogram above EXCEPT that we
# use ddply() to split by the interval column instead of the date column
intervalSummaryStats <- ddply(data, .(interval), summarize, sum.steps = sum(steps, 
    na.rm = T), mean.interval.steps = mean(steps, na.rm = T))

plot(intervalSummaryStats$interval, intervalSummaryStats$mean.interval.steps, 
    type = "l", main = "Plot of Daily Average Activity pattern", xlab = "interval", 
    ylab = "Number of steps (average)")
```

![plot of chunk plotAvgDailyActivityPattern](figure/plotAvgDailyActivityPattern.png) 


Calculating the 5-minute interval that contains the maximum number of steps, on average across all the days in the dataset. 

Put another, finding out which of the daily intervals had the highest average number of steps (averaged across the **61 days** represented in the original data set).  


```r
# what is the highest value of the mean daily interval?
maxIntervalMean <- max(intervalSummaryStats$mean.interval.steps)

# what interval does this value correspond to?
targetRow <- subset(intervalSummaryStats, mean.interval.steps == maxIntervalMean)
maxInterval <- targetRow$interval
```


The interval with the highest mean is **835**, which has a mean of **206.1698**.  

## Imputing missing values

Calculating the total number of missing values in the original data set.  


```r
# gives an overall summary of the composition of the original data set
summary(data)
```

```
##      steps               date          interval   
##  Min.   :  0.0   2012-10-01:  288   Min.   :   0  
##  1st Qu.:  0.0   2012-10-02:  288   1st Qu.: 589  
##  Median :  0.0   2012-10-03:  288   Median :1178  
##  Mean   : 37.4   2012-10-04:  288   Mean   :1178  
##  3rd Qu.: 12.0   2012-10-05:  288   3rd Qu.:1766  
##  Max.   :806.0   2012-10-06:  288   Max.   :2355  
##  NA's   :2304    (Other)   :15840
```

```r

# Convenience step to explicitely calculate the number of missing values and
# return then to a variable that can be displayed later inline (as HTML
# text)
missingSteps <- length(data$steps[is.na(data$steps)])

meanDataSteps <- mean(data$steps, na.rm = T)
```


The total number of missing values is: **2304**.  

Selecting the value to use for imputing missing values:  
1. From the summary above, the average number of steps taken at all intervals in the entire data set is **37.3826**.  
2. This can be taken to mean that at _any_ given _interval_ on _any_ given _day_ in the data set. The subject will on average have taken roughly **37** steps.  

Admittedly, this would appear to assume assume constant motion on the part of the subject at all times. This is not strictly realistic, as the subject would need to sleep or may take some form of transport other than walking, in which case the number of steps will be **0**.

However, for simplicity sake, the missing data will be imputed with the number **<span style="text-decoration:underline">37</span>** as follows:


```r
# Create a variable called imputedSteps from the steps column of the 'data'
# data frame
imputedSteps <- data$steps

# replace the NA values from imputedSteps with the desired value to use for
# imputation
imputedSteps[is.na(imputedSteps)] <- 37

# make a new data frame called imputedData: from imputedSteps variable and
# the 'date' and 'interval' columns of 'data' using data.frame() to ensure
# that R makes a data frame and NOT a matrix
imputedData <- data.frame(imputedSteps, data$date, data$interval)

# rename the column names of 'imputedData' data frame to match those of
# 'data'
names(imputedData) <- names(data)
```


Once we have created the imputed data set (in a way that does not overwrite the original data) we can _summarise_ and _plot_ the total number of steps per day of the **imputed data** as a histogram like we did for the original data.


```r
# make a summary of the imputed data via the method used earlier.
imputedDailySummaryStats <- ddply(imputedData, .(date), summarize, sum.steps = sum(steps, 
    na.rm = T), mean.interval.steps = mean(steps, na.rm = T))

# plot the data
hist(imputedDailySummaryStats$sum.steps, col = "red", main = "Histogram: Total number of steps per day", 
    xlab = "Total number of steps in a day", ylab = "Frequency (number of days)")
```

![plot of chunk plotImputedData](figure/plotImputedData.png) 

```r

imputedMeanDailySteps <- mean(imputedDailySummaryStats$sum.steps)
imputedMedianDailySteps <- median(imputedDailySummaryStats$sum.steps)
```


In the summary of the _imputed data_ original data:  
1. The _mean_ number of steps is: **10752**.  
2. The _median_ number of steps is: **10656**.  

In the summary of the _original data_ (before imputation):  
1. The _mean_ number of steps is: **9354.2**.  
2. The _median_ number of steps is: **10395**.  

Comparatively:  
1. The imputed mean values are **1.1494** higher than the previously calculated mean.  
2. The imputed values are **1.0251** higher than the previously calculated median.  

## Are there differences in activity patterns between weekdays and weekends?

Create the updated data set with the day.type factor variable to indicate whether a given day in the dataset is a **weekday** or a **weekend** day:


```r

# create a new numeric column according to whether the day in data is a
# weekday or not.
day.type <- ifelse(weekdays(as.Date(imputedData$date)) == "Saturday" | weekdays(as.Date(imputedData$date)) == 
    "Saturday", c(2), c(1))

# bind this factor to the data frame
factorData <- data.frame(data, day.type)

# Make the 'day.type' column a factor
factorData$day.type <- factor(factorData$day.type)

# set the levels (categories) of the weekday
levels(factorData$day.type) <- c("weekday", "weekend")
```


Create an appropriate summary of the data that is appropriate to make a time series similar to the one created earlier in the document:  


```r
factorSummaryStats <- ddply(factorData, .(interval, day.type), summarize, sum.steps = sum(steps, 
    na.rm = T), mean.interval.steps = mean(steps, na.rm = T))
```


Create the **_2-panel_ time series plot** to display the mean number of steps at each interval on **weekdays** compared to **weekend** days: 


```r
# Create the time series plot using the lattice system. For ease of use.
library(lattice)

# Create the plot so that it matches the format of the example in README.md
xyplot(mean.interval.steps ~ interval | day.type, data = factorSummaryStats, 
    type = "l", group = day.type, layout = c(1, 2), main = "Plot of Daily Average Activity pattern: Weekend vs. Weekday", 
    xlab = "interval", ylab = "Number of steps (average)")
```

![plot of chunk dayTypeTimeSeries](figure/dayTypeTimeSeries.png) 

```r

# Note: this is a very useful resource for constructing lattice plots
# https://www.stat.auckland.ac.nz/~paul/RGraphics/chapter4.pdf Chapter 4.4
# is where I got the hint about using the layout parameter to control plot
# layout.  another useful resource:
# http://lmdvr.r-forge.r-project.org/figures/figures.html
```

