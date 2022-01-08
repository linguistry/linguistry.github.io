---
layout: single
title: "Scraping: Movie Reviews"
categories: '2022'
tag: [python, scraping, coding, colab]
---

### Web crawling: Movie Reviews 
This is a python code in Colab to extract individual movie review comments from Rotten Tomatoes site https://www.rottentomatoes.com 

##### Mount Drive in Colab
```python 
# Mount my Google Drive (storage)
from google.colab import drive
drive.mount('/content/gdrive')

# data dir
import os
data_dir = '/content/gdrive/MyDrive/YOUR FOLDER NAME'  # Your data directory in Colab 
os.listdir(data_dir)
```

##### Rotten Tomatoes Review
```python 
# import library
# !pip install beautifulsoup4
# !pip install requests
import requests 
from bs4 import BeautifulSoup 
import pandas as pd
```

##### Fetch webpage contents using requests
To get everything about a webpage: use the get method from requests.
```python
url = "https://www.rottentomatoes.com/tv/squid_game/s01/reviews?type=user"
req = requests.get(url)
print(req) # get a status code. 200 is OK and it means no error.
# https://en.wikipedia.org/wiki/List_of_HTTP_status_codes 
```
To get the HTML as a string, use the `text` property of the Response object.
```python
html = req.text # get html content
print(html)
```

```python
page_num = range(1,3) # page_num can be changed
url = "https://www.rottentomatoes.com/tv/squid_game/s01/reviews?type=user&sort=&page="
url_list = []

for page in page_num:
  url_list.append(url+str(page))
url_list
```
['https://www.rottentomatoes.com/tv/squid_game/s01/reviews?type=user&sort=&page=1',
 'https://www.rottentomatoes.com/tv/squid_game/s01/reviews?type=user&sort=&page=2']

```python
movie_review = []
for url in url_list:
  req = requests.get(url)
  html = req.text
  # soup = BeautifulSoup(html, 'html.parser') 
  soup = BeautifulSoup(html, 'lxml') 

  name = soup.select('div.audience-reviews__user-wrap > div > a')
  review = soup.select('div.audience-reviews__review-wrap > p.audience-reviews__review--mobile.js-review-text.clamp.clamp-4.js-clamp')
  
  for item in zip(name, review):
    movie_review.append(
        {
            'name'      : item[0].text.replace('\n', '').replace('\t', '').replace('  ', ''),
            'review'    : item[1].text.replace('\n', '').replace('\t', '').replace('  ', ''),
        }
    )
data = pd.DataFrame(movie_review)
data.to_csv(os.path.join(data_dir,"movie_review_squidgame.csv"), encoding='utf-8-sig')
data
```
