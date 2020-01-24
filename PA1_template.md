Loading and preprocessing the data
----------------------------------

    data <- read.csv("C:\\Users\\rache\\Downloads\\repdata_data_activity\\activity.csv")

What is mean total number of steps taken per day?
-------------------------------------------------

    library(dplyr)

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

    stepsSum <- data %>%
      filter(!is.na(steps)) %>%
      group_by(date) %>%
      summarise(steps_sum = sum(steps))

\*histogramme

    hist(stepsSum$steps_sum)

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-3-1.png)
\*mean of steps per day

    mean(stepsSum$steps_sum)

    ## [1] 10766.19

\*median of steps per day

    median(stepsSum$steps_sum)

    ## [1] 10765

What is the average daily activity pattern?
-------------------------------------------

    step5 <- data %>%
      filter(!is.na(steps)) %>%
      group_by(interval) %>%
      summarise(steps_mean = mean(steps))


    plot(step5$interval,step5$steps_mean,type = "l", xlab = "interval", ylab = "steps mean")

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-6-1.png)

    max_interval <- step5 %>%
      filter(steps_mean==steps_mean)

-   maximum interval

<!-- -->

    max_interval$interval

    ##   [1]    0    5   10   15   20   25   30   35   40   45   50   55  100  105  110
    ##  [16]  115  120  125  130  135  140  145  150  155  200  205  210  215  220  225
    ##  [31]  230  235  240  245  250  255  300  305  310  315  320  325  330  335  340
    ##  [46]  345  350  355  400  405  410  415  420  425  430  435  440  445  450  455
    ##  [61]  500  505  510  515  520  525  530  535  540  545  550  555  600  605  610
    ##  [76]  615  620  625  630  635  640  645  650  655  700  705  710  715  720  725
    ##  [91]  730  735  740  745  750  755  800  805  810  815  820  825  830  835  840
    ## [106]  845  850  855  900  905  910  915  920  925  930  935  940  945  950  955
    ## [121] 1000 1005 1010 1015 1020 1025 1030 1035 1040 1045 1050 1055 1100 1105 1110
    ## [136] 1115 1120 1125 1130 1135 1140 1145 1150 1155 1200 1205 1210 1215 1220 1225
    ## [151] 1230 1235 1240 1245 1250 1255 1300 1305 1310 1315 1320 1325 1330 1335 1340
    ## [166] 1345 1350 1355 1400 1405 1410 1415 1420 1425 1430 1435 1440 1445 1450 1455
    ## [181] 1500 1505 1510 1515 1520 1525 1530 1535 1540 1545 1550 1555 1600 1605 1610
    ## [196] 1615 1620 1625 1630 1635 1640 1645 1650 1655 1700 1705 1710 1715 1720 1725
    ## [211] 1730 1735 1740 1745 1750 1755 1800 1805 1810 1815 1820 1825 1830 1835 1840
    ## [226] 1845 1850 1855 1900 1905 1910 1915 1920 1925 1930 1935 1940 1945 1950 1955
    ## [241] 2000 2005 2010 2015 2020 2025 2030 2035 2040 2045 2050 2055 2100 2105 2110
    ## [256] 2115 2120 2125 2130 2135 2140 2145 2150 2155 2200 2205 2210 2215 2220 2225
    ## [271] 2230 2235 2240 2245 2250 2255 2300 2305 2310 2315 2320 2325 2330 2335 2340
    ## [286] 2345 2350 2355

Imputing missing values
-----------------------

    missing_data <- data %>%
      filter(is.na(steps))

-   number of missing data

<!-- -->

    length(missing_data[1])

    ## [1] 1

    data_complet <- data
    iMeans<-by(data$steps,data$interval,mean,na.rm=TRUE)

    data_complet[!complete.cases(data_complet),1]<-
      
      iMeans[as.character(data_complet[!complete.cases(data_complet),3])]

-   histogramme

<!-- -->

    hist(data_complet$steps)

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-11-1.png) \*
mean of steps per day

    mean(data_complet$steps)

    ## [1] 37.3826

-   median of stepsper day

<!-- -->

    median(data_complet$steps)

    ## [1] 0

Are there differences in activity patterns between weekdays and weekends?
-------------------------------------------------------------------------

    day_of_week <- data$date %>%
      as.Date(format="%Y-%m-%d" ) %>%
      weekdays()

    data_week <- cbind(data_complet, day_of_week)

    weekend <- data_week %>%
      filter(day_of_week == "samedi" | day_of_week == "dimanche") %>%
      group_by(interval) %>%
      summarise(steps_mean = mean(steps))


    week <- data_week %>%
      filter(day_of_week != "samedi" & day_of_week != "dimanche") %>%
      group_by(interval) %>%
      summarise(steps_mean = mean(steps))


    par(mfrow=c(2,1))
    plot(weekend$interval, weekend$steps_mean , type = "l")
    plot(week$interval, week$steps_mean , type = "l")

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-14-1.png)
