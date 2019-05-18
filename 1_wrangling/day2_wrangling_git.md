---
title: "Day 2: Datetime Wrangling"
author: "Josephine Lukito"
date: "May 17, 2019"
output: rmarkdown::github_document
---

Today, we'll be working a little on datetime wrangling. There are a lot of packages that allow you to handle date or datetime data. We will discuss some of Jordan and my favorites: `posixct`, `zoo`, `xts`, `tsibble`, and `lubridate`.


```r
library(tidyverse)
library(zoo)
library(xts)
library(tsibble)
library(lubridate)
```

# Wangling the data into a tidy format
For this, we'll be using the `nycflights13` dataset. In this dataset, the year, month, date, hour, and time have been separated into different columns, so we'll want to make a new date-time variable out of these (there is a `time_hour` variable, but let's imagine that we want want to create a new datetime variable with the minute information). We'll first construct a string with all the necessary temporal information (year, month, day, hour, minute).


```r
library(nycflights13)
flights
```

```
## # A tibble: 336,776 x 20
##     year month   day dep_time sched_dep_time dep_delay arr_time
##    <int> <int> <int>    <int>          <int>     <dbl>    <int>
##  1  2013     1     1      517            515         2      830
##  2  2013     1     1      533            529         4      850
##  3  2013     1     1      542            540         2      923
##  4  2013     1     1      544            545        -1     1004
##  5  2013     1     1      554            600        -6      812
##  6  2013     1     1      554            558        -4      740
##  7  2013     1     1      555            600        -5      913
##  8  2013     1     1      557            600        -3      709
##  9  2013     1     1      557            600        -3      838
## 10  2013     1     1      558            600        -2      753
## # ... with 336,766 more rows, and 13 more variables: sched_arr_time <int>,
## #   arr_delay <dbl>, carrier <chr>, flight <int>, tailnum <chr>,
## #   origin <chr>, dest <chr>, air_time <dbl>, distance <dbl>, hour <dbl>,
## #   minute <dbl>, time_hour <dttm>, full_time <dttm>
```

```r
flights$full_time <- paste(flights$year, flights$month, sep = "-") %>% paste(flights$day, sep="-") %>% paste(flights$hour, sep=" ") %>% paste(flights$minute, sep=":")
str(flights)
```

```
## Classes 'tbl_df', 'tbl' and 'data.frame':	336776 obs. of  20 variables:
##  $ year          : int  2013 2013 2013 2013 2013 2013 2013 2013 2013 2013 ...
##  $ month         : int  1 1 1 1 1 1 1 1 1 1 ...
##  $ day           : int  1 1 1 1 1 1 1 1 1 1 ...
##  $ dep_time      : int  517 533 542 544 554 554 555 557 557 558 ...
##  $ sched_dep_time: int  515 529 540 545 600 558 600 600 600 600 ...
##  $ dep_delay     : num  2 4 2 -1 -6 -4 -5 -3 -3 -2 ...
##  $ arr_time      : int  830 850 923 1004 812 740 913 709 838 753 ...
##  $ sched_arr_time: int  819 830 850 1022 837 728 854 723 846 745 ...
##  $ arr_delay     : num  11 20 33 -18 -25 12 19 -14 -8 8 ...
##  $ carrier       : chr  "UA" "UA" "AA" "B6" ...
##  $ flight        : int  1545 1714 1141 725 461 1696 507 5708 79 301 ...
##  $ tailnum       : chr  "N14228" "N24211" "N619AA" "N804JB" ...
##  $ origin        : chr  "EWR" "LGA" "JFK" "JFK" ...
##  $ dest          : chr  "IAH" "IAH" "MIA" "BQN" ...
##  $ air_time      : num  227 227 160 183 116 150 158 53 140 138 ...
##  $ distance      : num  1400 1416 1089 1576 762 ...
##  $ hour          : num  5 5 5 5 6 5 6 6 6 6 ...
##  $ minute        : num  15 29 40 45 0 58 0 0 0 0 ...
##  $ time_hour     : POSIXct, format: "2013-01-01 05:00:00" "2013-01-01 05:00:00" ...
##  $ full_time     : chr  "2013-1-1 5:15" "2013-1-1 5:29" "2013-1-1 5:40" "2013-1-1 5:45" ...
```

The first thing we'll want to do is convert this string to a date-time format. One popular date-time format (or "class") in base R is called `POSIXct`, which you can learn more about [here](http://biostat.mc.vanderbilt.edu/wiki/pub/Main/ColeBeck/datestimes.pdf){target="_blank"} . To convert a vector of strings into a vector of `POSIXct` objects, you will need to use the `as.POSIXct()` function (learn more about it [here](https://www.rdocumentation.org/packages/base/versions/3.5.3/topics/as.POSIX*){target="_blank"}).


```r
flights$full_time <- as.POSIXct(flights$full_time)
str(flights)
```

```
## Classes 'tbl_df', 'tbl' and 'data.frame':	336776 obs. of  20 variables:
##  $ year          : int  2013 2013 2013 2013 2013 2013 2013 2013 2013 2013 ...
##  $ month         : int  1 1 1 1 1 1 1 1 1 1 ...
##  $ day           : int  1 1 1 1 1 1 1 1 1 1 ...
##  $ dep_time      : int  517 533 542 544 554 554 555 557 557 558 ...
##  $ sched_dep_time: int  515 529 540 545 600 558 600 600 600 600 ...
##  $ dep_delay     : num  2 4 2 -1 -6 -4 -5 -3 -3 -2 ...
##  $ arr_time      : int  830 850 923 1004 812 740 913 709 838 753 ...
##  $ sched_arr_time: int  819 830 850 1022 837 728 854 723 846 745 ...
##  $ arr_delay     : num  11 20 33 -18 -25 12 19 -14 -8 8 ...
##  $ carrier       : chr  "UA" "UA" "AA" "B6" ...
##  $ flight        : int  1545 1714 1141 725 461 1696 507 5708 79 301 ...
##  $ tailnum       : chr  "N14228" "N24211" "N619AA" "N804JB" ...
##  $ origin        : chr  "EWR" "LGA" "JFK" "JFK" ...
##  $ dest          : chr  "IAH" "IAH" "MIA" "BQN" ...
##  $ air_time      : num  227 227 160 183 116 150 158 53 140 138 ...
##  $ distance      : num  1400 1416 1089 1576 762 ...
##  $ hour          : num  5 5 5 5 6 5 6 6 6 6 ...
##  $ minute        : num  15 29 40 45 0 58 0 0 0 0 ...
##  $ time_hour     : POSIXct, format: "2013-01-01 05:00:00" "2013-01-01 05:00:00" ...
##  $ full_time     : POSIXct, format: "2013-01-01 05:15:00" "2013-01-01 05:29:00" ...
```

```r
flights_rearranged <- arrange(flights, full_time)
```

## Date
In base R, there is also `Date`, which will give you a date (not a date-time) variable. You can convert a date-time object into a date object using `as.Date()`

```r
flights_rearranged$dated <- as.Date(flights_rearranged$time_hour)
head(subset(flights_rearranged, select = c(full_time, dated)))
```

```
## # A tibble: 6 x 2
##   full_time           dated     
##   <dttm>              <date>    
## 1 2013-01-01 05:15:00 2013-01-01
## 2 2013-01-01 05:29:00 2013-01-01
## 3 2013-01-01 05:40:00 2013-01-01
## 4 2013-01-01 05:45:00 2013-01-01
## 5 2013-01-01 05:58:00 2013-01-01
## 6 2013-01-01 05:59:00 2013-01-01
```
Notice here that the `dated` column no longer has the time information.

## Filling in gaps
Let's return back to our minute-level data. If we wanted to construct a time series of the number of flights per minute, we could use `table()`. This will likely make your date-time variable into a factor, so make sure you reformat it using `as.POSIXct`.


```r
minutely_flights <- table(flights_rearranged$full_time) %>% as.data.frame()
colnames(minutely_flights)[1] <- "time"
colnames(minutely_flights)[2] <- "flights"
minutely_flights$time <- as.POSIXct(minutely_flights$time)
head(minutely_flights)
```

```
##                  time flights
## 1 2013-01-01 05:15:00       1
## 2 2013-01-01 05:29:00       1
## 3 2013-01-01 05:40:00       1
## 4 2013-01-01 05:45:00       1
## 5 2013-01-01 05:58:00       1
## 6 2013-01-01 05:59:00       1
```

You'll notice that this data has many gaps. This can be common depending on the real-world data you are using (the smaller the time unit, the more gaps you are likely to have, by virtue of aggregation). For example, there are no flights going out from 5:16 to 5:28 (however, there is obviously at least 1 flight going out every day). 

One way in which you can fill this gap is by merging your data with a sequential index of all the minutes in your time series. All it takes is 3 lines of code!


```r
full_time <- data.frame(time = seq.POSIXt(minutely_flights$time[1], minutely_flights$time[length(minutely_flights$time)], by="min")) #create an index of all minutes

allmin_flights <- merge(full_time, minutely_flights, by = "time", all.x = T) #merge the two data frames

allmin_flights$flights[is.na(allmin_flights$flights)] <- 0 #fill all <NA>'s with 0

head(allmin_flights) #check out your tidy data!
```

```
##                  time flights
## 1 2013-01-01 05:15:00       1
## 2 2013-01-01 05:16:00       0
## 3 2013-01-01 05:17:00       0
## 4 2013-01-01 05:18:00       0
## 5 2013-01-01 05:19:00       0
## 6 2013-01-01 05:20:00       0
```

Base R provides some support for time series objects in the form of `ts`. 


```r
ts_flights <- ts(allmin_flights)
class(ts_flights)
```

```
## [1] "mts"    "ts"     "matrix"
```

However, you'll also get a lot of support through other packages, some of which we mention here.

# Other Packages
As we mentioned, there are lots of other packages which can help you wrangle your data. Let's dive into some of them here.

## xts
We'll begin our package overview with the popular time series package is `xts`. One [tutorial](http://rstudio-pubs-static.s3.amazonaws.com/288218_117e183e74964557a5da4fc5902fc671.html){target="_blank"} describes xts objects as "normal R matrices, but with special powers".

One cool feature that you'll notice instantly is that `xts` objects use datetime stamps as its index. Use `xts()` to convert something into an xts object. In the `order.by` attribute, indicate the column with the time information.

`order.by` attribute when creating an xts object

```r
xts_flights <- xts(allmin_flights$flights, order.by = allmin_flights$time)
head(xts_flights)
```

```
##                     [,1]
## 2013-01-01 05:15:00    1
## 2013-01-01 05:16:00    0
## 2013-01-01 05:17:00    0
## 2013-01-01 05:18:00    0
## 2013-01-01 05:19:00    0
## 2013-01-01 05:20:00    0
```

```r
class(xts_flights)
```

```
## [1] "xts" "zoo"
```

`xts` has a variety of functions to help you understand your data. You can use the `indexClass()` function to see the format of your datetime information (which has become your index):

```r
indexClass(xts_flights)
```

```
## [1] "POSIXct" "POSIXt"
```

You can also use `tzone()` to check the time zone of your datetime information.

```r
tzone(xts_flights)
```

```
## [1] ""
```

```r
#tzone(xts_flights) <- "America/Chicago" #use this to set your timezone
```

`xts` can also help you figure out how many minutes, hours, months, or days there are in your dataset. Check out `?ndays` for more.

```r
nminutes(xts_flights)
```

```
## [1] 525285
```

```r
nhours(xts_flights)
```

```
## [1] 8755
```

```r
ndays(xts_flights)
```

```
## [1] 365
```

```r
nweeks(xts_flights)
```

```
## [1] 53
```

`xts` also supports a set of `apply` functions, such as `apply.daily()` and `apply.weekly()`.

```r
xts_daily <- apply.daily(xts_flights, sum)
xts_monthly <- apply.monthly(xts_flights, sum)

head(xts_daily)
```

```
##                     [,1]
## 2013-01-01 23:59:00  842
## 2013-01-02 23:59:00  943
## 2013-01-03 23:59:00  914
## 2013-01-04 23:59:00  915
## 2013-01-05 23:59:00  720
## 2013-01-06 23:59:00  832
```

```r
head(xts_monthly)
```

```
##                      [,1]
## 2013-01-31 23:59:00 27004
## 2013-02-28 23:59:00 24951
## 2013-03-31 23:59:00 28834
## 2013-04-30 23:59:00 28330
## 2013-05-31 23:59:00 28796
## 2013-06-30 23:59:00 28243
```

As with other apply functions, you can change the function you would want to use (for example, taking an average of a day, or the max peak, rather than a sum).

##lubridate
The second package we'll go over is `lubridate`, which is part of the `tidyverse` family of packages. You can visit the official site [here](https://lubridate.tidyverse.org/){target="_blank"}. There is also an excellent [cheatsheet](https://evoldyn.gitlab.io/evomics-2018/ref-sheets/R_lubridate.pdf){target="_blank"}. Lubridate reads strings as `POSIXct` objects (learn more about it by searchg `?lubridate`). It's great for calculations and for formatting your data. 

Lubridate includes functions like `month()` and `hour()` to pull out information from datetime data.

```r
lubridate_min_flight <- allmin_flights %>%
  mutate(month = month(allmin_flights$time)) %>%
  mutate(day = day(allmin_flights$time)) %>%
  mutate(morning = am(allmin_flights$time)) %>%
  mutate(quarter = quarter(allmin_flights$time))
head(lubridate_min_flight)
```

```
##                  time flights month day morning quarter
## 1 2013-01-01 05:15:00       1     1   1    TRUE       1
## 2 2013-01-01 05:16:00       0     1   1    TRUE       1
## 3 2013-01-01 05:17:00       0     1   1    TRUE       1
## 4 2013-01-01 05:18:00       0     1   1    TRUE       1
## 5 2013-01-01 05:19:00       0     1   1    TRUE       1
## 6 2013-01-01 05:20:00       0     1   1    TRUE       1
```

Lubridate also has nice functions for aggregating data by day or month. For example, you can use `floor_date()` to round down, which aggregates up your temporal data.

```r
allmin_flights %>% group_by(day=floor_date(time, "day")) %>%
   summarize(flights=sum(flights))
```

```
## # A tibble: 365 x 2
##    day                 flights
##    <dttm>                <dbl>
##  1 2013-01-01 00:00:00     842
##  2 2013-01-02 00:00:00     943
##  3 2013-01-03 00:00:00     914
##  4 2013-01-04 00:00:00     915
##  5 2013-01-05 00:00:00     720
##  6 2013-01-06 00:00:00     832
##  7 2013-01-07 00:00:00     933
##  8 2013-01-08 00:00:00     899
##  9 2013-01-09 00:00:00     902
## 10 2013-01-10 00:00:00     932
## # ... with 355 more rows
```

You can also `group_by()` the day variable we made above, but it won't retain the `POSIXct` object as nicely.

```r
lubridate_min_flight %>% group_by(day) %>%
   summarize(flights=sum(flights))
```

```
## # A tibble: 31 x 2
##      day flights
##    <int>   <dbl>
##  1     1   11036
##  2     2   10808
##  3     3   11211
##  4     4   11059
##  5     5   10858
##  6     6   11059
##  7     7   10985
##  8     8   11271
##  9     9   10857
## 10    10   11227
## # ... with 21 more rows
```
Source: https://ro-che.info/articles/2017-02-22-group_by_month_r

Lubridate also has `round_date()`, which allows you to round to the nearest unit (February 25th would be rounded to March if using `round_date()` to aggregate monthly data) and `ceiling_date()`, which rounds up. For more on this, I encourage you to check out the [cheatsheet](https://evoldyn.gitlab.io/evomics-2018/ref-sheets/R_lubridate.pdf){target="_blank"}.

## zoo
The third package we'll talk about is `zoo`, which is especially useful for munging time series data, such as when constructing a [moving average](https://en.wikipedia.org/wiki/Moving_average){target="_blank"}. You can find the [CRAN Documentation here](https://cran.r-project.org/web/packages/zoo/zoo.pdf){target="_blank"}. You may have noticed when you checked the class of your `xts` that it is also a `zoo` object. This means you can use `zoo` functions on `xts` objects!

Use `index()` and `coredata()` to look at your data columns

```r
head(zoo::index(xts_flights)) #use index() to check out the index of your xts object, i.e., your time points
```

```
## [1] "2013-01-01 05:15:00 CST" "2013-01-01 05:16:00 CST"
## [3] "2013-01-01 05:17:00 CST" "2013-01-01 05:18:00 CST"
## [5] "2013-01-01 05:19:00 CST" "2013-01-01 05:20:00 CST"
```

```r
head(zoo::coredata(xts_flights)) #use coredata() to check out the data of your time series
```

```
##      [,1]
## [1,]    1
## [2,]    0
## [3,]    0
## [4,]    0
## [5,]    0
## [6,]    0
```

One popular use of `zoo` is to create a moving average using the `rollapply()` function. For example, here, we'll use the `rollapply()` function to construct a rolling mean, otherwise known as a moving average. We'll use a window, or `width`, of 5.

```r
zoo_daily_movingaverage <- rollapply(xts_daily, width = 5, FUN = mean, na.rm = TRUE) 
zoo_daily_movingaverage[1:15]
```

```
##                      [,1]
## 2013-01-01 23:59:00    NA
## 2013-01-02 23:59:00    NA
## 2013-01-03 23:59:00    NA
## 2013-01-04 23:59:00    NA
## 2013-01-05 23:59:00 866.8
## 2013-01-06 23:59:00 864.8
## 2013-01-07 23:59:00 862.8
## 2013-01-08 23:59:00 859.8
## 2013-01-09 23:59:00 857.2
## 2013-01-10 23:59:00 899.6
## 2013-01-11 23:59:00 919.2
## 2013-01-12 23:59:00 870.6
## 2013-01-13 23:59:00 856.4
## 2013-01-14 23:59:00 861.6
## 2013-01-15 23:59:00 854.0
```

`rollapply()` is the generic form, which means you can use this to apply any function across a rolling window. `zoo` also has `rollmean()`, which will give you a moving average, or `rollmedian()`, which will give you a moving median. Check out this spcific functions by searching `?rollmean`.


```r
zoo_daily_movingaverage <- rollmean(xts_daily, k = 5) 
zoo_daily_movingaverage[1:15]
```

```
##                      [,1]
## 2013-01-03 23:59:00 866.8
## 2013-01-04 23:59:00 864.8
## 2013-01-05 23:59:00 862.8
## 2013-01-06 23:59:00 859.8
## 2013-01-07 23:59:00 857.2
## 2013-01-08 23:59:00 899.6
## 2013-01-09 23:59:00 919.2
## 2013-01-10 23:59:00 870.6
## 2013-01-11 23:59:00 856.4
## 2013-01-12 23:59:00 861.6
## 2013-01-13 23:59:00 854.0
## 2013-01-14 23:59:00 848.2
## 2013-01-15 23:59:00 895.6
## 2013-01-16 23:59:00 914.8
## 2013-01-17 23:59:00 864.0
```

```r
zoo_daily_movingsum <- rollsum(xts_daily, k = 5) 
zoo_daily_movingsum[1:15]
```

```
##                     [,1]
## 2013-01-03 23:59:00 4334
## 2013-01-04 23:59:00 4324
## 2013-01-05 23:59:00 4314
## 2013-01-06 23:59:00 4299
## 2013-01-07 23:59:00 4286
## 2013-01-08 23:59:00 4498
## 2013-01-09 23:59:00 4596
## 2013-01-10 23:59:00 4353
## 2013-01-11 23:59:00 4282
## 2013-01-12 23:59:00 4308
## 2013-01-13 23:59:00 4270
## 2013-01-14 23:59:00 4241
## 2013-01-15 23:59:00 4478
## 2013-01-16 23:59:00 4574
## 2013-01-17 23:59:00 4320
```

## tsibble
The last time series package we'll discuss is `tsibble`, which is built on top of the tidyverse `tibble`. `tsibble` is now part of the `tidyverts` package, which you can find out about [here](https://tidyverts.org/). This is probably Jo's current favorite package.

To convert sometime to a tsibble, you'll use `as_tsibble()`. For this, let's go back to our pre-filled minute-level data.

```r
tsibble_flights <- as_tsibble(minutely_flights) #note that time is automatically treated as the index variable
```

```
## Using `time` as index variable.
```

```r
head(tsibble_flights)
```

```
## # A tsibble: 6 x 2 [1m] <?>
##   time                flights
##   <dttm>                <int>
## 1 2013-01-01 05:15:00       1
## 2 2013-01-01 05:29:00       1
## 3 2013-01-01 05:40:00       1
## 4 2013-01-01 05:45:00       1
## 5 2013-01-01 05:58:00       1
## 6 2013-01-01 05:59:00       1
```

You can use the tsibble function `fill_gaps()` to fill missing gaps without merging it with an index. Learn more about this function [here](https://tsibble.tidyverts.org/reference/fill_gaps.html).

```r
tsibble_flights_full <- fill_gaps(tsibble_flights, flights = 0)
tsibble_flights_full
```

```
## # A tsibble: 525,285 x 2 [1m] <?>
##    time                flights
##    <dttm>                <dbl>
##  1 2013-01-01 05:15:00       1
##  2 2013-01-01 05:16:00       0
##  3 2013-01-01 05:17:00       0
##  4 2013-01-01 05:18:00       0
##  5 2013-01-01 05:19:00       0
##  6 2013-01-01 05:20:00       0
##  7 2013-01-01 05:21:00       0
##  8 2013-01-01 05:22:00       0
##  9 2013-01-01 05:23:00       0
## 10 2013-01-01 05:24:00       0
## # ... with 525,275 more rows
```

`tsibble` also has three different types of moving windows: `slide()`, `tile()`, and `stretch()`, which return lists. You can see a great vizualization of it in the [tsibble tutorial](https://tsibble.tidyverts.org/), under "rolling window animation". For size reasons (there are 525,285 minutes in the dataset), let's use a daily count, rather than a minute-by-minute count.

You can create daily data using the `tile()` function, which creates windows without overlapping observations.

```r
tsibble_flights_tile <- tile(tsibble_flights_full$flights, sum, .size = 1440) %>% unlist() %>% matrix() %>% as.data.frame()
head(tsibble_flights_tile)
```

```
##    V1
## 1 843
## 2 943
## 3 914
## 4 915
## 5 720
## 6 832
```

Let's compare this to `apply.daily()`, the function we used from `xts` to aggregate our minute-level time series to a daily time series.

```r
head(xts_daily)
```

```
##                     [,1]
## 2013-01-01 23:59:00  842
## 2013-01-02 23:59:00  943
## 2013-01-03 23:59:00  914
## 2013-01-04 23:59:00  915
## 2013-01-05 23:59:00  720
## 2013-01-06 23:59:00  832
```
Notice how these results are similar to xts_daily, though not the same. The `tile()` function cuts the window off every 1440 minutes, or one full day. Each "daily count", or time unit, is from 5:15:00 on day t to 5:14:00 on day t+1. The `apply.daily()`, on the other hand, uses the actual date itself as the cutoff. A flight that took off at 1:22:00 AM on February 2nd would be aggregated differently when using `apply.daily()` versus `tile()`.

*Importantly*: I am showing you this to make an instructive case about how decisions you make can impact the way your data is processed. To create aggregations using `tsibble`, I encourage you to look into `index_by()`, which you can learn more about [here](https://blog.earo.me/2018/02/06/tsibble-or-tibbletime/){target="_blank"}.

The next moving window function in `tsibble` is `slide()`, a traditional moving window with overlapping observations.

```r
tsibble_flights_slide <- slide(tsibble_flights_tile$V1, ~ mean(., na.rm = TRUE), .size = 5)
tsibble_flights_slide[1:10]
```

```
## [[1]]
## [1] NA
## 
## [[2]]
## [1] NA
## 
## [[3]]
## [1] NA
## 
## [[4]]
## [1] NA
## 
## [[5]]
## [1] 867
## 
## [[6]]
## [1] 864.8
## 
## [[7]]
## [1] 862.8
## 
## [[8]]
## [1] 859.8
## 
## [[9]]
## [1] 857.2
## 
## [[10]]
## [1] 899.6
```

Finally, `strech()` begins with a fixed window. For every number of steps (we'll use 3), `stretch()` expands its window. 

```r
tsibble_flights_stretch <- stretch(tsibble_flights_tile$V1, sum, .step = 3)
tsibble_flights_stretch[1:10]
```

```
## [[1]]
## [1] 843
## 
## [[2]]
## [1] NA
## 
## [[3]]
## [1] NA
## 
## [[4]]
## [1] 3615
## 
## [[5]]
## [1] NA
## 
## [[6]]
## [1] NA
## 
## [[7]]
## [1] 6100
## 
## [[8]]
## [1] NA
## 
## [[9]]
## [1] NA
## 
## [[10]]
## [1] 8833
```

You can learn more about these functions through [this vignette](https://cran.r-project.org/web/packages/tsibble/vignettes/window.html){target="_blank"}.

We've only scratched the surface of what each of these packages can do. It takes a bit of time to figure out which packages are compatable with which, and so on, but having two or three of these in your "time series toolbox" will definitely help you wrangle whatever kind of data you need into an appropriate time series format. 
