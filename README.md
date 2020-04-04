# Performance Reporting with Twitter Analytics data
**by Gerard Tieng**  

Today, we'll be using Python to chart Twitter performance metrics based on the standard set of data provided by Twitter Analytics. By the end of this project, we will be able to vizualize or list the following pieces of information:

- Most active windows on Twitter.
- Most mentioned accounts and hashtags.
- Twitter engagement totals and averages per tweet
- Optimal time windows for the best engagement performance.
- Themes of the user's most popular content.

![](https://github.com/gtieng/twitter_analytics/blob/master/readme_images/yearly_impressions.png)

## Getting Started
Before we begin, please refer to the code below which details the libraries we'll use to explore our Twitter data:

```
#for dataframe management and slicing
import pandas as pd
import numpy as np
import datetime as dt

#main visualization library
import matplotlib.pyplot as plt
%matplotlib inline

#string cleaning and visualization
import re
from wordcloud import WordCloud, STOPWORDS
```

## Data Cleaning and Transformations
Native data from Twitter Analytics is fairly clean, but we'll still need to perform a few transformations of the data to get the answers we want from it. Below are explanations as to how to add custom sorting columns plus a simple sorting method to separate organic tweets from replies.

### Adding Time Columns
Using the `datetime` library is great for parsing months, days, and days of the week from the `time` columns. We'll be adding these classifications to their own columns for further aggregate plotting further in the project.

```
gerard["time"] = pd.to_datetime(gerard["time"])
gerard["hour"] = gerard["time"].dt.hour
gerard["month"] = gerard["time"].dt.month
gerard["dayofweek"] = gerard["time"].dt.dayofweek
```

### Separating Organic Tweets from Replies
When it comes to measurement, separating the organic tweets from replies is necessary to compare your apples against apples. Organic tweets are more often than not a one-to-many conversation--usually leading to higher engagement totals--while replies are typically a one-to-one type of dialogue.

As all replies begin with "@", using Python's `string.startswith()` method is a very easy way to filter those tweets from the rest. Use of the `~` symbol is just as easy to perform the opposite.

```
gerard_tweets = gerard[~gerard["Tweet text"].str.startswith("@")]
gerard_replies = gerard[gerard["Tweet text"].str.startswith("@")]
```

## Plotting User Activity
Before measuring engagement, it is important to understand the patterns of user activity. As Twitter conversations move relatively in real-time (as compared to other popular social media platforms), the times in which the majority of engagement will likely be gathered in the first few moments any content is posted. Engagement patterns are highly affected by user activity.

With the following code, we'll form three custom dataframes based on aggregated month, day, and hour totals and plot them to visualize this user's most popular windows of activity.

```
month_activity = gerard_tweets.groupby("month").count()
day_activity = gerard_tweets.groupby("dayofweek").count()
hour_activity = gerard_tweets.groupby("hour").count()


fig, ax = plt.subplots(3, 1, figsize=(15, 18))

ax[0].bar(month_activity.index, month_activity["Tweet permalink"], tick_label=month_activity.index)
ax[0].set_xlabel("month")
ax[0].set_ylabel("tweet activity")

ax[1].bar(day_activity.index, day_activity["Tweet permalink"], tick_label=day_activity.index)
ax[1].set_xlabel("day")
ax[1].set_ylabel("tweet activity")

ax[2].bar(hour_activity.index, hour_activity["Tweet permalink"], tick_label=hour_activity.index)
ax[2].set_xlabel("hour")
ax[2].set_ylabel("tweet activity")

plt.show()
```
![](https://github.com/gtieng/twitter_analytics/blob/master/readme_images/activity_plot.png)

## Measuring Follower Engagements
In measuring follower engagements, we'll examine performance based on the same windows of month, day and year. This time, we'll change the aggregate method to `DataFrame.groupby().sum()` to measure the totals at each period interval. Plotting each metric is similar to code above. Here's a demonstration of types of engagement by the hour.

```
hour_engagement = gerard_tweets.groupby("hour").sum()

fig, ax = plt.subplots(2,2, figsize=(15,10))

ax[0,0].bar(hour_engagement.index, hour_engagement["impressions"], tick_label=hour_engagement.index, color="blue")
ax[0,0].set_title("impressions by hour")

ax[0,1].bar(hour_engagement.index, hour_engagement["likes"], tick_label=hour_engagement.index, color="red")
ax[0,1].set_title("likes by hour")

ax[1,0].bar(hour_engagement.index, hour_engagement["retweets"], tick_label=hour_engagement.index, color="green")
ax[1,0].set_title("retweets by hour")

ax[1,1].bar(hour_engagement.index, hour_engagement["replies"], tick_label=hour_engagement.index, color="orange")
ax[1,1].set_title("replies by hour")
```
![](https://github.com/gtieng/twitter_analytics/blob/master/readme_images/engagement_plot.png)

## Content Analysis
For the last section of this project, we'll get to dive into the content of the tweets themselves to see trends in popular keyword topics by visualizing them in word clouds.

Before we construct the word cloud, we'll need to perform a minor cleaning of the `Tweet text` column. When Twitter Analytics exports its data, the raw Tweet strings will include special characters including `amp&` and URLs in the `https://t.co` format that will dominate the visualization if not first removed. 

First, we'll sort our data to find the most popular tweets by impressions and slice the top 20 tweets. Then we'll use a simple function in conjunction with the `Series.apply()` method to clean the `Tweet text` column.

```
def tweet_cleaner(tweet):
    tweet = re.sub(r"https://\S+", "", tweet) #eliminates urls
    tweet = re.sub(r"amp\b", "", tweet) #eliminates ampersand
    return tweet
    
best_impressions = gerard_tweets.sort_values("impressions", ascending=False)[0:20]
best_impressions = best_impressions["Tweet text"].apply(tweet_cleaner)
```
Now with our cleaned `Tweet text` series, it can then be fed into the word cloud.
``` 
#bag of words
comment_words = ' '

stopwords = set(STOPWORDS) 

for val in best_impressions["Tweet text"]: 

    # typecaste each val to string 
    val = str(val) 

    # split the value 
    tokens = val.split() 

    # Converts each token into lowercase 
    for i in range(len(tokens)): 
        tokens[i] = tokens[i].lower() 
        
    #fills bag of words
    for words in tokens: 
        comment_words = comment_words + words + ' '


wordcloud = WordCloud(width = 800, height = 800, 
                background_color ='white', 
                stopwords = stopwords, 
                min_font_size = 10).generate(comment_words) 

# plot the WordCloud image                        
plt.figure(figsize = (8, 8), facecolor = None) 
plt.imshow(wordcloud) 
plt.axis("off") 
plt.tight_layout(pad = 0)
```


