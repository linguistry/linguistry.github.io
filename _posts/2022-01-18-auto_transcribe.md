---
layout: single
title: "auto-transcribe"
categories: '2022'
tag: [python, transcribe, speech recognition, google, 음성인식, 전사]
---

### Auto transcribe (English spoken data) 
This is a python code in Colab to automatically transcribe the wav file in English using SpeechRecognition library (https://pypi.org/project/SpeechRecognition/). 


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

##### Import libraries
```python 
# install SpeechRecognition library
!pip install SpeechRecognition
!pip install pydub

# importing libraries 
import speech_recognition as sr 
import os 
from pydub import AudioSegment
from pydub.silence import split_on_silence
```

##### create a speech recognition object
```python
r = sr.Recognizer()
```

The following function code is modified from https://www.thepythoncode.com/code/using-speech-recognition-to-convert-speech-to-text-python .
##### make a function to split the audio file into chunks and apply speech recognition
```python
# split the audio file into chunks 
# apply speech recognition
def transcribe_audio_file(audio_input):
    # open the audio file
    sound = AudioSegment.from_wav(audio_input)  
    # split audio sound 
    chunks = split_on_silence(sound,
        # split on silences with a minimum length of 500ms(0.5 sec)
        min_silence_len = 500,
        # any sound under -14 dBFS is considered silence
        silence_thresh = sound.dBFS-14,
        # keep 0.5 sec of leading/trailing silence
        keep_silence=500,
    )
    folder_name = "audio-chunks"
    # create a directory to store the audio chunks
    if not os.path.isdir(folder_name):
        os.mkdir(folder_name)
    whole_text = ""
    # process each chunk 
    for i, audio_chunk in enumerate(chunks, start=1):
        # export audio chunk and save it in the `folder_name` directory
        chunk_filename = os.path.join(folder_name, f"chunk{i}.wav")
        audio_chunk.export(chunk_filename, format="wav")
        # recognize the chunk
        with sr.AudioFile(chunk_filename) as source:
            audio_listened = r.record(source)
            # try converting it to text
            try:
                text = r.recognize_google(audio_listened)
            except sr.UnknownValueError as e:
                print("Error:", str(e))
            else:
                text = f"{text.capitalize()}." + "\n"
                whole_text += text
    # return the text for all chunks detected
    return whole_text
```
##### Load wav file
```python 
filename = os.path.join(data_dir, 'audio_filename.wav')
print(filename)
```
### Run the function 
```python 
transcript = transcribe_audio_file(input_audio)
transcript
```
### Export the transcript file 
```python 
text_file = open(os.path.join(data_dir, "transcript_filename.txt"), "w")
text_file.write(transcript)
text_file.close()
```