---
layout: single
title: "Python File Rename Method"
categories: '2023'
tag: [python, file_rename, pandas]
---

로컬 드라이드의 파일명을 일괄 변경할 때

```python 
# 엑셀에 저장된 워드리스트 불러오기. 
import pandas as pd
import os
import shutil

# 파일 리스트가 들어 있는 xlsx 불러오기

df = pd.read_excel('file_list.xlsx')
df
```
![파일리스트]!(https://github.com/linguistry/linguistry.github.io/assets/59856081/ec2b560b-343b-4ef7-91cc-6073bc4581bd)


```python
for index, row in df.iterrows():
    original_name = row['Original']
    converted_name = row['Converted']
    
    # Construct the full file paths
    original_path = os.path.join('folder_path', original_name)
    converted_path = os.path.join('folder_path', converted_name)
    
    # Check if the original file exists and if the converted file does not exist
    if os.path.exists(original_path) and not os.path.exists(converted_path):
        try:
            shutil.move(original_path, converted_path)  # Rename the file
            print(f'Renamed {original_name} to {converted_name}')
        except Exception as e:
            print(f'Error renaming {original_name}: {e}')
    else:
        print(f'File {original_name} does not exist or {converted_name} already exists.')

``` 


