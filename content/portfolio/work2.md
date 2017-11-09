+++
date = "2016-11-05T19:41:01+05:30"
title = "Exploring, Clustering, and Mapping Toronto’s Crimes"
draft = false
image = "img/portfolio/business-card-26.jpg"
showonlyimage = false
weight = 1
+++

>library(ggplot2)
library(ggthemes)
library(dplyr)
library(viridis)
library(tidyr)
library(cluster)
library(ggmap)
library(maps)
After a little bit of exploration, I found that there were many duplicated event_unique_id. Let’s drop them.

>toronto <- read.csv('toronto_crime.csv')
toronto <- subset(toronto, !duplicated(toronto$event_unique_id))
unique(toronto$occurrenceyear)
unique(toronto$reportedyear)
> unique(toronto$occurrenceyear)
 [1] 2016 2015 2014 2012 2010 2013 2011 2003 2007 2008 2006 2002 2001   NA 2005
[16] 2009 2000
> unique(toronto$reportedyear)
[1] 2016

Find anything interesting? The occurrence year ranged from 2000 to 2016, but report year is only 2016. This means people came to police to report incidents happened 16 years ago. Let’s have a look how many late reported incidents in our data.

>year_group <- group_by(toronto, occurrenceyear)
crime_by_year <- summarise(year_group,
                          n = n())
crime_by_year
# A tibble: 17 x 2
   occurrenceyear     n
             
 1           2000     2
 2           2001     2
 3           2002     3
 4           2003     2
 5           2005     1
 6           2006     1
 7           2007     5
 8           2008     1
 9           2009     1
10           2010    10
11           2011     3
12           2012     8
13           2013    21
14           2014    37
15           2015   341
16           2016 27705
17             NA     4

2 incidents occurred in 2000, 2 occurred in 2001 and so on. The vast majority of occurrences happened in 2016. So we are going to keep 2016 only. And I am also removing all the columns we do not need and four rows with missing values.

> drops <- c("X", "Y", "Index_", "ucr_code", "ucr_ext", "reporteddate", "reportedmonth", "reportedday", "reporteddayofyear", "reporteddayofweek", "reportedhour", "occurrencedayofyear", "reportedyear", "Division", "Hood_ID", "FID")
toronto <- toronto[, !(names(toronto) %in% drops)]
toronto <- toronto[toronto$occurrenceyear == 2016, ]
toronto <- toronto[complete.cases(toronto), ]
Explore
What are the major crimes in 2016?
indicator_group <- group_by(toronto, MCI)
crime_by_indicator <- summarise(indicator_group, n=n())
crime_by_indicator <- crime_by_indicator[order(crime_by_indicator$n, decreasing = TRUE),]
ggplot(aes(x = reorder(MCI, n), y = n), data = crime_by_indicator) +
  geom_bar(stat = 'identity', width = 0.5) +
  geom_text(aes(label = n), stat = 'identity', data = crime_by_indicator, hjust = -0.1, size = 3.5) +
  coord_flip() +
  xlab('Major Crime Indicators') +
  ylab('Number of Occurrences') +
  ggtitle('Major Crime Indicators Toronto 2016') +
  theme_bw() +
  theme(plot.title = element_text(size = 16),
        axis.title = element_text(size = 12, face = "bold"))

Gives this plot:


When she reached the first hills of the Italic Mountains, she had a last view back on the skyline of her hometown Bookmarksgrove, the headline of Alphabet Village and the subline of her own road, the Line Lane.
<!--more-->

Far far away, behind the word mountains, far from the countries Vokalia and Consonantia, there live the blind texts. Separated they live in Bookmarksgrove right at the coast of the Semantics, a large language ocean.

A small river named Duden flows by their place and supplies it with the necessary regelialia. It is a paradisematic country, in which roasted parts of sentences fly into your mouth.

1. Lorem ipsum dolor sit amet, consectetuer adipiscing elit.
2. Aliquam tincidunt mauris eu risus.

> The Big Oxmox advised her not to do so, because there were thousands of bad Commas, wild Question Marks and devious Semikoli, but the Little Blind Text didn't listen. She packed her seven versalia, put her initial into the belt and made herself on the way.

## Header Level 2

Even the all-powerful Pointing has no control about the blind texts it is an almost unorthographic life One day however a small line of blind text by the name of Lorem Ipsum decided to leave for the far World of Grammar.

The Big Oxmox advised her not to do so, because there were thousands of bad Commas, wild Question Marks and devious Semikoli, but the Little Blind Text didn't listen. She packed her seven versalia, put her initial into the belt and made herself on the way.

* Lorem ipsum dolor sit amet, consectetuer adipiscing elit.
* Aliquam tincidunt mauris eu risus.

When she reached the first hills of the Italic Mountains, she had a last view back on the skyline of her hometown Bookmarksgrove, the headline of Alphabet Village and the subline of her own road, the Line Lane. Pityful a rethoric question ran over her cheek, then  
