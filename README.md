# Storytelling with `data viz` ![Logo](https://github.com/michelleg06/DataViz/blob/main/images/Storytelling.png)

## Introduction
This repository hosts graphs and other forms of data visualisation that I have found useful for my research. The compendium is not exhaustive, and will be evolving as I discover new ways to express data graphically - or I endeavour to write a visual story for policy audiences. Most of what I visualise is done with `R` package `ggplot2`. Some visualisations will come from `Python` 🐍, and some additional edits might be done outside of a programming language. I will indicate this when or if it happens. 

-----

## Maps

A fun way to visualise the incidence of an event within or across geographies is through cloropleths. The basic concept of a cloropleth is the representation of the variability of an event using shades of colour (think shades of red, or increasing intensity in a colour palette). To visualise maps you need three elements: a shapefile, which contains coordinates (lat,long) of a region delimiting its borders, data at the border level, e.g. aggregate data at the municipality or at the state level, and a unique identifier for the two datasets so that you can merge and match. Below I have a map of Mexico that shows how much property tax is collected by each municipality. To draw this map, I used the following `R` packages: 

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
*Note: The snippet above does not include the data wrangling part.*

## Distributions

Visualising the distribution of a variable can give us pretty good insights on the statistical properties of the vector and practical implications on what we're trying to offer. Below I'll show a few visualisations that I enjoy: split histograms, and overlapping density distributions. 

- **Overlaying kernel density distributions**

<img src="https://github.com/michelleg06/DataViz/blob/main/images/kdens_plot.png" width="350" height="300">

The density plot shows the distribution of a numeric continuous vector, here called utilities. You can plot this vector for subgroups of your data. Here, I wanted to compare the distributions between experimental participants (treated and control) in a sample. Whilst the analysis showed a trivial mean difference between these two subgroups, visualising the overlaid density plots clearly shows that there's a group that exihibits higher utility. These words, with little to no context, can be meaningless. But you can think of these distributions as scores: with a large overlap, there is a group of people with a higher score _en masse_. Fun, quick finding eh?






