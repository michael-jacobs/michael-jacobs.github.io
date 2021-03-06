---
title: "European Economic Indicators - accessing data from the Eurostat API"

tags:
  - API
  - R
---

This is the first of (hopefully!) many blog posts, where I explore data freely available online, with the aim of creating thought-provoking data visualisations and analysis.  

In this instalment I am working with Economic data that can be accessed directly from Eurostat via its [API](https://ec.europa.eu/eurostat/online-help/public/en/API_06_DataQuery_en/). The idea is to better understand the economic impact of the COVID19 pandemic to date. As the Statistical office of the European Union, Eurostat is responsible for gathering comparable data from member states in a variety of statistical domains, meaning the API is a rich source of free data and information. In some cases data for non-EU countries are also available for comparison purposes, including Japan, the United Kingdom<sup>[*](#myfootnote)</sup> and the United States in the case of the economic indicators used in the following example.  

In this post I will explain briefly the process for accessing the data from the API (using R), before moving on to a (Shiny) data application from which I will draw some conclusions from in a follow up blog. Thankfully, extracting the data itself is relatively straight forward, thanks to the [Eurostat package](http://ropengov.github.io/eurostat/articles/website/eurostat_tutorial.html). Having installed and initialised the package in R, a full list of contents of the API - including data codes, descriptions and other metadata - can be downloaded, as detailed in the script below.  

The resulting table of contents **(toc)** details the catalogue of datasets available from the API - you can review and filter to identify the data sets you would like to download. Alternatively, the [RAMON database](https://ec.europa.eu/eurostat/ramon/nomenclatures/index.cfm?TargetUrl=LST_NOM&StrGroupCode=SCL&StrLanguageCode=EN) is available and searchable online, to assist with identifying the code(s) required for downloading of the data of interest.

```r
## Load required libraries##
library(eurostat)

## Download full list of available data##
toc <- get_eurostat_toc()

view(toc)

```
  
For the data I want to analyse - related to unemployment, gross domestic product (GDP) and consumer prices (including associated expenditure weights) - four seperate codes are needed, each referring to specific data sets. The R-code below extracts and saves the required data from the API and does some minor reformatting of the information before saving the downloaded data in a series of .csv files (each named after the relevant database code). 

```r
library(dplyr)

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

**To note** – the dimensions included in the different datasets may differ, meaning some re-shaping of the data will be required if you intend to create one large data table combining each of the seperate data sources. Be sure also to consider the format of the date fields when creating your main data table to save headaches later on. Finally, some of the datasets can be large, so give thought to the precise data you need before you start downloading. The Eurostat package can limit the slice of data you pick up, for example by narrowing down the reference period - more details on how to do this, and other options, are availale here: [Eurostat package.](http://ropengov.github.io/eurostat/articles/website/eurostat_tutorial.html)  

In order to make the data easier to use for visualisation purposes some additional reformatting was also required, as detailed in the R-script below. 

```r
##Shorten data descriptions and country names as required

data$s_adj <-gsub("Calendar adjusted data, not seasonally adjusted data","calendar adjusted",data$s_adj)
data$s_adj <-gsub("Unadjusted data \\(i\\.e\\. neither seasonally adjusted nor calendar adjusted data\\)","unadjusted",data$s_adj)
data$s_adj <-gsub("Seasonally adjusted data, not calendar adjusted data","seasonally adjusted",data$s_adj)
data$s_adj <-gsub("Seasonally and calendar adjusted data","seasonally and calendar adjusted",data$s_adj)
data$geo <-gsub("Germany \\(until 1990 former territory of the FRG\\)","Germany",data$geo)
data$geo <-gsub("European Union - 27 countries \\(from 2020\\)","EU27",data$geo)

```
Now the data are prepared, it's time to start working on the visualisation, details of which I will post shortly.
    
      
<a name="myfootnote">*</a>_The UK is no longer formally required to provide data to Eurostat, following the end of the [Brexit transition period](https://ec.europa.eu/eurostat/web/products-eurostat-news/-/WDN-20200201-1) at the end of 2020.
