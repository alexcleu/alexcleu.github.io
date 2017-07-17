---
layout: post
title: Practical Reporting with R and Google Sheet.
tags: [r_business, r, dplyr, reporting]
---


Quick Reporting with R and Google Sheet.
----------------------------------------

The idea is that at startups, we don't really have the luxury to create an end to end reporting to leaderships/managers that needed more in a quicker turnaround. Imagine how 10 users had asked you for a report that is updated on a daily basis, and you had to 1) pull in the query, 2) drop it in as csv file and 3) send it to the end client. The purpose of this article is to automate this whole thing until a more sustatinable reporting system can be built out.

First we preload a couple of packages that we will be using.
``` r
library(dplyr)
library(tidyr)
library(stringr)
library(googlesheets)
library(lubridate)
```
Next we use some dummy data to play with. Let's pull in some dummy data from iris.

``` r
flower_df <- iris
flower_df %>% head() 
```

    ##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
    ## 1          5.1         3.5          1.4         0.2  setosa
    ## 2          4.9         3.0          1.4         0.2  setosa
    ## 3          4.7         3.2          1.3         0.2  setosa
    ## 4          4.6         3.1          1.5         0.2  setosa
    ## 5          5.0         3.6          1.4         0.2  setosa
    ## 6          5.4         3.9          1.7         0.4  setosa

Connect to googlesheet.
-----------------------

Next, let's connect to google sheet with our pre-created sheet with gs id '1TU-9Z9ixoa5yicebijub1Og0tyGzhZE-E2kYHy6WkGE'

When you first added in googlesheet, you need to provide permission for R to access to your sheet. To do so, run `(my_sheets <- gs_ls())` for authentication.

``` r
gs_input_sheet <- gs_key('1TU-9Z9ixoa5yicebijub1Og0tyGzhZE-E2kYHy6WkGE') 
```

    ## Sheet successfully identified: "testing iris "

``` r
gs_input_sheet %>%
  gs_edit_cells(ws='Sheet1', input=flower_df)
```

    ## Range affected by the update: "R1C1:R151C5"

    ## Worksheet "Sheet1" successfully updated with 755 new value(s).

Awesome! It's now pasted to the sheets. If the user wanted to the data to be printed into PDFs, we can also do that too with googlesheet.

``` r
gs_input_sheet %>%
  gs_download(ws='Sheet1', to='iris.pdf', overwrite=TRUE)
```

    ## Accessing worksheet titled 'Sheet1'.

    ## Sheet successfully downloaded:
    ## /Users/alexleu/iris.pdf

To send your result to the end user, you can try using mailR to do the trick.

Conslusion:
-----------

You can use this tip to send adhoc-analysis to users. To implement it further, you can also add in a mailR packages to notify the user the sheet has been updated.

Additionally, you can also put in some input sheet, where you can pick up the user's input to do more data wrangling.

Happy Data Science!

