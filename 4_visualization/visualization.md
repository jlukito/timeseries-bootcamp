Time Series Data Visualizations
================
Josephine Lukito (<jlukito@wisc.edu>)
May 18, 2019

And finally... data visualization! We'll be primarily using two packages, the famous `ggplot2` (), and `plotly`, which is used to make interactive plots. If you would like to export that plot, you will need to have a free account with plot.ly. At some point, we will convert our data to `xts` so make sure you have that package downloaded.

``` r
library(tidyverse)
library(forecast)
library(ggplot2)
library(reshape2)
library(xts)
library(knitr)
```

We return to our original tidied time series (`brexit_full`). For now, let's focus on the news outlets (approval and events are really working on different scales, and there are disproportiontely more German media articles).

Importing data
==============

``` r
brexit_full <- read.csv("brexit_exercise_data.csv", header = TRUE, sep = ",", stringsAsFactors = F) %>% 
  as.data.frame()
brexit_dates <- as.Date(brexit_full$date, format = "%m/%d/%Y")
brexit_full$event <- NULL #deletes this column
brexit_full$approval <- NULL #deletes this column
brexit_full$german <- NULL #deletes this column

knitr::kable(head(brexit_full))
```

| date      |  guardian|  timesofindia|  nyt|
|:----------|---------:|-------------:|----:|
| 3/12/2018 |        16|             1|    0|
| 3/13/2018 |        20|             2|    0|
| 3/14/2018 |        18|             2|    2|
| 3/15/2018 |        16|             1|    3|
| 3/16/2018 |        20|             2|    1|
| 3/17/2018 |        17|             1|    6|

One thing to note is that a line graph will look particularly noisy at the daily level. You may want to aggregate up to the week (or month) for your plot.

``` r
guardian_weekly <- ts(brexit_full$guardian) %>% xts(order.by = brexit_dates) %>% apply.weekly(sum) #weekly sum
toi_weekly <- ts(brexit_full$timesofindia) %>% xts(order.by = brexit_dates) %>% apply.weekly(sum) #weekly sum
nyt_weekly <- ts(brexit_full$nyt) %>% xts(order.by = brexit_dates) %>% apply.weekly(sum) #weekly sum

brexit_weekly <- data.frame(date = index(guardian_weekly),
                            guardian = guardian_weekly,
                            timesofindia = toi_weekly, 
                            nyt = nyt_weekly)
```

There are a lot of different way to do this process. Here, I convert everything to an xts and rely on the `apply.weekly()` function. You can look at the other ways to do this in our wrangling tutorial.

Alright, so let's actually plot this data! Tidy time series data can be tricky to put into ggplot neatly. You can treat each observation as a `geom_line()`, but the code is not particularly efficient (or dynamic). A way around this is the `melt` function in the `reshape2` package. This function will "melt" all your variables into one factor variable. This is a big hard to explain without visualizing it, so let's see the function in action below.

``` r
brexit_melted <- reshape2::melt(brexit_weekly, c("date")) 

levels(brexit_melted$variable)[levels(brexit_melted$variable)=="timesofindia"] <- "The Times of India"
levels(brexit_melted$variable)[levels(brexit_melted$variable)=="guardian"] <- "The Guardian"
levels(brexit_melted$variable)[levels(brexit_melted$variable)=="nyt"] <- "The New York Times"
```

If you `View(brexit_melted)`, you'll notice that there is now a `variable` column that treats your variables (anything you didn't list) as factors.

Plotting
========

``` r
brexit_melted$variable <- as.character(brexit_melted$variable)

ggplot(brexit_melted, aes(x = as.Date(date), y = value, color = variable)) + 
  geom_line(size=1) +
  labs(title="Daily counts of articles about Brexit in the past year, across multiple news media",
     x="", y = "Number of Tweets") +
  theme_minimal() +
  scale_color_manual(values=c('blue','green', 'red'))+
  theme(axis.title.x=element_blank(),
      axis.text.x=element_blank(),
      axis.ticks.x=element_blank()) +
  theme(axis.text.x = element_text(angle = 0, hjust = 0), legend.position="bottom") 
```

![](visualization_files/figure-markdown_github/plotty-1.png)

You can save this plot using ggsave (you can get much larger images, with much better DPI, using this rather than any save icon.

``` r
ggsave("brexit_articles.png", plot = last_plot(), width = 10, height = 5, units = "in", dpi = 600)
```

Plotly
======

Finally, I'll show you how to make this an interactive plot using `plotly`. To do this, you'll need a free plotly account, which you can register for <a href="https://plot.ly/" target="_blank">here</a>. First, you'll save your `ggplot` plot as a object. You'll then use the `ggplotly()` function in the `plotly` package to convert a ggplot2 plot into a plotly one.

``` r
library(plotly)
#Sys.setenv("plotly_username"="USERNAME")
#Sys.setenv("plotly_api_key"="API KEY FROM WEBSITE")

savethis <- ggplot(brexit_melted, aes(x = date, y = value, color = variable)) + 
  geom_line(size=1) +
  labs(title="Daily counts of articles about Brexit in the past year, across multiple news media",
     x="", y = "Number of Tweets") +
  theme_minimal() +
  scale_color_manual(values=c('blue','green', 'red')) +
  theme(axis.title.x=element_blank(),
      axis.text.x=element_blank(),
      axis.ticks.x=element_blank())

plottage <- ggplotly(savethis)
plottage
api_create(plottage, filename = "brexit_articles")
```

From here, you should be directed to the plotly site, where you can customize the plot further, or get the html/javascript code to upload to your website.
