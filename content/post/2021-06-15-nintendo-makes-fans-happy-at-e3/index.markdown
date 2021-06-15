---
title: Nintendo makes fans happy at E3
author: Najmus Saqib
date: '2021-06-15'
slug: nintendo-makes-fans-happy-at-e3
categories:
  - rstats
  - tidytext
  - sentiment analysis
tags:
  - rstats
  - tidytext
  - sentiment analysis
  - nintendo
  - video games
subtitle: ''
summary: 'Online fans appear happy with Nintendo for a change. We dig deeper into tweets after the Nintendo E3 event '
authors: []
lastmod: '2021-06-15T16:45:58-04:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---



While the Nintendo Switch remains the best selling video game console on the market, passionate gamers have made no secret of their disappointment due to a lack of exciting announcements over the past couple of years. While personally this sentiment seems very misplaced, it is at least partially understandable in light of the new consoles and exciting new video games other companies have released in the recent past.

## Twitterverse rejoices!

If brief perusal of Twitter is anything to go by, it seems that Nintendo's announcements today at the E3 2021 gaming convention have succeeded in reinvigorating its online fanbase.


```r

# Tweet score
tweet_score <- lapply(nin_twitter$text, function(x){sentiment_bing(x)})

tweet_sentiment <- tweet_score %>%
  unlist() %>%
  tibble()

# split tweets into individual words
sentiment <- nin_twitter %>% 
  unnest_tokens(output = 'word', input = 'text')

# bing sentiment analysis
bing_sentiment <- sentiment %>%
  inner_join(get_sentiments("bing")) %>%
  count(word, sentiment, sort = TRUE) %>%
  ungroup()

## Plots
ggplot(tweet_sentiment,aes(x=.)) +
  geom_histogram() +
    labs(title = "Distribution of sentiment in tweets containing #NintendoDirect",
       y = "Count of tweets",
       x = NULL) +
  theme_minimal()
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-1-1.png" width="672" />

# Breakdown by specific words

The above graph seems to indicate that the sample of tweets collected right after Nintendo's presentation concluded skew towards a negative sentiment, which goes counter to my expectations. Let's dig deeper.


```r
# split tweets into individual words
sentiment <- nin_twitter %>% 
  unnest_tokens(output = 'word', input = 'text')

# bing sentiment analysis
bing_sentiment <- sentiment %>%
  inner_join(get_sentiments("bing")) %>%
  count(word, sentiment, sort = TRUE) %>%
  group_by(sentiment) %>%
  top_n(10) %>%
  ungroup() %>%
  mutate(word = reorder(word, n))
## Joining, by = "word"
## Selecting by n
  

## Plots
ggplot(bing_sentiment,aes(word, n, fill = sentiment)) +
  geom_col(show.legend = FALSE) +
  facet_wrap(~sentiment, scales = "free_y") +
  labs(title = "Tweets containing #NintendoDirect",
       y = "Contribution to sentiment",
       x = NULL) +
  coord_flip() +
  theme_minimal()
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-2-1.png" width="672" />
The breakdown sheds some much needed light. Most of the negative sentiment appears to be coming from words like "wild,""dread," and "smash," which is misleading as the most prominent games announced by Nintendo today contain those words in their titles. Additionally, the Bing lexicon (will perhaps be further elaborated in a future post) defines words like "hype" as negative, which is definitely not how the word is used on Twitter the vast majority of times.

Taking these facts into account, it seems that overall sentiment is positive. Obviously this analysis is very surface level, but hopefully it has been helpful in illustrating some of the strengths and weaknesses associated with conducting sentiment analysis on Tweets. A future post will dig deeper on how a more robust analysis can be undertaken using the `tidytext` R package. 
