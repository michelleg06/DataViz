# Storytelling with `data-viz` ![Logo](https://github.com/michelleg06/DataViz/blob/main/images/logo-dv2.png)

## Introduction
This repository hosts graphs and other forms of data visualisation that I have found useful for my research. The compendium is not exhaustive, and will be evolving as I discover new ways to express data graphically - or I endeavour to write a visual story for policy audiences. Most of what I visualise is done with the 🇷 package `ggplot2`. Some visualisations will be made in the `Python` 🐍 language, and some additional edits might be done outside of a programming language (I have been known to use the Adobe suite and Canva from time to time). I will indicate this when or if it happens. 

-----
## OECD Gender and Development Data
I'm looking forward to exploring meaningful datasets as they are released. The Gender team of the OECD Development Centre recently released the latest version of their Social Institutions and Gender Index, along with the disaggregated data. Below is a simple yet powerful bar graph à la OECD style. The SIGI is an index that comprises four dimensions: i) discrimination in the family, ii) restricted physical integrity, iii) restricted access to productive and financial resources, and iv) restricted civil liberties. In turn, each dimension is comprised of 4 indicators, and each indicator is built on a set of variables that describe it best. For the graph below, I decided to look at the Restricted Physical Integrity Dimension, and delve deeper into the Violence Against Women indicator (1 of the 4 that compose the dimension). The dataset includes a series of binary vectors that explore all the situaitons in which the law protects women who experience physical abuse. For example, one of the binary vectors corresponds to the question "Are there specific provisions in the law for investigation, prosecution and punishment of the perpetrator?". In total, the indicator asks 23 questions that cover an important range of legal protections for female victims of violence. I computed a 'violence against women' weighted score per country, based on these questions. Details regarding this computation are available upon request. Finally, the graph shows a subset of countries for which a score exists (the choice of countries was random, as this exercise is just to illustrate the power of dataviz). It tells us how well our country's laws protect us (women) if we are victims of violence. Note that because we are simply looking at the laws, this does not include social norms or the quality of rule of law, which may come in contrast with what we observe here. Nonetheless, for the safety of women, a thorough legal framework is the first step.

As always, the `R`packages used were: 

* {[ggplot2](https://ggplot2.tidyverse.org/)}: a system for declaratively creating graphics, based on [The Grammar of Graphics](https://www.amazon.com/Grammar-Graphics-Statistics-Computing/dp/0387245448/ref=as_li_ss_tl).
  
* {[cowplot](https://cran.r-project.org/web/packages/cowplot/vignettes/introduction.html)}: a simple add-on to ggplot. It provides various features that help with creating publication-quality figures, such as a set of themes, functions to align plots and arrange them into complex compound figures, and functions that make it easy to annotate plots and or mix plots with images.

**The end result:**

![OECDBarGraph](https://github.com/michelleg06/DataViz/blob/main/images/bar_plot_with_flags.png)

**Some cloropleths**

The visualisation below is composed of three cloropleths: one per year 1970 - 2000 - 2023. It lets us see the evolution of gender equality in access to employment across the globe. You'll notice that the US and the UK are missing. Polygon data for those countries was not available. Fear not, it is easy to find, and I'll do so in the coming weeks. The cloropleths were done in R. However, the logos and titles were done with Canva Pro. For the maps, the `R`packages include:

* {[ggplot2](https://ggplot2.tidyverse.org/)}: a system for declaratively creating graphics, based on [The Grammar of Graphics](https://www.amazon.com/Grammar-Graphics-Statistics-Computing/dp/0387245448/ref=as_li_ss_tl).
* {[gridExtra](https://cran.r-project.org/web/packages/gridExtra/index.html)}: a package that provides a number of user-level functions to work with "grid" graphics, notably to arrange multiple grid-based plots on a page, and draw tables.
* {[sf](https://r-spatial.github.io/sf/)}: A package that provides [simple features access](https://en.wikipedia.org/wiki/Simple_Features) for R.
* {[RColorBrewer:](https://cran.r-project.org/web/packages/RColorBrewer/index.html)}: A package that provides color schemes for maps (and other graphics).
* {[maps](https://cran.r-project.org/web/packages/RColorBrewer/index.html)}: display of maps. Projection code and larger maps are in separate packages ('mapproj' and 'mapdata').

![OECDcloropleths](https://github.com/michelleg06/DataViz/blob/main/images/Women%20can%20take%20the%20same%20jobs%20as%20men.png)

**Code Snippets**
I'll update the code to replicate these visuals in due time. These plots required a bit more work than the graphs below, and I need to parse code lines and generally make the scripts as easy to read as everything below. I promise to get to it! 😀

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
*Note 1: The snippet above does not include the data wrangling to get the final dataset, feel free to reach out to me if you want to learn how to do that.*

*Note 2: To put both images in one .png file I used Canva. Alternatively, you can use the [`grid.arrange()`](https://cran.r-project.org/web/packages/egg/vignettes/Ecosystem.html) function from the `gridExtra`package to do this inside of Rstudio.*


## Distributions

Visualising the distribution of a variable can give us pretty good insights on the statistical properties of the vector and practical implications on what we're trying to offer. Below I'll show a few visualisations that I enjoy: split histograms, and overlapping density distributions. 

To draw these plots I only used one package:
* {[ggplot2](https://ggplot2.tidyverse.org/)}: a system for declaratively creating graphics, based on [The Grammar of Graphics](https://www.amazon.com/Grammar-Graphics-Statistics-Computing/dp/0387245448/ref=as_li_ss_tl).

**Overlaying kernel density distributions**

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
**Same-grid histograms**
  
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

For the animated scatterplot below, I've used the following packages:

* {[ggplot2](https://ggplot2.tidyverse.org/)}: a system for declaratively creating graphics, based on [The Grammar of Graphics](https://www.amazon.com/Grammar-Graphics-Statistics-Computing/dp/0387245448/ref=as_li_ss_tl).
* {[scales](https://cran.r-project.org/web/packages/scales/index.html)}: a package that scales map data to aesthetics, and provides methods for automatically determining breaks and labels for axes and legends.
* {[gganimate](https://gganimate.com/)}: a package that extends the grammar of graphics as implemented by ggplot2 to include the description of animation. It does this by providing a range of new grammar classes that can be added to the plot object in order to customise how it should change with time.
* {[gapminder](https://cran.r-project.org/web/packages/gapminder/index.html)}: a package that provides an excerpt of the data available at Gapminder.org. For each of 142 countries, the package provides values for life expectancy, GDP per capita, and population, every five years, from 1952 to 2007.

**The animated scatterplot (.gif):**

![life_exp_gdp](https://github.com/michelleg06/DataViz/assets/17123522/6fa849c4-371f-4de9-9f85-52d6aa816c20)

**To `R` code:**

```r
p <- ggplot(gapminder, aes(x = gdpPercap, y = lifeExp, color=continent)) +
            geom_point(alpha=0.5) + theme_minimal() + 
            xlab("GDP per capita (logged)") +
            ylab("Life Expectancy of Population") +
            scale_x_log10(labels = scales::dollar) + 
            facet_wrap(~continent) +
            labs(title = 'Year: {frame_time}', x = 'GDP per capita', y = 'life expectancy') +
            transition_time(year) + 
            ease_aes('linear') + 
            guides(color = FALSE)

anim_save("life_exp_gdp.gif")
```


## Customised Visuals

`ggplot2`and its seemingly infinte add-ons allows you to programatically customise your data visuals as much as you want. The simple bar graph below is an example of combining your creativity with the `magick` and `sysfonts`packages. This is meant to be a fun bar chart for children who, like me, enjoy learning about penguins 🐧!

As always, the packages involved in this design include:

* {[ggplot2](https://ggplot2.tidyverse.org/)}: a system for declaratively creating graphics, based on [The Grammar of Graphics](https://www.amazon.com/Grammar-Graphics-Statistics-Computing/dp/0387245448/ref=as_li_ss_tl).
* {[magick](https://docs.ropensci.org/magick/articles/intro.html)}: a package that provides a modern and simple toolkit for image processing in R. It wraps the ImageMagick STL which is the most comprehensive open-source image processing library available today.
* {[sysfonts](https://cran.r-project.org/web/packages/sysfonts/index.html)}: Loading system fonts and Google Fonts <https://fonts.google.com/> into R, in order to support other packages such as 'R2SWF' and 'showtext'.
* {[palmerpenguins](https://allisonhorst.github.io/palmerpenguins/articles/intro.html)}: A package that contains two datasets for  statistics and data science education, as well as software documentation and testing.

*Note: I obtained the image and the colour scheme from the `palmerpenguins` vignette.*

**The result:**

![PenguinBarChart](https://github.com/michelleg06/DataViz/blob/main/images/pengbar.png)

**The `R` code snippet:**
```r
# Upload image to Rstudio
library(magick) # add images
penguins_img <- image_read("https://allisonhorst.github.io/palmerpenguins/reference/figures/lter_penguins.png")

# Add a custom font for fun
library(sysfonts) # add more fonts to the system
library(showtext) # to work with different fonts

font_add_google("Schoolbell")
showtext_auto() # turn on/off the automatic use of showtext functionality

# Heavily customise your bar graph!

img_order <- c("Chinstrap", "Gentoo", "Adelie" )

pengbar <- penguins %>%
    dplyr::filter(!is.na(sex)) %>% # filter for non-missing values in random (in this case sex) variable
    dplyr::count(species) %>% # count the number of penguins of each species 
    mutate(species = factor(species, levels = img_order)) %>% # Specify preferred order # replace the order of the species to a preferred order, specified above
    ggplot(aes(x = species, y = n, fill = species)) + # x is factor var, y is the n count variable returned by the dplyr count function
    geom_col(width = 0.7, color = "black") + # define the width of the bars and the border colour
    scale_fill_manual(values = c("darkorchid2", "aquamarine4", "darkorange"), # change the fol
                      guide = guide_legend(override.aes = list(shape = NA))) + # Remove default legend symbols
    labs(x = "Species", y = "Number of penguins", fill = "Species:") # specify labels

pengbar <- pengbar +
    scale_y_continuous(expand = c(0, 0), limits = c(0, 165)) +
    theme_classic(base_size = 15, base_family = "Schoolbell") +
    theme(
        panel.background = element_rect(fill = "white", color = "grey30"),
        panel.border = element_rect(fill = NA, color = "white"),
        legend.background = element_rect(fill = "white", color = "grey93"),
        plot.background = element_rect(fill = "white", color = NA),
        axis.text.x = element_text(face = "italic"),
        axis.text.y = element_text(face = rep(c("bold", "plain"), 10), angle = 30),
        axis.title = element_text(face = "bold", family = "Schoolbell"),
        legend.title = element_text(face = "bold", family = "Schoolbell")
    )

# define image placement

img_x <- with(penguins, as.numeric(factor(species))) # x axis 
img_y <- rep(10, n_distinct(penguins$species)) # y axis

pengbar <- pengbar + 
    annotation_custom(
        rasterGrob(image = penguins_img, interpolate = TRUE),
        xmin = min(img_x) - 0.05, xmax = max(img_x) + .05,
        ymin = -99, ymax = 150
    )

print(pengbar)
```

