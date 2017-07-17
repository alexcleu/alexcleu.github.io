---
layout: post
title: Practical Reporting with R and Google Sheet.
tags: [r_business, r, dplyr, reporting]
---

```{r}
suppressMessages(library(lubridate))       # Date Manipulation Library

suppressMessages(library(dplyr))
suppressMessages(library(stringr))
library(dplyr)
library(tidyr)
library(stringr)
library(googlesheets)
library(lubridate)
```

## Quick Reporting with R and Google Sheet.

The idea is that at startups, we don't really have the luxury to create an end to end reporting to leaderships/managers that needed more in a quicker turnaround. Imagine how 10 users had asked you for a report that is updated on a daily basis, and you had to 1) pull in the query, 2) drop it in as csv file and 3) send it to the end client. The purpose of this article is to automate this whole thing until a more sustatinable reporting system can be built out. 

First we use some dummy data to play with. Let's pull in some dummy data from iris.

```{r iris}
flower_df <- iris
flower_df %>% head() 
```

## Connect to googlesheet.

Next, let's connect to google sheet with our pre-created sheet with gs id '1TU-9Z9ixoa5yicebijub1Og0tyGzhZE-E2kYHy6WkGE'

When you first added in googlesheet, you need to provide permission for R to access to your sheet.
To do so, run `(my_sheets <- gs_ls())` for authentication. 

```{r}
gs_input_sheet <- gs_key('1TU-9Z9ixoa5yicebijub1Og0tyGzhZE-E2kYHy6WkGE') 
```

```{r}
gs_input_sheet %>%
  gs_edit_cells(ws='Sheet1', input=flower_df)
```
Awesome! It's now pasted to the sheets.
If the user wanted to the data to be printed into PDFs, we can also do that too with googlesheet.
```{r}
gs_input_sheet %>%
  gs_download(ws='Sheet1', to='iris.pdf', overwrite=TRUE)
```

To send your result to the end user, you can try using mailR to do the trick.

## Conslusion:
You can use this tip to send adhoc-analysis to users. To implement it further, you can also add in a mailR packages to notify the user the sheet has been updated. 

Additionally, you can also put in some input sheet, where you can pick up the user's input to do more data wrangling.

Happy Data Science!
