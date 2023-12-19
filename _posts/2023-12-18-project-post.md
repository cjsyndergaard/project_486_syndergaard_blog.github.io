---
layout: post
title:  "Spotify ML Project"
author: Colton Syndergaard
description: The required blog post for Tier 1 engagement for STAT 486
image: "/assets/images/spotify.jpg"
---

## Problem Description and Introduction
Music is messy. It is one of the most important forms of art that humanity has. Music, by its nature, is very interconnected with culture and other music, and difficult to label. However classifying music into genres, categorizing it by some overarching quality allows listeners to experience a wider variety of music that still is likely to be to their tastes. Spotify, a music streaming platform, has done exactly this, assigning each song a label, a genre. They have also found ways to numerically describe each song, rating attributes such as the danceability and acousticness on some proprietary scale.

This allowed us to explore how separable the genres themselves are; of more interest, it turned out, is there some overarching way of classifying these songs, some super-genres that we can quantify and describe? Could we predict what these super genres are based on the connections we can draw based on domain knowledge of the music, or would a less formal, more natural grouping be better? What characteristics would these natural groupings have?

## Data
The data contain tracks collected from Spotify, with their associated song metrics. In the original dataset there are 114,000 songs. However, many of these tracks are recorded multiple times under multiple genres (music really is messy!), with only 89,741 of these songs being unique. To simplify the already complex process only the first genre/recording of each song was kept. The numeric variables were standardized. 

The genres were not evenly distributed throughout the dataset, following the distribution below:

![Genre Distribution]({{site.url}}/{{site.baseurl}}/assets/images/figures/cm_other/genre_distribution.png)

To examine preliminary seperability, a simple random forest model was fit, attempting to determine how separable these genres were. Though it was entirely possible to somewhat separate the genres (with an out-of-sample accuracy rate of just under 50%, much better than random guessing with this many categories), it was still very difficult to classify them. The correct genre is the most likely predicted class, however the interconnections between the genres stops significant improvement. This is unsurprising. After all, as discussed above genres are messy, and what constitutes one genre from another is often the intent of the composer alone, not any specific musical quality. Upon this realization, my interest in the project shifted from simply classifying possible genre to describing how larger collections of genres interact with each other.

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


Even these overarching, intelligently chosen, groups would undoubtedly have overlap, and may not be the best way to divide the songs. I also then used a cluster analysis to see how separable naturally occuring super genres are, which we could then describe. This also served to check for any serious anomolous tracks. I checked several numbers of clusters to see if 8 was a reasonable number of divisions (to be most comparable to my super-genres), and it seemed to be a reasonable number of clusters, being somewhat a minimum in the WCSS, and so I decided to move forward with it.

![WCSS Cluster Number]({{site.url}}/{{site.baseurl}}/assets/images/figures/cm_other/wcss.png)

There were no particularly anomalous tracks found by clustering, as at the granularity used the distribution of songs in the clusters was, while not even, not particularly alarming or unbalanced.

![Cluster Genre Distribution]({{site.url}}/{{site.baseurl}}/assets/images/figures/cm_other/cluster_distribution.png)

Both groupings proved interesting, with the following plots representing the centroids of the different groups (genres vs clusters). They can be read somewhat like parallel coordinate plots.

![Super Genre Centroids]({{site.url}}/{{site.baseurl}}/assets/images/figures/attributes/super_genre.png)
![Cluster Genre Centroids]({{site.url}}/{{site.baseurl}}/assets/images/figures/attributes/clusters.png)

It is interesting that the average value for each of these attributes do line up somewhat with what we would expect in the super genres, and very informative to see how the data is "best" separated into 8 groups with the cluster attributes. 

From the Super Genre Centroids, we can see that folk music is characterized by acoustic, relatively low energy songs. Electronic music is identified by it's high danceability (being made for dance floors) and absolute lack of acoustic instruments (unsurprisingly). Easy listening is characterized by relatively quiet, low energy, and high instrumental tracks. This goes on, lining up with what we might expect to see.

From the Cluster Genre Centroids we can see some interesting aspects as well, though there are no natural explanations to be had for why each cluster behaves how it does like with super genre. Several of the clusters are remarkably similar, being different only on a handful of axes, such as clustered genres 0 and 6, whereas some follow reminiscent behaviors, simply being exactly the opposite, likely lying along the other end of the same axis of the principal components, like clustered genres 5 and 6 being centered at a point that looks like -1 times the other.

## Modelling
Several types of models were used to explore the data using these competing labels. Naturally, it was expected that the natural clusters would provide easier to predict categories, however I wanted to see if the gain in accuracy for that grouping would be worth the relative lack of interpretability. Thus, the primary competing factor would be the labels, not the models themselves. This would be judged using the out-of-sample accuracy (as in-sample accuracy was always very high), and here I will report the best model type (being random forest in both cases).

The out-of-sample prediction accuracy for the super genre label was 62.4% - much better than when being predicted across all possible genres, however it was not phenomenal. In particular it appears that many genres were misclassified as electronic music (the confusion matrix below has the classes in the same order that they're listed above). It seems that the intelligently labelled genres are insufficient for several possible reasons. Manual attempts to classify music often fall short of the artistic vision, and emotions that are evoked by a work.

![Super Genre Confusion Matrix]({{site.url}}/{{site.baseurl}}/assets/images/figures/cm_other/super_rf.png)

This does not mean, however, that there does not exist a good set of 8 super genres to classify music into. The out-of-sample accuracy rate for the clustered genres was very high, at 96.3%. So the clusters, in addition to being relatively compact (as seen from the WCSS) are also very distinct from each other, and describe the different "kinds" of song much better than the "intelligently" chosen genres.

![Cluster Genre Confusion Matrix]({{site.url}}/{{site.baseurl}}/assets/images/figures/cm_other/cluster_rf.png)

## But Why?
But what, exactly, are the models picking up on, what is separating the groupings? SHAP is a tool that allows us to look into the black box, as it were, and find that out. A small sample of data was analyzed using SHAP for both models. These are seen below.

![Super Genre SHAP]({{site.url}}/{{site.baseurl}}/assets/images/figures/cm_other/super_shap.png)
![Cluster Genre SHAP]({{site.url}}/{{site.baseurl}}/assets/images/figures/cm_other/cluster_shap.png)

We can see that though the chosen super genres do use some variables slightly differently (rock is better separated by acousticness than instrumentalness, for example), most use every attribute approximately the same amount. This could be a part of the reason that the models have a difficult time separating these groups, and may be fixed with the collection of variables that better describe the genres individually. The clusters, however, are separated by different variables, such as cluster using the "key" attribute, but minimally using the rest.

## Conclusion
Music is messy. The way that we classify music may not be the best. In this project I suggested two competing methods for labelling music. One was simply an extension of how it has been done in the past, combining subgenres to be more cohesive and identifiable; the other was using cluster analysis to discover natural groupings of musical qualities. These clusters, though less easily interpreted by humans, are much more powerful and better describe broadly encompassing genres than these intelligently labelled identifiers. However many dimensions are needed to describe how these clusters are formed. Even UMAP cannot condense the essential dimensionality of the musical structure down to two dimensions, as can be seen in the final figure.

![Cluster Genre SHAP]({{site.url}}/{{site.baseurl}}/assets/images/figures/cm_other/umap.png)




