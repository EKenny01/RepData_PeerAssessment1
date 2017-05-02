Peer-graded Assignment: Course Project 1 - Reproducible Research
================================================================

Erika Kenny

Introduction
------------

Using the activity dataset (consisting of 17,568 observations of steps,
date and five-minute interval taken from one individual over the course
of two months), this report will answer the following questions:  
- What is the mean total number of steps per day - What is the average
daily activity pattern - What are the differences (if any) between
weekdays and weekends

The following code was used to load/read in and process the data:
-----------------------------------------------------------------

          activity <- read.csv("/Users/erika_2o6j3wu/~Repro_Research1/RepData_PeerAssessment1/activity.csv", 
            header = TRUE, sep = ",")
          activity$date <- as.Date(as.character(activity$date))
          activityNoNA <- na.omit(activity)
          aggTry2 <- aggregate(activityNoNA[,1], by=list(activityNoNA[,2]), FUN=sum)

Here is a histogram of the total number of steps taken each day (with NA values removed)
----------------------------------------------------------------------------------------

          hist(aggTry2$x, col = "blue", breaks = 53)

![](PA1_template_files/figure-markdown_strict/histogram-1.png)

          meanSteps <- mean(aggTry2$x)
          medianSteps <- median(aggTry2$x, na.rm = FALSE)

The mean total number of steps taken each day is 1.076618910^{4}.
-----------------------------------------------------------------

The median number of steps taken is each day is 10765.
------------------------------------------------------

To find the average number of steps taken, the following code was used:
-----------------------------------------------------------------------

          aggTry4 <- aggregate(activityNoNA[,1], by=list(activityNoNA[,3]), FUN=mean)
          aggTry4TimeSeries <- ts(aggTry4)

The average daily activity patter is illustrated in the following time series plot of a the average number of steps taken
-------------------------------------------------------------------------------------------------------------------------

          plot(aggTry4$Group.1, aggTry4$x, type = "l", xlab = "Interval", 
                ylab = "Avg Steps Taken All Days", col="blue", lwd=2)

![](PA1_template_files/figure-markdown_strict/averagePlot-1.png)

The five minute interval that, on average, contains the maximum number
of steps is 835.

To impute missing values, the following code was used to replace NA values in the dataset with the mean value using a for loop
------------------------------------------------------------------------------------------------------------------------------

          copyActivity <- activity
          for(i in 1:ncol(copyActivity)){
            copyActivity[is.na(copyActivity[,i]), i] <- mean(copyActivity[,i], na.rm = TRUE)
          }

Here is a histogram of the total number of steps taken each day (with missing values imputed)
---------------------------------------------------------------------------------------------

          aggTry5 <- aggregate(copyActivity[,1], by=list(copyActivity[,2]), FUN=sum)
          hist(aggTry5$x, col = "blue", breaks = 61)

![](PA1_template_files/figure-markdown_strict/stepsWithValues-1.png)

There is clearly variation between weekend and weekday activity as illustrated in the following panel plot comparing the average number of steps taken per five minute interval across weekdays and weekends.
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

          library(lattice)
          newCopyActivity <- copyActivity
          weekdays1 <- c('Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday')
          newCopyActivity$wDay <- factor((weekdays(newCopyActivity$date) %in% 
                                      weekdays1), levels=c(FALSE, TRUE), 
                                   labels=c('weekend', 'weekday'))
          aggTry6 <- aggregate(newCopyActivity[,1], by=newCopyActivity[c("interval", "wDay")], FUN=mean)
          xyplot(x~interval|wDay, data = aggTry6, type = "l", layout=c(1,2))

![](PA1_template_files/figure-markdown_strict/wdayPlots-1.png)
