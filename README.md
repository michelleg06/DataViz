# Storytelling with `data viz` ![Logo](https://github.com/michelleg06/DataViz/blob/main/images/Storytelling.png)

## Introduction
This repository hosts graphs and other forms of data visualisation that I have found useful throughout my research. The compendium is not exhaustive, and will be evolving as I discover new ways to express data graphically - or I manage to write a visual story. Most of what I visualise is done with `ggplot2`in the `R` language. Some visualisations will come from `Python` 🐍, and some additional edits might be done outside of a programming language. I will indicate this when or if it happens. 

-----

## Maps

A fun way to visualise the incidence of an event within or across geographies is through cloropleths. The basic concept of a cloropleth is the representation of the variability of an event using shades of colour (think shades of red, or increasing intensity in a colour palette). To visualise maps you need three elements: a shapefile, which contains coordinates (lat,long) of a region and its borders, data at the border level, e.g. aggregate data at the municipality or at the state level, and a unique identifier for the two datasets so that you can merge and match. Below I have a map of Mexico that shows how much property tax is collected by each municipality. To draw this map, I used the following `R` packages: 

* {[ggplot2](https://ggplot2.tidyverse.org/)}: a system for declaratively creating graphics, based on [The Grammar of Graphics](https://www.amazon.com/Grammar-Graphics-Statistics-Computing/dp/0387245448/ref=as_li_ss_tl).
* {[sf](https://r-spatial.github.io/sf/)}: a package that provides simple features access for R. It's arguably the go-to package to read geospatial data in `R`.
* {[scales](https://cran.r-project.org/web/packages/scales/index.html)}: a package that scales map data to aesthetics, and provides methods for automatically determining breaks and labels for axes and legends.

  **The end result:**

![MexicoMap](https://github.com/michelleg06/DataViz/blob/main/images/cloropleths.png)

**The `R`code snippet**
```r
p <- merged_sf_data %>%
        filter(year == 2010) %>%
            ggplot() +
            geom_sf(aes(fill = `Property Tax in USD ppp`)) + # Color fill based on property tax in USD (ppp)
            scale_fill_distiller(palette = "Spectral", trans = "reverse") +     
            theme_void() +
            scale_fill_gradientn(colors = col_palette, labels = scales::label_comma()) + 
            ggtitle("Property Tax in Mexican Municipalities: 2010")

p_series <- merged_sf_data %>%
                    filter(year >= 2010) %>%
                    ggplot() +
                    geom_sf(aes(fill = `Property Tax in USD ppp`)) +
                    facet_wrap(~ year, nrow = 3) + 
                    theme_void() +
                    scale_fill_gradientn(colors = col_palette, labels = scales::label_comma()) +
                    ggtitle("Property Tax in Mexican Municipalities: 2010:2021")
                                 
```
*Note: The snippet above does not include the data wrangling part*
  
