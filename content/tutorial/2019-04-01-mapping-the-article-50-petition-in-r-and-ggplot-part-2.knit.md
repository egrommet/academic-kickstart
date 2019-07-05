---
title: Mapping the article 50 petition in R and ggplot - part 2
author: Glyn Mottershead
date: '2019-04-01'
slug: mapping-the-article-50-petition-in-r-and-ggplot-part-2
categories:
  - R
  - Data Visualisation
  - Mapping
tags:
  - ggplot2
  - json
  - map
image:
  caption: ''
  focal_point: ''

---
![The final map](/tutorial/2019-04-01-mapping-the-article-50-petition-in-r-and-ggplot-part-2_files/Plot_Zoom.png)

# Mapping in R - using ggplot2, part two
In the previous tutorial we looked at getting data from web APIs in JSON and GeoJSON formats to create a simple map.

This time we'll be adapting code from [Timo Grossenbacher](https://timogrossenbacher.ch/2016/12/beautiful-thematic-maps-with-ggplot2-only/ "Timo Grossenbacher's post on how to make beautiful plots in ggplot2") to make our map more attractive.

## Getting started
We need to reload the data again, so we're going to get a different number of signatures than last time.

I'm going to run this in one block and assume you can follow along, if not [go back to the previous post]({{< relref "2019-03-29-mapping-the-article-50-petition-in-r-and-ggplot.markdown" >}}).



















