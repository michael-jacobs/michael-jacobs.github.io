---
title: "European Economic Indicators - tracking the impact of COVID19"

tags:
  - Shiny
  - Dashboard
  - Economic Data
  - Visualisation
---

To illustrate both the current and future impact of the COVID19 pandemic on Europe’s economy I have created an online dashboard to retrieve and analyse the latest available 
economic data for the region. The application, which uses the R library Shiny, downloads the latest available data on a weekly basis and includes a series of interactive charts and graphics - follow the link below to starting playing with the different comparisons:  
  
[https://mjacobsdata.shinyapps.io/europe-economy-covid/](https://mjacobsdata.shinyapps.io/europe-economy-covid/)

Using data accessed from the [Eurostat API](https://michael-jacobs.github.io/europe-economy-tracker-part1/), the dashboard focuses on three key economic indicators:

   **1. Unemployment rates**, including both total unemployment rates, as well as data disaggregated by age and gender.  
   **2. Inflation rates** based on the [Harmonised Index of Consumper prices](https://ec.europa.eu/eurostat/web/hicp), as well as the related expenditure weights.  
   **3. Gross Domestic Product (GDP)** a widely used measure of output and economic activity.       
  
The dashboard aims at visualising the changes in these three main areas since the start of 2020, but also to contextualise the changes with reference to past events (in particular with reference to the last major economic downturn, which began across Europe in 2008). While it remains too early to draw definitive conclusions about the overall impact of the COVID19 pandemic, the screen shots provided below give an idea of some of the main the findings from the visualisations.

***Unemployment has increased following the pandemic, but levels generally remain below the those seen during the last recession***  

![Unemployment rate over time](/assets/images/mjacobsdata_unemployment1.PNG "Unemployment rate)

***The increase in unemployment since early 2020 has affected different groups in different ways, with under 25 year olds being particularly affected. Across the EU27 
unemployment rates went up most for under 25 year old females***

![Unemployment rate disaggregated](/assets/images/mjacobsdata_unemployment2.PNG)

***Household expenditure patterns have changed as lockdowns and restrictions on movement have meant the relative importance of spending on transport, restaurants, hotels and other recreation 
and cultural activities has declined***   

![Expenditure weights](/assets/images/mjacobsdata_expenditure.PNG)

***The drop in economic output that started in the first quarter of 2020 was more abrupt than the decline that begin in 2008, but the bounce back has been quicker. Nonetheless, for now, output 
remains below the levels seen in 2019***

![GDP index](/assets/images/mjacobsdata_gdp.PNG)
