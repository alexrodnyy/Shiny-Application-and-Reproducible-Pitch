---
title       : K-means plot for mtcars dataset
subtitle    : Getting started with reactivity k-means plot output by slider numeric input
author      : 
job         : Assignment - Course Project - Shiny Application and Reproducible Pitch
framework   : io2012        # {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight.js  # {highlight.js, prettify, highlight}
hitheme     : tomorrow      # 
widgets     : []            # {mathjax, quiz, bootstrap}
mode        : selfcontained # {standalone, draft}
knit        : slidify::knit2slides
---

## About this ShinyApp

### Who would use this?

* for someone who wants to by a car (1973/74 models) to understand main characteristics
* for researches who want to find some dependences to visualize first steps of an analisys
* others

### How to use this?

* choose first variable to plot on X Variable
* choose second varianle to plot on Y Variable
* choose number for K-means clustering for this pair

--- .class #id 

## Data set: Motor Trend Car Road Tests

### Description

The data was extracted from the 1974 Motor Trend US magazine, and comprises fuel consumption and 10 aspects of automobile design and performance for 32 automobiles (1973/74 models).

### Format

A data frame with 32 observations on 11 variables.

[, 1]        mpg	Miles/(US) gallon  
[, 2]	cyl	Number of cylinders  
[, 3]	disp	Displacement (cu.in.)  
[, 4]	hp	Gross horsepower  
[, 5]	drat	Rear axle ratio  
[, 6]	wt	Weight (lb/1000)  
[, 7]	qsec	1/4 mile time  
[, 8]	vs	V/S  
[, 9]	am	Transmission (0 = automatic, 1 = manual)  
[,10]	gear	Number of forward gears  
[,11]	carb	Number of carburetors  

### Method: K-means clustering
k-means clustering is a method of vector quantization, originally from signal processing, that is popular for cluster analysis in data mining. k-means clustering aims to partition n observations into k clusters in which each observation belongs to the cluster with the nearest mean, serving as a prototype of the cluster.

--- .class #id 

## ui.R code


```r
shinyUI(pageWithSidebar(
        headerPanel('Motor Trend Car k-means clustering'),
        sidebarPanel(
                selectInput('xcol', 'X Variable', names(mtcars)),
                selectInput('ycol', 'Y Variable', names(mtcars),
                            selected=names(mtcars)[[2]]),
                numericInput('clusters', 'Cluster count', 3,
                             min = 1, max = 9)
        ),
        mainPanel(
                plotOutput('plot1')
        )
))
```

--- .class #id 

## server.R code


```r
shinyServer(function(input, output, session) {
        
        # Combine the selected variables into a new data frame
        selectedData <- reactive({mtcars[, c(input$xcol, input$ycol)]})
        
        clusters <- reactive({kmeans(selectedData(), input$clusters)})
        
        output$plot1 <- renderPlot({
                par(mar = c(5.1, 4.1, 0, 1))
                plot(selectedData(),
                     col = clusters()$cluster,
                     pch = 20, cex = 3)
                points(clusters()$centers, pch = 4, cex = 4, lwd = 4)
        })
})
```
