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

To draw theseplots I only used one package:
* {[ggplot2](https://ggplot2.tidyverse.org/)}: a system for declaratively creating graphics, based on [The Grammar of Graphics](https://www.amazon.com/Grammar-Graphics-Statistics-Computing/dp/0387245448/ref=as_li_ss_tl).

- **Overlaying kernel density distributions**

<img src="https://github.com/michelleg06/DataViz/blob/main/images/kdens_plot.png" width="350" height="300">

The density plot shows the distribution of a numeric continuous vector, here called utilities. You can plot this vector for subgroups of your data. Here, I wanted to compare the distributions between experimental participants (treated and control) in a sample. Whilst the analysis showed a trivial mean difference between these two subgroups, visualising the overlaid density plots clearly shows that there's a group that exihibits higher utility. These words, with little to no context, can be meaningless. But you can think of these distributions as scores: with a large overlap, there is a group of people with a higher score _en masse_. Fun, quick finding eh?



To replicate this type of plot you can use the following code snippet:

```r
kdensplot <- ggplot(baseline, aes(x=baseline_utility, fill=treatment)) + # declare which dataset you're using, x = continuous variable to plot, fill = group by which to split
                geom_density(alpha=0.80, color="grey90") + theme_classic() + # alpha = opacity of the fill colours, color = color of the density line (border)
                xlab("Population baseline utilities") + 
                ylab("Density") + 
                ggtitle("Kernel density of utilities") +
                theme(plot.title = element_text(color="grey22", size=14, face="bold", hjust = 0.5)) + # play around with this line to change the style of the text in the title
                scale_fill_manual("Treatment Status", labels=c("Control","Treatment"), values=c("darkgrey","grey90")) # fill in the density with your own colours, I chose shades of gray
    
```
- **Same-grid histograms**
  
<img src="https://github.com/michelleg06/DataViz/blob/main/images/hist_facetwrap.png" width="400" height="300">

When you simply want to observe the shape of the distribution by subgroups, but are not worried about overlaps, simply plotting two histograms next to each other will do. This is probably useful when the subgroups of your sample you want to look at are clearly distinct. Also, I was clearly enjoying the basic grey aesthetic for these plots 😉. 

P.S. In the snippet below you'll recognise the facet_wrap argument, which we used for our maps as well! 

```r
utilities_academics <- ggplot(baseline, aes(x=baseline_utility)) + # declare the dataset, x = variable to plot
                                geom_histogram(col="gray", fill=NA, aes(y = stat(density))) + theme_classic() + # borders coloured grey, y scale density of variable, theme classic (no grid lines)
                                xlab("Population baseline utilities") + # add a label on the x axis
                                ggtitle("Distribution of utilities") + # add a title to my plot
                                facet_wrap(~baseline$academics) # now use the above elemnts to plot two contiguous histograms, based on the two-category variable 'academics'

```
## Scatterplots

A scatterplot is the standard way to visualise the relationship between two features. I'm going to have a little fun using the [Gapminder](https://www.gapminder.org/) data. Gapminder is the brain-child of the late Hans Rosling, who was keen on visualising trends over time, and showing us that the world is not so terrible through global trend analysis. 







