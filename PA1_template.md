---
title: "RepResearchPeerAsmt1"
author: "Marie DeAeth"
date: "October 17, 2015"
output: html_document
---  
**Load and Preprocess the Data**
1. Load the Data  
-Make sure that unzipped file is in your working directory  
-read file  
-check dimensions  
-check variable names  
-get column/variable classes with lapply  
-set Date variable to class 'Date'  

```{R}
list.files()
activity <- read.csv("activity.csv", stringsAsFactors = FALSE)
dim(activity)
names(activity)
lapply(activity, class)
```  
2. Preprocess the date (convert date column)  
```{R}
activity$date <- as.Date(activity$date, format="%Y-%m-%d")
```  
**Find mean total number of steps taken per day**  
1. find mean totals by day using aggregate function  
```{R}
MeanTotSteps <- aggregate(steps ~ date, data = activity, mean)
```  
2. create histogram of mean total steps taken per day  
```{R}
hist(MeanTotSteps$steps, col= "lightblue2")
```  
3. Use mean and median functions to calculate for steps per day  
```{R}
mean(MeanTotSteps$steps)
median(MeanTotSteps$steps)
```  
**Find Average Daily Activity Pattern**  
1. Use aggregate function to find average steps per 5 minute interval across all days  
```{R}
DailyPattern <- aggregate(steps ~ interval, data = activity, mean)
plot(DailyPattern, type = "l", col = "blueviolet", main = "Average Daily Activity Pattern")
```  
2. On average the 5-minute interval across all days that contains the most steps is the 615th 5-minute interval  
  
**Imputing missing values**  
1. Calculate the total number of rows with NAs using complete.cases  
```{R}
sum(!complete.cases(activity))
```  
2. (&3.) Now we create a new dataset, activity2 in which we will replace missing values.  
Using the mean total steps per day calculated above, replace NAs in activity$steps variable.  
```{R}
activity2 <- activity
activity2$steps[is.na(activity2$steps)] <- mean(MeanTotSteps$steps)
```  
Check new data set for NA values (just in case there are any in the date or internal variables)  
```{R}
sum(!complete.cases(activity2))
```  
4. Create a new histogram from our new data set and calculate the new mean and median  
```{R}
MeanTotSteps2 <- aggregate(steps ~ date, data = activity2, mean)
hist(MeanTotSteps2$steps, col= "cyan4")
mean(MeanTotSteps2$steps)
median(MeanTotSteps2$steps)
```  
If the mean from our earlier calcualtion was already 37.3826, it makes sense that this would also be our new mean. Filling in the missing step values with the mean of total daily steps  has also increased the ealier median value to now equal the mean value.  
  
**Differences in activity patterns between weekdays and weekends**  
1. Using the 'timeDate' package, we will now create a fourth variable in the acitivty2 dataset, which gives a two level variable, using the isWeekday funciton to determine if the date for the given observation is a Weekday (True) or weekend (False).  
```{R}
library('timeDate')
activity2$weekday <- isWeekday(activity2$date, wday = 1:5)
```  
2. Now we will replot the mean steps across all days, by the 5-minute interval, differentiating between weekdays and weekends, using ggplot2.  
```{R}
library('ggplot2')
DailyPattern2 <- aggregate(steps ~ interval + weekday, data = activity2, mean)
ggplot(data=DailyPattern2, aes(x=interval, y=steps, group=weekday, colour= weekday)) +
    geom_line()
```
  
Fin.





