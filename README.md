# Storytelling with `data viz` ![Logo](https://github.com/michelleg06/DataViz/blob/main/images/Storytelling.png)

## Introduction
This repository hosts graphs and other forms of data visualisation that I have found useful throughout my research. The compendium is not exhaustive, and will be evolving as I discover new ways to express data graphically - or I manage to write a visual story. Most of what I visualise is done with `ggplot2`in the `R` language. Some visualisations will come from `Python` 🐍, and some additional edits might be done outside of a programming language. I will indicate this when or if it happens. 

-----

## Maps

A fun way to visualise the incidence of an event within or across geographies is through cloropleths. The basic concept of a cloropleth is the representation of the variability of an event using shades of colour (think shades of red, or increasing intensity in a colour palette). To visualise maps you need three elements: a shapefile, which contains coordinates (lat,long) of a region and its borders, data at the border level, e.g. aggregate data at the municipality or at the state level, and a unique identifier for the two datasets so that you can merge and match. 
