---
layout: single
title: "extract YouTube subtitle using keyword search"
categories: '2022'
tag: [python, subtitle, YouTube, corpus, keyword]
---

키워드 서치 후 유튜브 자막 긁어오기(복수의 비디오, 선택 순위 지정)

This is a python code in Colab to extract subtitles in a Youtube (single) video after searching a keyword. 


##### Mount drive in Colab
```python 
# Mount my Google Drive (storage)
from google.colab import drive
drive.mount('/content/gdrive')

# data dir
import os
data_dir = '/content/gdrive/MyDrive/YOUR FOLDER NAME'  # Your data directory in Colab 
os.listdir(data_dir)
```

##### YouTube Data API
```python 
!pip install youtube_transcript_api

# Keyword Query
from googleapiclient.discovery import build
from googleapiclient.errors import HttpError
from oauth2client.tools import argparser

# transcribing api
from youtube_transcript_api import YouTubeTranscriptApi

# text cleaning
import pandas
import re 
import string

```
##### Enter my API key 
```python
api_key = "  "

```
```python
##### Enter search keyword
SearchQuery = "Language Testing" 
```
##### search video clips
```python
# build(YOUTUBE_API_SERVICE_NAME, YOUTUBE_API_VERSION, API_KEY)
api_obj = build('youtube', 'v3', developerKey=api_key)
request = api_obj.search().list(
    q = SearchQuery, 
    order = "viewCount",      # viewCount, date, rating, relevance, title, videoCount
    part = "snippet",
    type = "video",  # video, playlist, channel
    maxResults=10) # first 10 video clips by view counts
response = request.execute()
```
##### extract video ids
```python
video_ids = []
titles = []
i = 1 # Add numbering before "title"
for t in response["items"]:
  video_ids += {t["id"]["videoId"]}
  ttl = t["snippet"]["title"]
  title = str(i) + "_" + ttl 
  # print(title)
  exclude = ['/', ':', '(', ')', '|']
  title = {''.join(ch for ch in title if ch not in exclude)}
  # print(title)
  titles += title
  i += 1
video_titles = dict(zip(video_ids, titles))
print(video_titles)
```

##### extract subtitles function
```python
### function 
def make_transcript(video_id): 
  try: 
      subtitles = YouTubeTranscriptApi.get_transcript(video_id, languages=['en'])  
      prompts = []
      words = []
      
      for subtitle in subtitles:        
          time = subtitle['start']
          prompt = subtitle['text']
          words.append(prompt)
          prompts.append([time, prompt])
       
      title = video_titles[video_id]
      print(title)

      with open(os.path.join(data_dir, SearchQuery + "_" + title + "_" + video_id + '.txt'), 'w') as file: 
        file.writelines(words)
        file.close()

      df = pandas.DataFrame(prompts, columns =['time', 'subtitle'])
      df.to_excel(os.path.join(data_dir, SearchQuery + "_" + title + "_" + video_id +'.xlsx'), index=None)
  except:
    print("no_subtitle")
```

##### Run the function
```python 
for video_id in video_ids: 
  make_transcript(video_id)
```

