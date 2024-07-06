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

**Code snippet**

```r

# Choose a color palette and assign an object to it. Highlight the OECD average bar.
color_palette <- c("OECD" = "cornflowerblue", "Mexico" = "grey", "France" = "grey", "Egypt" = "grey","Ecuador" = "grey", "Denmark" = "grey", "Brazil" = "grey", "Belgium" = "grey", "Argentina" = "grey")

# reorder the dataset in a descending order based on the violence_score variable, i.e. the computed score for quality of law for the protection of women victims of physical violence
reordered_df <- df_country[order(-df_country$violence_score), ]

# add a vector containing the URL for flag images
reordered_df$url_flag <- c("https://raw.githubusercontent.com/michelleg06/DataViz/main/images/Brazil_flag.png","https://raw.githubusercontent.com/michelleg06/DataViz/main/images/Flag_of_France.png","https://raw.githubusercontent.com/michelleg06/DataViz/main/images/Flag_of_Mexico.png","https://raw.githubusercontent.com/michelleg06/DataViz/main/images/flag_argentina.png", "https://raw.githubusercontent.com/michelleg06/DataViz/main/images/Flag_of_Belgium.png","https://raw.githubusercontent.com/michelleg06/DataViz/main/images/OECD_logo.png", "https://raw.githubusercontent.com/michelleg06/DataViz/main/images/Flag_of_Ecuador.png", "https://raw.githubusercontent.com/michelleg06/DataViz/main/images/Flag_of_Denmark.png", "https://raw.githubusercontent.com/michelleg06/DataViz/main/images/Flag_of_Egypt.png")

# create base graph bar

bar_2 <- ggplot(reordered_df, aes(x = reorder(Country, -violence_score), y = violence_score, fill = Country)) +
    geom_bar(stat = "identity", width=0.5, position = position_dodge(width = 0.3)) +
    coord_flip() + 
    geom_text(aes(label = paste0(round(violence_score, 1), "%")), hjust = 1.2, colour = "white", fontface = "bold") +
    labs(x = "", y = bquote(bold("Score"))) +
    scale_fill_manual(values = color_palette) +  # Use color palette defined above
    theme_classic() +
    theme(
        #axis.title.x = element_blank(),  # Hide x-axis title
        axis.title.y = ggtext::element_markdown(),  # to edit titles using markdown 
        plot.title = element_blank(),    
        panel.grid.major.y = element_blank(),  
        panel.grid.minor.y = element_blank(),   
        legend.position = "none"         
    ) +
    ylim(0, max(df_country$violence_score) + 10)  # add some extra space at the top

# function to transform png into grob class
read_flag_grob_url <- function(flag_url) {
    img <- image_read(flag_url)
    grob <- rasterGrob(image = img, interpolate = TRUE)
    return(grob)
}

# for loop: iterate over the rows of dataset (or iterate over each country)
for (i in 1:nrow(reordered_df)) {
    bar_2 <- bar_2 + annotation_custom(
        grob = read_flag_grob_url(reordered_df$url_flag[i]),
        xmin = i - 0.25, xmax = i + 0.25,  # Adjust x-position and width of the flag
        ymin = reordered_df$violence_score[i] + 1, ymax = reordered_df$violence_score[i] + 5  # Adjust y-position and height of the flag
    ) # paste the in each bar the grob class which corresponds to the order in the dataset, uses the grob function above
}

# read OECD logo as png
logo <- image_read("~/Desktop/OECD.png")

# Create a title with the logo using cowplot
title_with_logo <- ggdraw() +
    draw_image(logo, x = 0.01, y = 0.1, width = 0.1) +  # Adjust x, y, and width as needed
    draw_label("Are women protected from physical violence by the law?\n", x = 0.12, y = 0.55, 
               hjust = 0, vjust = 0.5, fontface = "bold", size = 18) + 
    draw_label(label = "Score in %, based on the 2023 Social Institutions and Gender Index (SIGI).The score rates the countries's laws with regards to the protection\nof women who experience        physical abuse.", 
               size = 10, x = 0.12, y = 0.38, hjust = 0, vjust = 0.5)

# Combine the title with the bar plot using cowplot's plot_grid()
final_plot <- plot_grid(
    title_with_logo,
    bar_2,
    ncol = 1,
    rel_heights = c(0.2, 0.5)  # Adjust the relative heights of the title and the bar plot
)

ggsave("bar_plot_with_flags.png", plot = final_plot, width = 10, height = 8, units = "in", dpi = 300, bg = "white")
```

**Some cloropleths**

The visualisation below is composed of three cloropleths: one per year 1970 - 2000 - 2023. It lets us see the evolution of gender equality in access to employment across the globe. You'll notice that the US and the UK are missing. Polygon data for those countries was not available. Fear not, it is easy to find, and I'll do so in the coming weeks. The cloropleths were done in R. However, the logos and titles were done with Canva Pro. For the maps, the `R`packages include:

* {[ggplot2](https://ggplot2.tidyverse.org/)}: a system for declaratively creating graphics, based on [The Grammar of Graphics](https://www.amazon.com/Grammar-Graphics-Statistics-Computing/dp/0387245448/ref=as_li_ss_tl).
* {[gridExtra](https://cran.r-project.org/web/packages/gridExtra/index.html)}: a package that provides a number of user-level functions to work with "grid" graphics, notably to arrange multiple grid-based plots on a page, and draw tables.
* {[sf](https://r-spatial.github.io/sf/)}: A package that provides [simple features access](https://en.wikipedia.org/wiki/Simple_Features) for R.
* {[RColorBrewer:](https://cran.r-project.org/web/packages/RColorBrewer/index.html)}: A package that provides color schemes for maps (and other graphics).
* {[maps](https://cran.r-project.org/web/packages/RColorBrewer/index.html)}: display of maps. Projection code and larger maps are in separate packages ('mapproj' and 'mapdata').

![OECDcloropleths](https://github.com/michelleg06/DataViz/blob/main/images/Women%20can%20take%20the%20same%20jobs%20as%20men2.png)

**Code Snippet:**

```r
# get the shape
world <- map_data('world') # from maps library

# we need to transform it into an sf object first
world_sf <- world %>%
    st_as_sf(coords = c("long", "lat"), crs = 4326) %>% #(CRS) number 4326 corresponds to the WGS 84 
    group_by(group, region) %>% # groups by group (mandatory) but also keeps region (needed to merge to other world/country files)
    summarize(geometry = st_combine(geometry)) %>%
    st_cast("POLYGON")

# CRS - coordinate ref system
st_crs(world_sf) #CRS assigned!

# merge data subset for the year 1970 with the lat and long sf dataset
merged_df_70 <- map70 %>% left_join(world_sf, by ="region")
merged_sf_data70 <- st_as_sf(merged_df_70)

# make sure our variable of interest is in factor class
merged_sf_data70 <- merged_sf_data70 %>%
    mutate(same_job_label = factor(same_job, levels = c(0, 1), labels = c("No", "Yes")))

# use ggplot to draw the map with geom_sf() :)
map_sf_70 <- merged_sf_data70 %>%
                    ggplot() +
                    geom_sf(aes(fill = same_job_label)) + # Color fill based on binary factor women can take the same job as men
                    scale_fill_manual(values = c("No" = "#FFE5B4", "Yes" = "cornflowerblue"), 
                      name = "Gender Equality in Jobs") + 
                    theme_minimal() + 
                    labs(title = "Women can take the same jobs as men 1970")+
                    theme(
                        axis.text = element_blank(),# remove latitude/longitude text
                        axis.ticks = element_blank(),# remove axis ticks
                        panel.grid = element_blank(),# remove grid lines
                        plot.title = element_text(size = 15, face = "bold"),
                        plot.background = element_rect(fill = "white"),  # set plot background to white
                        legend.key = element_rect(fill = "white")  # set legend background to white
                    )

# repeat the process above for subsets of the data that correspond to the year 2000 and 2023.
# Once the three maps have been plotted, place them in the same grid:

library(gridExtra)
grid.arrange(map_sf_70,map_sf_2000,map_sf_23)

```

**Easter Egg**

Although gganimate is the standard for dynamic ggplots, there's loads of other alternatives. One of them is the camcorder pkg. The gif below shows the evolution of the cloropleths in gif format. P.S. I also recommend D3 and Svelte for dynamic plots.

![maps_oecd_gif](https://github.com/michelleg06/DataViz/blob/main/images/maps_oecd.gif)

The package:

* {[camcorder](https://github.com/thebioengineer/camcorder)}: an R package to track and automatically save graphics generated with {ggplot2} that are created across one or multiple sessions with the eventual goal of creating a GIF showing all the plots saved sequentially during the design process.

**Code Snippet**

```r
library(camcorder)
paths <- "~/Desktop/"
gg_record(
    dir = file.path(paths, "record_maps"), # where to save the recording
    device = "png", # device to use to save images
    width = 16,      # width of saved image
    height = 24,     # height of saved image
    units = "in",   # units for width and height
    dpi = 600       # dpi to use when saving image
)

map_sf_70 
map_sf_2000 
map_sf_23 
# ran all the ggplots above

gg_playback(
    name = file.path(paths, "record_maps", "maps_oecd.gif"),
    first_image_duration = 5,
    last_image_duration = 5,
    frame_duration = .4,
    image_resize = 1000
)
```

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

