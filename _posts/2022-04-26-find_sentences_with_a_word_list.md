---
layout: single
title: "find sentences with a word list"
categories: '2022'
tag: [python, sentence, corpus, keyword, NLTK]
---

단어리스트의 단어를 포함한 문장을 NLTK 코퍼스에서 찾아 단어리스트의 예문 채우기 

This is a python code in Colab to find sentences that include the words in the vocabulary list. 


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

##### Input Word list 
```python 
import pandas as pd
import numpy as np

voca = pd.read_excel(os.path.join(data_dir, "input_words.xlsx"))
voca.head()
```

##### Prepara Corpora 
```python
# Corpora in NLTK
import nltk
from nltk import sent_tokenize, word_tokenize

nltk.download('inaugural')
nltk.download('punkt')
nltk.download('gutenberg')
nltk.download('brown')
nltk.download('genesis')

from nltk.corpus import inaugural
from nltk.corpus import gutenberg
from nltk.corpus import brown
from nltk.corpus import genesis
```
```python
# Combine all corpora
corpus = list()
corpus.extend(nltk.corpus.inaugural.sents())
corpus.extend(nltk.corpus.gutenberg.sents())
corpus.extend(nltk.corpus.brown.sents())
corpus.extend(nltk.corpus.genesis.sents())

corpus_sent = [" ".join(list_of_words) for list_of_words in corpus]

```
##### Find Sentences
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
##### Extract sentence index function
```python
# "lines=" indicate the number of sentences to be extracted.

def sent_find(word, corpus, lines = 2):

    results = []
    for idx, s in enumerate(corpus):
        if s.find(word) != -1:  # If a substring doesn't exist inside the string, it returns -1.
            if lines <= 0 : 
              break
            results.append(idx)
            lines -= 1

    try: 
      return results    
    except ValueError:
      pass 
```

##### Extract sentences with indices
```python
### function 
def sent_extract(sent_nos, corpus):
  sents = str()
  no_len = len(sent_nos)
  for no in sent_nos:
    sents += corpus[no]
    if no_len > 1:
      sents += '\n'   # for a new line 
    no_len -= 1
  return sents
```

##### Run the function
```python 
voca["Sent_No"] = voca["Word"].apply(lambda x: sent_find(x, corpus_sent, 2)) # two sentences

voca_book = voca[["No", "Word", "Meaning", "Sentences"]]
voca_book.to_excel(os.path.join(data_dir, 'output_sent_nltkCorpus.xlsx'))
voca.head()
```

