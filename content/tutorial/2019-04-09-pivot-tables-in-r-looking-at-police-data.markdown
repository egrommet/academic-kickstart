---
title: Pivot tables in R - looking at police data
author: Glyn Mottershead
date: '2019-04-09'
slug: pivot-tables-in-r-looking-at-police-data
categories:
  - R
tags:
  - Police
image:
  caption: ''
  focal_point: ''
---
# Looking at police data using Tidyverse tools
The Tidyverse tools are one of my favourite things and made my R coding experience much simpler. Most of the things I'll be posting will use them in one way or another.

We're going to find out what happens when someone reports a stolen bicycle - a friend seems to believe the police do nothing, so let's investigate what actually happens.

## Downloading the data
I've downloaded a month's worth of data for South Wales Police from [data.police.uk](https://data.police.uk/data/). 

I'm going to do the R equivalent of a pivot table, grouping information together and then analysing it.

## Getting started
Open RStudio and go to **FILE > NEW PROJECT** then create it in a new folder. This will give you a chance to choose where you want it to go. I tend to have a project folder and then each individual project is stored inside that.

We'll leave RStudio for a second and head into the computer's file manager to find the folder we just created in RStudio. Inside that I always create a folder called data, I'll then put my downloaded data inside it.

## Create a script
Back inside RStudio, I'm going to go to **File menu > New File > R Script**. I'm doing this as it allows me to repeat this analysis when I want to. You'll want to give it a name that makes sense and then save it. R files have a **.R** file extension at the end.

## Installing your first R Package
A package is a tool, or group of tools, that someone has created to cut out some of the repetition in R. We'll be using the tidyverse by Hadley Wickham. These are a set of really helpful data tools that also make R programming easier to read.

We can install packages by going to the **Tools menu > Install Packages** and type the name into the pop-up box.

Or I can write the command (known as a function) into my script, highlight my line of code and hit the **RUN** button at the top of the script. The other way to do it is a line of code - `install.packages("tidyverse")`.


I then need to load it in my script. This is done by the `library()` function. Again, run the line of code.

```r
library(tidyverse)
```

## Loading data
Even though we have the data set in our R project folder we need to load it up.

I'm going to use `read_csv()` from readr to read a CSV file into our RStudio environment.

If you ever want to know how what a package or function does then type `?function_name` in the console eg `?read_csv`, and you'll get a help message.

Our function wrapping works like this `read_csv()`.

We need to put the address (** aka file path**) for our CSV inside quote marks inside the inner pair of brackets.
It will look like this - `read_csv("file_path")`.

The full syntax (not the working code I want to use) looks like this:
`data_frame_name <- read_csv("file_path")`

The **<-** is an assignment symbol, I tend to think of it as a hosepipe which allows the function to pour into the name we give our data frame. 

My file path in the example below says **./** inside the folder we are currently in is a folder called **data** and **/** inside that is a csv file.


```r
crimes <- read_csv("./data/2019-01-south-wales-street.csv")
```

```
## Parsed with column specification:
## cols(
##   `Crime ID` = col_character(),
##   Month = col_character(),
##   `Reported by` = col_character(),
##   `Falls within` = col_character(),
##   Longitude = col_double(),
##   Latitude = col_double(),
##   Location = col_character(),
##   `LSOA code` = col_character(),
##   `LSOA name` = col_character(),
##   `Crime type` = col_character(),
##   `Last outcome category` = col_character(),
##   Context = col_logical()
## )
```
The output for this will show us what types of columns we have - in this case `col_character()` means text and `col_double()` means a number format. All good so far.

## Filtering with dplyr
I want to filter the information so it just includes bicycle thefts. I'm going to use some dplyr package tricks to do this. 

I'm going to store it in a new data frame called **bikes**. We'll be using the crimes data frame we made earlier and then filtering it, before assigning the new information to bikes. So `bikes <- crimes`.

The next bit of the code is `%>%` a pipe - essentially you can say **and then** to yourself when you write it. Our next function is a filter(), it does a similar job to the Excel function. 

Most functions will need you to say which data frame you are working on and then what you want to do. So we could say `filter(data_frame, column_name == "text we want back")`.

But dplyr knows we are working on the data frame before the first `%>%`. 



```r
bikes <- crimes %>%
  filter(`Crime type` == "Bicycle theft") 

# If I use the dataframe name on its own and run that line I can see what is in it
bikes
```

```
## # A tibble: 126 x 12
##    `Crime ID` Month `Reported by` `Falls within` Longitude Latitude
##    <chr>      <chr> <chr>         <chr>              <dbl>    <dbl>
##  1 0fad86217… 2019… South Wales … South Wales P…     -3.54     51.5
##  2 5ea5f1bb3… 2019… South Wales … South Wales P…     -3.19     51.5
##  3 ab5c7a5c7… 2019… South Wales … South Wales P…     -3.12     51.5
##  4 44e702929… 2019… South Wales … South Wales P…     -3.23     51.5
##  5 979ee8076… 2019… South Wales … South Wales P…     -3.09     51.5
##  6 34490753e… 2019… South Wales … South Wales P…     -3.12     51.5
##  7 59c9863ef… 2019… South Wales … South Wales P…     -3.12     51.5
##  8 7029c550b… 2019… South Wales … South Wales P…     -3.22     51.5
##  9 f8f063bad… 2019… South Wales … South Wales P…     -3.22     51.5
## 10 cdd878455… 2019… South Wales … South Wales P…     -3.20     51.5
## # … with 116 more rows, and 6 more variables: Location <chr>, `LSOA
## #   code` <chr>, `LSOA name` <chr>, `Crime type` <chr>, `Last outcome
## #   category` <chr>, Context <lgl>
```

The output will show us how many rows (observations) and columns (variables) there are - in this case a 126 x 12. We can start adding some more analysis now.

## Filtering and renaming for speed
You may notice when we printed the data frame that some of the column names have backticks around them. That's because they've got a space between the words and R is trying to help out. So, if we wanted to work with our Crime ID column we'd actually have to type ``Crime ID`` with the back ticks. 

I'm too lazy for things like that. I'd rather change the name so I can write something easier. We're going to use `rename()` to do it. We will tell `rename()` that our `"new column name" = "old column name"`  - but make sure you put the quote marks around the names when you do this.

So, I'm going to tweak my code from before. I'm going to change the name of the `Last outcome category` and the `Crime type` columns. I'm going to change them to Outcome and Crimes - much shorter to type.

So, we're telling dplyr to take the **crimes** data frame **and then** rename by assigning the new name to the old name. (The comma means we can do more than one rename at once, to do more just add a comma and then say "new" = "old" again.) **And then** filter our newly-named **Crimes** column and look for the text inside the quote marks.


```r
bikes <- crimes %>%
  rename("Outcome" = "Last outcome category", "Crimes" = "Crime type") %>%
  filter(Crimes == "Bicycle theft") 
```

## Doing a pivot table in dplyr syntax
Now we can do some digging. I want to know what the police have done about the reported bike thefts.

We can group things together by our Outcome category and count how often each of the outcomes happen. So, we tell `group_by()` which column we want to work on. **And then** we tell it what to count - in this case it creates a column called outcome_count and uses the `n()` function to count the Outcome.



```r
bikes <- crimes %>%
  rename("Outcome" = "Last outcome category", "Crimes" = "Crime type") %>%
  filter(Crimes == "Bicycle theft") %>%
  group_by(Outcome) %>%
  summarise(outcome_count = n())

# Again I'll type the dataframe name here and run this line to see what we have got.
bikes
```

```
## # A tibble: 4 x 2
##   Outcome                                       outcome_count
##   <chr>                                                 <int>
## 1 Awaiting court outcome                                    1
## 2 Investigation complete; no suspect identified            88
## 3 Unable to prosecute suspect                               1
## 4 Under investigation                                      36
```

We've got a pivot table - it has grouped the **Outcome column** and counted how often each one occurs.

## TLDR: "Investigation complete; no suspect identified" = "No suspect identified"
We're going to change an element by using the `mutate()` function - we're going to `recode()` one of our Outcome categories to shorten it. The code would look like this - `mutate(Outcome = recode(Outcome, "Investigation complete; no suspect identified" = "No suspect identified"))`

But I'd like to order it in descending order - so we can do one more **and then** `%>%` at the end of our code. We will `arrange()` our **outcome_count** in `desc()` - descending order. You must put `desc()` inside the `arrange()` function. And check your brackets.


```r
bikes <- crimes %>%
  rename("Outcome" = "Last outcome category", "Crimes" = "Crime type") %>%
  filter(Crimes == "Bicycle theft") %>%
  mutate(Outcome = recode(Outcome, 
                          "Investigation complete; no suspect identified" = "No suspect identified")) %>%
  group_by(Outcome) %>%
  summarise(outcome_count = n()) %>%
  arrange(desc(outcome_count))

# Again I'll type the dataframe name here and run this line to see what we have got.
bikes
```

```
## # A tibble: 4 x 2
##   Outcome                     outcome_count
##   <chr>                               <int>
## 1 No suspect identified                  88
## 2 Under investigation                    36
## 3 Awaiting court outcome                  1
## 4 Unable to prosecute suspect             1
```

And we've got it. A pivot table that tells us what has happened to the bike crimes in the original data set and has put them in descending order.
