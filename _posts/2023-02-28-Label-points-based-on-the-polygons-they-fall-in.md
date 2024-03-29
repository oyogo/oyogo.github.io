---
title: "Label points based on the polygon they fall in"
author: "Clinton" 
output:
  html_document:
    keep_md: true
category: R
tags:
 - r  
 - GIS  
 - spatial data analysis  
 - data munging  
---

## Problem
Let's say you have a dataset (dataframe) that has coordinates of locations where a certain species was observed take for example 
*Striped Kingfisher* species. The observations were done for a given Country take for example Kenya which has 47 Counties (second level adminstrative boundaries). You may want to know the total number of observations per County but then you only have locations with no labels of the respective County in which the species was observed. To achieve that you will of course need to have the dataframe have a colum with the County names for each of the point so that you do the aggregations per County. Now, here's a step by step approach on how to solve this problem using R software.  

### What you'll need
* R program installed on your laptop. 
* R Libraries for solving this task.
* Data frame with data and coordinates.
* Shapefile of Kenya with subdivisions of Counties. 

#### Step 1 
Assuming you have some basic knowledge in R and you have it installed on your pc, load the following libraries. 
_Remember to download the libraries first if you don't have them._  
```{r}
library(raster) # handling spatial data
library(sp) # handling spatial data
library(data.table) # for data munging
```

#### Step 2   
Import the dataframe and the shapefile into R session. 

```{r}
kenya.counties <- shapefile("data/shp/counties_pop.shp")
kingfisher.observations <- fread("data/kingfisher.csv")
```

#### Step 3

```{r}
# Set the longitude and latitude columns as coordinates. The kingfisher dataframe will now be a spatial polygons dataframe. 
coordinates(kingfisher.observations) <- ~longitudeDecimal + latitudeDecimal   

# Set the cordinate projection of the dataframe coordinate columns to that of the shapefile. 
proj4string(kingfisher.observations) <- proj4string(kenya.counties)  

# From the shapefile get the name of the column with the County names.
str(kenya.counties)  
# For this file he name is : COUNTY 

# The over function from sp library will overlay the dataframe coordinates on to the shapefile polygons and return the respective COUNTY name. 
overlay_df_cnames <- sp::over(kingfisher.observations,kenya.counties[,"COUNTY"])

# Add the extracted COUNTY names to the spatial polygons dataframe

kingfisher.observations@data$COUNTY <- overlay_df_cnames$COUNTY

# If you wish, convert the spatial polygons dataframe back to a dataframe 
kingfisher_df <- as.data.frame(kingfisher.observations)

# Let's have a look at the first 5 observations to see if all went well. 

head(kingfisher_df)

```
We have successfully labeled the coordinates with the County name. 
With that we can now perform an aggregation of the observations per County.

```{r}
totals_perCounty <- setDT(kingfisher_df)[,c(totals=sum(individualCount)),by=.(COUNTY)]
```

And there we are!! 

