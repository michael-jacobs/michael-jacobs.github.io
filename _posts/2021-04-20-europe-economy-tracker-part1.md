---
title: "European Economic Indicators - accessing data from the Eurostat API"

tags:
  - API
  - Shiny
  - Data Visualisation
  - R
---

This is the first of (hopefully!) many blog posts, where I explore data freely available online, with the aim of creating a thought-provoking data visualisation.  

In this instalment I am working with Economic data that can be accessed directly from Eurostat website via its [API](https://ec.europa.eu/eurostat/online-help/public/en/API_06_DataQuery_en/), with the intention of shedding some light on the economic impact of the COVID19 pandemic to date. As the Statistical office of the European Commission, Eurostat is responsible for gathering comparable data from European Union member states in a variety of statistical domains, meaning the API is a rich source of free data and information. In some cases data for non-EU countries are also available for comparison purposes, including Japan, the United Kingdom<sup>[*](#myfootnote)</sup> and the United States in the case of the economic indicators used in the following example.  

In this post I will explain briefly the process for accessing the data from the API (using R), before moving on to the Shiny application and conclusions from the resulting analysis in a follow up blog. Thankfully, extracting the data is relatively straight forward, thanks to the [Eurostat package](http://ropengov.github.io/eurostat/articles/website/eurostat_tutorial.html). Having initialised the package in R, a full list of contents of the API - including codes and related descriptions - can be downloaded, as detailed in the script below.  

The resulting table of contents **(toc)** details the catalogue of datasets available from the API - you can review and filter to identify the data sets of interest. 
Alternatively, the [RAMON database](https://ec.europa.eu/eurostat/ramon/nomenclatures/index.cfm?TargetUrl=LST_NOM&StrGroupCode=SCL&StrLanguageCode=EN) is available and  
searchable online, to assist with identifying the code(s) required for downloading of the data of interest.

```r
## Load required libraries##
library(eurostat)
library(dplyr)


## Download full list of available data##
toc <- get_eurostat_toc()

view(toc)

```

For the data I want to analyse - related to unemployment, gross domestic product (GDP) and consumer prices (and associated expenditure weights) - four seperare codes are needed, each referring to specific data sets. The R-code below extracts and saves the required data from the API and does some minor reformatting of the information to be stored in each of the resulting .csv files. 

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

**To note** – the dimensions included in the different datasets may differ, meaning some re-shaping of the data will be required if you intend to create one large data table combining each of the seperate data sources. Be sure also to consider the format of the date fields when creating your main data table to save headaches later on. Finally, some of the datasets can be large, so be sure to give sufficent thought to the precise data you need before downloading. The Eurostat package will enable you to limit the slice of data you pick up, for example by limiting the period covered by the - more details are availale [here: Eurostat package.](http://ropengov.github.io/eurostat/articles/website/eurostat_tutorial.html)  

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

<a name="myfootnote">*</a>: Data are no longer formally provided to Eurostat by the UK, following the end of the [Brexit transition period](https://ec.europa.eu/eurostat/web/products-eurostat-news/-/WDN-20200201-1). Further updating of information for the UK is subject to agreement on areas for future statistical cooperation. 
