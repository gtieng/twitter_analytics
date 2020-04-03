# Performance Reporting with Twitter Analytics data

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
