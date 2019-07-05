---
title: "Mapping the article 50 petition in R and ggplot"
author: "Glyn Mottershead"
date: '2019-03-29'
slug: mapping-the-article-50-petition-in-r-and-ggplot
categories: ["R", "Mapping", "Data Visualisation"]
tags: ["json", "map", "ggplot2"]
draft: false


---
# Mapping in R - using ggplot2
The [Revoke Article 50 petition](https://petition.parliament.uk/petitions/241584/) got my colleague and all-round codemeister [Dr Martin Chorley](https://twitter.com/martinjc) and I talking.

We started thinking about ways that we could see what the patterns for people signing were like.
  
It was well into the millions when I started playing with ways of visualising where people who voted were located. The site can map all of the signatures (5,962,824 at the time of writing), but it also has an option to get the data in a [machine-friendly json format] (https://petition.parliament.uk/petitions/241584.json).  

According to the site:   
*The data shows the number of people who have signed the petition by country as well as in the constituency of each Member of Parliament. This data is available for all petitions on the site. It is not a list of people who have signed the petition. The only name that is shared on the site is that of the petition creator.*

## Getting started
You'll need to install the following packages:
`install.packages(c("geojsonio", "ggplot2", "dplyr", "jsonlite"))`

## Getting the map shapes
We're going to use *geojsonio* first to get a mapping file from the [ONS Open Geography Portal](http://geoportal.statistics.gov.uk/). It has a great repository of mapping files. We'll be using the parliamentary wards file. Go to the site and use the menu bar to:
*Boundaries > Electoral Boundaries > Westminster Parliamentary Constituencies > 2017 Boundaries*  
You can download the file in a variety of formats, but we're going to use the API to import it directly in GeoJSON format.  
  
**NB At the time of writing there appeared to be a glitch in the site, I actually found the right map home page via a search engine.**

First we use the `library()` function to call geojsonio to handle the file, we'll store the URL as a variable and then read it in to our working environment. The 'what' argument uses "sp" - spacial class for a mapping file.  


```r
library(geojsonio)
```

```
## 
## Attaching package: 'geojsonio'
```

```
## The following object is masked from 'package:base':
## 
##     pretty
```

```r
url <- "https://opendata.arcgis.com/datasets/5ce27b980ffb43c39b012c2ebeab92c0_2.geojson"

uk_map <- geojson_read(url, what = "sp")
```

We then need to turn it from the form it is in to something we can map more easily in ggplot2, so we'll call the library here and use the `fortify()` function. 

By having a look in side the uk_map dataframe we can see our code names for the constituencies are stored in **pcon17cd**, so we'll add that as our region.



```r
library(ggplot2)
```

```
## Registered S3 method overwritten by 'dplyr':
##   method         from     
##   print.location geojsonio
```

```r
fort_uk_map <- fortify(uk_map, region = "pcon17cd")
```


## Getting the data for our map
We're now going to read in the data from the Parliament Petitions site. We'll use **jsonlite** to do that.


```r
library(jsonlite)
```

```
## 
## Attaching package: 'jsonlite'
```

```
## The following object is masked from 'package:geojsonio':
## 
##     validate
```

```r
json_data <- fromJSON("https://petition.parliament.uk/petitions/241584.json", flatten = FALSE)
```

The next thing we need to do is get the data out of the json file we just imported. If you click on the json_data object in the Environment pane, you'll see it is a list of two - double click to open it up and we cab view the file.  Inside the json-data structure we can see data has a list of three objects inside it, opening that shows us attributes is where the interesting things are happening.  

There's a lot going on but there are two things that interest me for mapping - signatures_by_constituency and signatures_by_country (this second one is for a later date).  

Opening the signatures_by_country list shows it has the following elements **name, ons_code, mp, signature_count** for each of the 650 constituencies in the file.  The ons_code will come in useful later when we want to merge our map and data together.

We can move through the levels of our json_data object  in this fashion `name$parent_element$child_element`. 

So in our case:
`sign_data <- json_data$data$attributes$signatures_by_constituency`

We'll store that in a dataframe and while we're at it we'll calculate how many signatures there were at the time of running the code, I'll do this as a dataframe as it will be useful in the second tutorial.


```r
sign_data <- json_data$data$attributes$signatures_by_constituency


total_sig <- sum(sign_data$signature_count)

total_sig
```

```
## [1] 5728351
```

## Joining the data sets
This is where **dplyr** comes into its own as a data-wrangling toolkit. We'll call the library and then use a `left_join()` to merge them together into a new dataframe called **full_uk_map**. There's an explanation of join types on the [tidyverse site](https://dplyr.tidyverse.org/reference/join.html).  

To do the join we have to tell the function where our common columns are in the 'by' element -- `left_join(dataset1, dataset2, by = c("a_column" = "the_equivalent_column"))`


```r
library(dplyr)
```

```
## 
## Attaching package: 'dplyr'
```

```
## The following objects are masked from 'package:stats':
## 
##     filter, lag
```

```
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
```

```r
full_uk_map <- left_join(fort_uk_map, sign_data, by = c("id" = "ons_code"))
```


## Basic ggplot2 map
We'll start off with a simple map that shows which constituency people are signing from, so we need to load ggplot2 as a function. I'll break down the structure below for what we are doing here.


```r
# Call ggplot here as a function and use the '+' symbol to denote 'and then'
ggplot() +
  # We'll use geom_polygon() and tell it where the data is, what our aesthetics are and what to fill how to create it as a choropleth map.
  geom_polygon(data = full_uk_map, aes(x = long, y = lat, group = group, fill = signature_count)) +
  # We'll put a white stroke on the constituency boundaries
  geom_path(color = "white") +
  # Get rid of the background
  theme_void() +
  # And finally let's use coord_equal to ensure the x and y scales are the same.
  coord_equal()
```

<div class="figure">
<img src="/tutorial/2019-03-29-mapping-the-article-50-petition-in-r-and-ggplot_files/figure-html/map-1.png" alt="A simple choropleth map." width="672" />
<p class="caption">Figure 1: A simple choropleth map.</p>
</div>


## Next steps
I've also been playing with a great post from Timo Grossenbaher on how to [make beatiful thematic maps with ggplot2](https://timogrossenbacher.ch/2016/12/beautiful-thematic-maps-with-ggplot2-only/) to create something a bit more effective.

Now pop along to [stage two of this tutorial]({{< relref "2019-04-01-mapping-the-article-50-petition-in-r-and-ggplot-part-2" >}}) which goes further and looks at making things more interesting.

## More analysis
Andy Dickinson from Manchester Met has done a [Pandas (Python) look at the article 50 and knife crime petitions](https://andydickinson.net/2019/03/27/quick-analalysis-of-petitions-data).





