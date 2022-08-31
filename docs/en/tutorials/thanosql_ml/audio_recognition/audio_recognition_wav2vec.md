---
title: Create a speech recognition model to dictate an audio file
---

# **Create a speech recognition model to dictate an audio file**

## Preface

- Tutorial Difficulty : ★☆☆☆☆
- 10 min read
- Languages : [SQL](https://ko.wikipedia.org/wiki/SQL) (100%)
- File location : tutorial/tutorial_en/ml/audio_recognition_model/speech_recognition_en.ipynb
- References : [LibriSpeech Data Set](http://www.openslr.org/12), [wav2vec 2.0: A Framework for Self-Supervised Learning of Speech Representations](https://arxiv.org/abs/2006.11477)
- Updated Date : {{ git_revision_date_localized }}

## Tutorial Introduction

!!! note "Understanding Speech Recognition Technology"
    Speech recognition technology, also called computer speech recognition or speech-to-text, allows programs to process human speech into text format. Recently, it is being used in a wide range of fields including automobiles, medical fields, and everyday life using artificial intelligence speakers or smartphones. Recently [Machine Learning](https://en.wikipedia.org/wiki/%EA%B8%B0%EA%B3%84_%ED%95%99%EC%8A%B5) Speech recognition technology using algorithms integrates the grammar, syntax, structure, and construction of audio and speech signals to understand and process speech.

!!! warning ""
    In general, it can be confused with Voice Recognition, which focuses only on identifying individual users' voices.

Today, speech recognition technology is being applied in various industries. Advances in speech recognition technology are expanding from automatic interpretation for simple travel to interpreting difficult business meetings. In addition, voice recognition technology has developed into speech synthesis technology, which acts as a virtual guide or secretary, and is also applied by mimicking the voice of a specific entertainer or celebrity and converting a predetermined fingerprint into a voice.

**Below is a usage and example of the ThanoSQL speech recognition model.**

- Voice recognition technology converts phone consultation data into text, enabling customer sentiment analysis and consultation trend analysis. Through voice recognition technology, counselors can improve customer service by quickly providing information that answers customer inquiries or similar cases in the past.
  In addition, after the end of the consultation, customer satisfaction can be checked by trend by indirectly measuring customer satisfaction through sentiment analysis using the data stored in voice.

- Using voice recognition technology, you can write faster than notes written with a keyboard, and you can quickly search for specific keywords even in long voice files.

!!! note "In this tutorial"
    :point_right: Librispeech [Panayotov et al. 2015] is one of the most widely used large-scale English voice data in speech recognition research, and it is the result of [LibriVox project](https://libribox.org/), a user-participating audiobook project. It was created by processing approximately 1,000 hours of recorded audiobook data sampled at 16 kHz. The target table for the tutorial consists of the pre-uploaded audio file path and script. This tutorial aims to convert audio files to text.

!!! warning "Tutorial Notes"
    - Audio file formats currently supported by ThanoSQL are '.wav', '.flac'.
    - A column indicating the audio file path and a column indicating the text corresponding to the target value must exist in the table.
    - The base model of the speech recognition model (`Wav2Vec2En`) uses GPU. Depending on the size of the model used and the batch size, you may run out of GPU memory. In this case, try using a smaller model or reduce the batch size.

## **0. Data set preparation**

To use ThanoSQL's query syntax, [ThanoSQL Workspace](/en/getting_started/how_to_use_ThanoSQL/#5-thanosql-workspace)
As mentioned in , you need to generate an API token and run the query below.

```sql
%load_ext thanosql
%thanosql API_TOKEN=<Issued_API_TOKEN>
```

```sql
%%thanosql
COPY librispeech_train
OPTIONS(overwrite = True)
FROM "tutorial_data/librispeech_data/librispeech_train.csv"
```

```sql
%%thanosql
COPY librispeech_test
OPTIONS(overwrite = True)
FROM "tutorial_data/librispeech_data/librispeech_test.csv"
```

!!! note "**Query Details**"
    - Use the "**COPY**" query syntax to specify the name of the data set to be stored in the DB.
    - Specify the options to use for **COPY** via the "**OPTIONS**" query syntax.
        - "overwrite": If a data set with the same name exists in the DB, it can be overwritten. If True, the old data set is replaced with the new data set (True|False, DEFAULT : False)

## **1. Check data set**

For this tutorial, we use the <mark style="background-color:#FFEC92 ">librispeech_train</mark> table stored in ThanoSQL DB. Execute the query statement below to check the table contents.

```sql
%%thanosql
SELECT *
FROM librispeech_train
LIMIT 5
```

[![IMAGE](/img/thanosql_ml/audio_recognition/audio_recognition_wav2vec/train_data.png)](/img/thanosql_ml/audio_recognition/audio_recognition_wav2vec/train_data.png)

!!! note "Understanding data"
    - <mark style="background-color:#D7D0FF ">audio_path</mark>: location path of audio file
    - <mark style="background-color:#D7D0FF ">text</mark> : Target value of the corresponding voice (Target, script)

```sql
%%thanosql
PRINT AUDIO
AS
SELECT audio_path
FROM librispeech_train
LIMIT 3
```

[![IMAGE](/img/thanosql_ml/audio_recognition/audio_recognition_wav2vec/print_audio.png){: style="width:400px"}](/img/thanosql_ml/audio_recognition/audio_recognition_wav2vec/print_audio.png)

## **2. Predict Speech Recognition Results Using Pretrained Models**

Using the pre-trained speech recognition model <mark style="background-color:#E9D7FD ">tutorial_audio_recognition</mark> by running the following query syntax:
Predict the outcome.

```sql
%%thanosql
PREDICT USING tutorial_audio_recognition
OPTIONS (
  audio_col='audio_path',
  text_col='text',
  epochs=1,
  batch_size=8
  )
AS
SELECT *
FROM librispeech_train
```

[![IMAGE](/img/thanosql_ml/audio_recognition/audio_recognition_wav2vec/predict_on_test_data_1.png)](/img/thanosql_ml/audio_recognition/audio_recognition_wav2vec/predict_on_test_data_1.png)

## **3. Create a speech recognition model**

Create a speech recognition model using the <mark style="background-color:#FFEC92 ">librispeech_train</mark> dataset from the previous step. Execute the query syntax below to create a model named <mark style="background-color:#E9D7FD ">my_speech_recognition_model</mark>.  
(Estimated time required for query execution: 1 min)

```sql
%%thanosql
BUILD MODEL my_speech_recognition_model
USING Wav2Vec2En
OPTIONS (
  audio_col='audio_path',
  text_col='text',
  epochs=1,
  batch_size=4 ,
  overwrite=True
  )
AS
SELECT *
FROM librispeech_train
```

!!! note "Query Details"
    - Use the "**BUILD MODEL**" query syntax to create and train a <mark style="background-color:#E9D7FD ">my_speech_recognition_model</mark> model.
    - Specify to use `Wav2Vec2En` as the base model through the "**USING**" query syntax.
    - Specify the options to use for model creation via the "**OPTIONS**" query syntax.
        - "audio_col" : the name of the column containing the audio path to be used for training
        - "text_col" : the name of the column containing the audio script information
        - "epochs" : the number of times to train all training data sets
        - "batch_size" : one training The size of the data set bundle read from
        - "overwrite": Set whether overwriting is possible if a model with the same name exists. If True, the old model is changed to the new model (True|False, DEFAULT : False)

!!! tip ""
    Here, we set "epochs" to 1 to learn quickly. In general, larger numbers take more computation time, but predictive performance increases as training progresses.

## **4. Predict speech recognition results using the model you created**

Use the speech recognition model you created in the previous step to predict the target (script) of a particular speech (data table not used for learning, <mark style="background-color:#FFEC92">librispech_test</mark>). After performing the query below, the prediction results are returned in the <mark style="background-color:#D7D0FF">predicted</mark> column.

```sql
%%thanosql
PREDICT USING my_speech_recognition_model
OPTIONS (
  audio_col='audio_path'
  )
AS
SELECT *
FROM librispeech_test
```

[![IMAGE](/img/thanosql_ml/audio_recognition/audio_recognition_wav2vec/predict_on_test_data_2.png)](/img/thanosql_ml/audio_recognition/audio_recognition_wav2vec/predict_on_test_data_2.png)

!!! note "Query Details"
    - Use the <mark style="background-color:#E9D7FD">my_speech_recognition_model</mark> model created in the previous step with the "**PREDICT USING**" query syntax.
    - Specify the options to use for prediction via the "**OPTIONS**" query syntax.
        - "audio_col" : The name of the column containing the audio path to be used for prediction

## **5. In conclusion**

In this tutorial, we created a speech recognition model using the <mark style="background-color:#FFD79C">LibriSpeech</mark> data set. As this is a beginner-level tutorial, we have focused on operation rather than explaining the process to improve accuracy. Speech recognition models can improve their accuracy through fine tuning for each platform or service. Use your own data to train the base model and improve its performance. By combining various unstructured data (image, video, text, etc.) and numeric data, you can create your own model and create competitive services.

The next step, the [Creating an Intermediate Speech Recognition Model] tutorial, takes a deeper dive into the speech recognition model. If you want to learn more about how to build your own speech recognition model for your service, try the following tutorials. <br>

- [How to Upload to ThanoSQL DB](/en/how-to_guides/ThanoSQL_connecting/data_upload/)
- [Creating an Intermediate Speech Recognition Model]
- [Deploying My Speech Recognition Models](/en/how-to_guides/ThanoSQL_connecting/thanosql_api/rest_api_thanosql_query/)

!!! tip "**Inquiries on model distribution for 'my own' service**"
    If you have difficulty creating your own model using ThanoSQL or applying it to service, please feel free to contact me below😊

    Inquiries about building a speech recognition model: contact@smartmind.team
