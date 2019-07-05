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













