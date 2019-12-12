# Multivariate Analysis
Finally, we have reached the point where we can begin to do some multivariate analysis. Though there are many multivariate models, we'll focus on the Vector Autoregression (VAR) model, which is popular in communication research. We'll also go over how to do Granger causality tests and how to plot Impulse Response Functions (IRFs).

This folder contains three important files.
1. The **slidedeck** provides an overview of Vector Autoregression Models (including references). We recommend that you start with this.
2. The **varmodeling** markdown file is a tutorial of how to run your first VAR model, Granger causality tests, and IRFs. 
3. Data from this tutorial can be found in the **brexit_exercise_data.csv**. We also use this data for our [data visualization topic](https://github.com/jlukito/timeseries-bootcamp/tree/master/4_visualization).

## The Data
This dataset contains the following 7 variables:
1. date: Tidy time series data's temporal unit (daily, for 1 year)
2. guardian: count of articles published by The Guardian using the word "brexit"
3. timesofindia: count of articles published by Times of India using the word "brexit"
4. german: count of articles published by German news outlets in the [MediaCloud collection](https://sources.mediacloud.org/#/collections/34412409) using the word "brexit"
5. nyt: count of articles published by The New York Times using the word "brexit"
6. approval: public approval of Brexit
7. event: significant Brexit events, as identified from [Wikipedia](https://en.wikipedia.org/wiki/Brexit#2019)
