#### What is mean total number of steps taken per day?

-   Calculate the total number of steps taken per day

<!-- -->

    totsteps<-tapply(activity$steps,activity$date,sum )

-   Make a histogram of the total number of steps taken each day

<!-- -->

    hist(totsteps, breaks=10)

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-3-1.png)

-   Calculate and report the mean and median of the total number of
    steps taken per day

<!-- -->

    mean(totsteps, na.rm=TRUE)

    ## [1] 10766.19

    median(totsteps, na.rm=TRUE)

    ## [1] 10765

#### What is the average daily activity pattern?

-   Make a time series plot (i.e.**type="l"**) of the 5-minute interval
    (x-axis) and the average number of steps taken, averaged across all
    days (y-axis)

<!-- -->

    avg_steps <- aggregate(activity$steps, by = list(activity$interval), mean, na.rm = TRUE)


    plot( avg_steps[,2], type= "l", ylab="average steps", xlab="5-minute interval")

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-5-1.png)

\*Which 5-minute interval, on average across all the days in the
dataset, contains the maximum number of steps?

    avg_steps[which.max(avg_steps[,2]),1]

    ## [1] 835

#### Imputing missing values

-   Calculate and report the total number of missing values in the
    dataset (i.e. the total number of rows with **NA**s)

<!-- -->

    sum(is.na(activity))

    ## [1] 2304

-   Devise a strategy for filling in all of the missing values in the
    dataset. The strategy does not need to be sophisticated. For
    example, you could use the mean/median for that day, or the mean for
    that 5-minute interval, etc. Create a new dataset that is equal to
    the original dataset but with the missing data filled in.

First we will create an index of all NAs.

    index<-as.numeric(is.na(activity$steps))

Now, it's time to impute the missing values, using matrix **avg\_steps**
from above.

    imputed_activity<-activity

    for (i in 1:17568){
            if(index[i]==1){
                   imputed_activity$steps[i]<-avg_steps[avg_steps$Group.1==imputed_activity$interval[i],2]
            }
    }

-   Make a histogram of the total number of steps taken each day and
    Calculate and report the **mean** and **median** total number of
    steps taken per day. Do these values differ from the estimates from
    the first part of the assignment? What is the impact of imputing
    missing data on the estimates of the total daily number of steps?

<!-- -->

    hist(tapply(imputed_activity$steps,imputed_activity$date,sum ), breaks=10, xlab="steps per day")

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-10-1.png)

    mean(tapply(imputed_activity$steps,imputed_activity$date,sum ))

    ## [1] 10766.19

    median(tapply(imputed_activity$steps,imputed_activity$date,sum ))

    ## [1] 10766.19

We observe the impact of imputing missing data at the scatterplot,
below.

    plot(tapply(imputed_activity$steps,imputed_activity$date,sum ), col="blue", ylab="total steps per date", main="Before(red) and After(blue) imputation")
    points(totsteps,col="red")
    axis(side = 1, seq(0,60,by=5))

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-11-1.png)

#### Are there differences in activity patterns between weekdays and weekends?

-   Create a new factor variable in the dataset with two levels -
    "weekday" and "weekend" indicating whether a given date is a weekday
    or weekend day.

<!-- -->

    fac<-vector(mode="numeric",length=17568)
    for (i in 1:17568){
            fac[i]<-(weekdays(activity$date[i])=="Saturday"|weekdays(activity$date[i])=="Sunday")
    }

    fac<-as.numeric(fac)
    colnames(imputed_activity)

    ## [1] "steps"    "date"     "interval"

    imputed_activity<-cbind(imputed_activity, fac)
    colnames(imputed_activity)<-c("steps","date","interval","weekdays")
    imputed_activity$weekdays<-as.factor(imputed_activity$weekdays)
    levels(imputed_activity$weekdays)<-c("Weekday","Weekend")

-   Make a panel plot containing a time series plot (i.e. **type="l"**)
    of the 5-minute interval (x-axis) and the average number of steps
    taken, averaged across all weekday days or weekend days (y-axis).
    See the README file in the GitHub repository to see an example of
    what this plot should look like using simulated data.

<!-- -->

    Weekdays_steps<-tapply(imputed_activity$steps[imputed_activity$weekdays=="Weekday"],imputed_activity$interval[imputed_activity$weekdays=="Weekday"],mean )

    Weekend_steps<-tapply(imputed_activity$steps[imputed_activity$weekdays=="Weekend"],imputed_activity$interval[imputed_activity$weekdays=="Weekend"],mean )


    par(mfrow = c(2,1))
    plot(Weekdays_steps, type="l", main="Weekdays", ylab="steps", xlab="interval")
    plot(Weekend_steps, type="l", main="Weekends", ylab="steps", xlab="interval")

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-13-1.png)
