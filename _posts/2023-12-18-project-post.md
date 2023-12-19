---
layout: post
title:  "Spotify ML Project"
author: Colton Syndergaard
description: The required blog post for Tier 1 engagement for STAT 486
image: "/assets/images/spotify.jpg"
---

## Problem Description and Introduction
Music is one of the most important forms of art that humanity has. Music, by its nature, is messy, interconnected, and difficult to label. However classifying music into genres, categorizing it by its nature allows listeners to experience a wider variety of music that still is likely to be to their tastes. Spotify, a music streaming platform, has done exactly this, assigning each song a label, a genre. They have also found ways to numerically describe each song, rating attributes such as the danceability and acousticness on some proprietary scale.

This allowed us to explore how separable the genres themselves are; of more interest, it turned out, is there some overarching way of classifying these songs, some super-genres that we can quantify and describe? Could we predict what these super genres are based on the connections we can draw based on domain knowledge of the music, or would a less formal, more natural grouping be better? What characteristics would these natural groupings have?

## Data
The data contain tracks collected from Spotify, with their associated song metrics. In the original dataset there are 114,000 songs. However, many of these tracks are recorded multiple times under multiple genres (music really is messy!), with only 89,741 of these songs being unique. To simplify the already complex process only the first genre/recording of each song was kept. The numeric variables were standardized. 

The genres were not evenly distributed throughout the dataset, following the distribution below:

`![Genre Distribution](/assets/images/figures/cm_other/genre_distribution.png)`

To examine preliminary seperability, a simple random forest model was fit, attempting to determine how separable these genres were. 



