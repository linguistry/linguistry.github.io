---
layout: single
title: "extract YouTube subtitle: single video"
categories: '2022'
tag: [python, subtitle, YouTube, corpus]
---

This is a python code in Colab to extract subtitles in a Youtube (single) video using YouTube API. 


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
# install libraries
!pip install google-api-python-client
!pip install youtube_transcript_api

# importing libraries 
import pandas
import json
from googleapiclient.discovery import build # Google API request
from youtube_transcript_api import YouTubeTranscriptApi

# Enter your YouTube api key
api_key = 'Enter your YouTube API key here' 
video_id = 'Enter YouTube ID here' 
# If the address of the Youtube is https://youtu.be/zOjov-2OZ0E then, the video id is tha last part "zOjov-2OZ0E".

```

##### extract subtitles 
```python
subtitles = YouTubeTranscriptApi.get_transcript(video_id, languages=['en']) # English subtitle

prompts = []
words = []
for subtitle in subtitles: 
  time = subtitle['start']
  prompt = subtitle['text']
  words.append(prompt)
  prompts.append([time, prompt])
```

### Export the subtitle to txt file
```python 
output_file = open(os.path.join(data_dir,'FileName.txt'), 'w')
text = ["{} ".format(word) for word in words]
output_file.writelines(text)
output_file.close()
```
### Export the transcript file to xlsx file
```python 
df = pandas.DataFrame(prompts, columns =['time', 'subtitle'])
df.to_excel(os.path.join(data_dir,'FileName.xlsx'), index=None)
df
```
![](/assests/images/2022-01-23-14-57-10.png) 
