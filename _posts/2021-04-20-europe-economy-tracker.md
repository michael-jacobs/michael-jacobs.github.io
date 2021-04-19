---
title: "European Economic Indicators - tracking the impact of COVID19"

tags:
  - API
  - Shiny
  - Data Visualisation
  - R
---

This is the first of (hopefully!) many blog posts, where I explore data freely available online, with the aim of creating a thought-provoking data visualisation.  


In this first instalment I am working with Economic data that can be accessed directly from Eurostat website via its API, with the overall aim of shedding 
some light on the economic impact of the COVID19 pandemic to date. As the Statistical office of the European Commission Eurostat is responsible for gathering 
comparable data from European member states in a variety of policy areas, meaning the API is a rich source of data and information. Data for non/EU countries 
are also available for comparison purposes, including Japan, the United Kingdom* and the United States in the case of the economic indicators used in the following example.

Accessing the data

Firstly, I will explain briefly the process for accessing the data from the API (using R), before moving on to the Shiny application itself and some of the conclusions 
from the analysis. Thankfully, extracting the data is relatively straight forward, thanks to the Eurostat package. Having loaded the package, a full list of codes and
related descriptions can be downloaded. 

```r
## Load required libraries##
library(eurostat)
library(dplyr)
library(xlsx)

## Download full list of available data##
toc <- get_eurostat_toc()

view(toc)

```
A table with eight variables (as shown below) is downloaded, detailing the catalogue of datasets available from the API - you can review and filter to identify the data sets of interest. 
Alternatively, the [RAMON database](https://ec.europa.eu/eurostat/ramon/nomenclatures/index.cfm?TargetUrl=LST_NOM&StrGroupCode=SCL&StrLanguageCode=EN) is available and  
searchable online, to assist with identifying the code(s) required for downloading of the data.

| title | code | type | last update of data | last table structure change | data start | data end | values |
|-------|------|------|---------------------|-----------------------------|------------|----------|--------|
|       |      |      |                     |                             |            |          |        |
|       |      |      |                     |                             |            |          |        |

As you can see from the R code below, the data I require - related to unemployment, gross domestic product (GDP) and consumer prices - are accessed using four codes in total.

```r
##Define codes to download
codes <- c("ei_cphi_m","ei_lmhr_m","prc_hicp_inw", "namq_10_gdp", "naidq_10_gdp")
codes <- filter(toc, code %in% codes)

## Download data sets as listed in codes and save as .csv##
for(i in 1:length(codes)){
  
  tmp <-get_eurostat(codes$code[i], type = "label")

##Add general title from the codes table
  tmp$title <-codes$title[i]

##Rename variable with information on date of update  
  tmp$update <-codes$`last update of data`[i]

##Add field to denote when the data were accessed
 tmp$apidate <-Sys.time()
  
    
  ##Save data in .csv format##
  write.csv(tmp, paste(codes$code[i],".csv", sep = ""))
}
```
**To note** – the dimensions included in the different datasets may differ, meaning some cleaning or re-shaping of the data will be required if you intend to create one large data table. 
Be sure also to consider the format of the date fields when creating your main data.

