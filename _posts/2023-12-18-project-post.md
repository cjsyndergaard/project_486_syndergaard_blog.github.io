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

![Genre Distribution]({{site.url}}/{{site.baseurl}}/assets/images/figures/cm_other/genre_distribution.png)

To examine preliminary seperability, a simple random forest model was fit, attempting to determine how separable these genres were. Though it was entirely possible to somewhat separate the genres (with an out of sample accuracy rate of just under 50%, much better than random guessing with this many categories), it was still very difficult to classify them. The correct genre is the most likely predicted class, however the interconnections between the genres stops significant improvement. This is unsurprising. After all, as discussed above genres are messy, and what constitutes one genre from another is often the intent of the composer alone, not any specific musical quality. Upon this realization, my interest in the project shifted from simply classifying possible genre to describing how larger collections of genres interact with each other.

![Initial Classification for all the Genres]({{site.url}}/{{site.baseurl}}/assets/images/figures/cm_other/init_rf.png)

## Super Genre vs Clustering.
I decided to approach this feature engineering in two ways - the first a guided approach, using copious research into musical genre, and the second using unsupervised learning to find "naturally" clustered overarching genres. I would then compare each model on the two different output possibilities, and see if the increase in accuracy that I would undoubtedly gain would be worth the loss of interpretibility from the super genres.

I went through each of the genres present in the data, and assigned them to 8 super-genres. These super genres were 
* "entertainment", a genre that contained music connected to plays and movies
* "electronic", music that one would find associated with clubs and parties, characterized by electronic synthesis
* "rock", music that is associated with rock-n-roll, heavy metal, and similar music
* "pop", popular music throughout the years, often with very upbeat messages
* "folk", music that is characterized by its acoustic nature, such as country, folk, and jazz
* "latin", music that is associated with Latin America, such as reggaeton and samba
* "foreign", music that simply had its country of origin listed as its genre, and includes traditional music from around the world
* "easy_listening", music that is made to be calming or inspirational, such as classical and gospel music 

These genres were not perfectly uniformly distributed, however the class sizes are of relative scale.

![Super Genre Distribution]({{site.url}}/{{site.baseurl}}/assets/images/figures/cm_other/super_distribution.png)


Even these overarching, intelligently chosen, groups would undoubtedly have overlap, and may not be the best way to divide the songs. I also then used a cluster analysis to see how separable naturally occuring super genres are, which we could then describe. This also served to check for any serious anomolous tracks. I checked several values 
