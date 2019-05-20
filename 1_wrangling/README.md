# Data Wrangling
In this module, we'll go over how to wrangle date-time data for time series analysis. `R` has many packages for managing date-time objects; we discuss `xts`, `lubridate`, `zoo`, and `tsibble` here.

##useful Keywords
* **datetime** - A type of data structure that includes information about both date and time.
* **date** - A type of data structure that **only** includes date information.
* **tidy data** - Data that has been wrangled to be [tidy](https://vita.had.co.nz/papers/tidy-data.pdf), where "each variable is a column, each observation is a row, and each type of observational unit is a table." For **Tidy time series data**, each observation should be a unique time point, and all data should be organized chronologically, with no gaps.
  * Not all time series models require regularly spaced data, but the ones we discuss do.
 * **temporal aggregation** - This refers to the process of grouping, or aggregating, data, from smaller time units (e.g., minutes) to larger time units (e.g., hours).
