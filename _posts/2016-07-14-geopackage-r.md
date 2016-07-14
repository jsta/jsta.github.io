---
title: "Using the Geopackage Format with R"
output: html_document
layout: post
---

Many (most?) people involved in vector geospatial data analysis work exlusively with shapefiles. However, the [shapefile format](https://en.wikipedia.org/wiki/Shapefile) has a number of drawbacks including the fact that spatial attributes, metadata, and projection information are stored in seperate files. For instance, it can take as much as [45 lines of code](https://github.com/jhollist/miscPackage/blob/master/R/download_shp.R) to ensure a complete "shapefile" download.

In a post to the [R-Sig-Geo listserv](https://stat.ethz.ch/pipermail/r-sig-geo/) (excerpted below) Barry Rowlingson mentions the [GeoPackage format](https://en.wikipedia.org/wiki/GeoPackage) which is an interesting alternative to shapefiles. 


```
Barry Rowlingson b.rowlingson at lancaster.ac.uk
Wed Jul 13 19:44:28 CEST 2016

[...] the agency from which I got the data has been enlightened
enough not to use the clunky, outdated, and limited "shapefile" format
and has released the data as a modern, OGC-standard GeoPackage. My
variables have long names, my metadata is stored with my data, and its
all in one file instead of six. [...]

 Shapefiles are an awful, awful format which Esri didn't think people
would actually use. They should not be encouraged. [...] 

Barry
```

The interested but time-limited geospatial analyst might wonder; Can I easily switch from a shapefile workflow to a GeoPackage workflow? Will my colleagues be able to open and interact with GeoPackage files? Fortunately, the answer is yes because the `rgdal` `R` package [supports reading and writing of vector geospatial data to GeoPackage files](https://gist.github.com/mdsumner/cc9b29692c450fd182cfbf46b85c5365). 

First, lets load some geospatial data into a `SpatialPointsDataFrame` object.

```r
library(rgdal)
cities <- readOGR(system.file("vectors", package = "rgdal")[1], "cities")
class(cities)
```

> [1] "SpatialPointsDataFrame"    
> attr(,"package")    
> [1] "sp"

Now lets write the data to disk using the GeoPackage format:

```r
# outname <- file.path(tempdir(), "cities.gpkg")
outname <- "cities.gpkg"
writeOGR(cities, dsn = outname, layer = "cities", driver = "GPKG")
```

This file can then be read back into R:

```r
cities_gpkg <- readOGR("cities.gpkg", "cities")
identical(cities, cities_gpkg)
```
> [1] TRUE

The file can also be opened in your standalone GIS program of choice such as GRASS, QGIS, or even ArcGIS.