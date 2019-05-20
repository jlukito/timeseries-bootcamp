# Time Series Boot Camp
Materials by Josephine Lukito and Jordan Foley

Time series has become an increasingly popular method in communication research. Researchers from many subfields, including Health Communication, Human-Computer Interaction, and Political Communication, now use time series methods to test a plethora of theories. This is further aided by the availability of "big data", which often includes highly granular temporal data (e.g., timestamps of when social media messages were posted).

Given this, Jordan and I organized a three-day boot camp (or workshop series) for graduate students in our department to provide an overview of how to use time series in the programming language `R`. The materials in this repo are a modification of these materials. Though the workshop series was originally structured to be taught over three days, we have reorganized it for public consumption.

## Table of Content
Our tutorial is grouped into four topics:
* [**Data Wrangling**](https://github.com/jlukito/timeseries-bootcamp/tree/master/1_wrangling) - Because time series data deals with date-time objects, we need special packages and tools to do calculations (e.g., subtract or add hours) and wrangle the data. 
* [**Univariate Analysis**](https://github.com/jlukito/timeseries-bootcamp/tree/master/2_univariate) - Our first day of the boot camp focused on the theory and practice of univariate time series analysis. We focused here on the Autoregression (AR), Integrated (I), Moving Average (MA) model, or ARIMA model.
* [**Multivariate Analysis**](https://github.com/jlukito/timeseries-bootcamp/tree/master/3_multivariate) - Our second day of the boot camp focused on the theory of multivariate time series analysis, focusing on the well-known Vector Autoregression (VAR) model, Granger Causality tests, and Impulse Response Functions. We practiced this on the third day of our boot camp.
* [**Data Visualization**](https://github.com/jlukito/timeseries-bootcamp/tree/master/4_visualization) - Many time series dynamics are best understood when displayed visually. For example, seasonal components (like a weekly dip in news stories) should be visibly evident when plotting a time series. On our third day, we discussed how to plot time series data and create interactive line graphs.

## Meet Our Organizers
**Josephine Lukito** is a Ph.D student in UW-Madison's School of Journalism and Mass Communication, studying linguistics and political communication. She led the department’s Computational Methods Research Group (2016-2019).

**Jordan Foley** is a Ph.D candidate in UW-Madison's School of Journalism and Mass Communication, studying political communication. He also serves as the Assistant Director of Debate at UW-Madison. 

## Acknowledgement
We would like to extend our thanks to the UW-Madison School of Journalism and Mass Communication for providing us with a location to run this boot camp and to all our attendees for participating. We would also like to thank Dr. Dhavan Shah, Dr. Chris Wells, and Dr. Jon Pevehouse for their support as we organized these materials.

## Questions?
For more information, or for permission to use these materials, please contact [jlukito@wisc.edu](mailto:jlukito@wisc.edu).
