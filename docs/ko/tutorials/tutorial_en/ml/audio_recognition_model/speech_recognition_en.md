---
jupyter:
  jupytext:
    text_representation:
      extension: .md
      format_name: markdown
      format_version: '1.3'
      jupytext_version: 1.14.1
  kernelspec:
    display_name: Python 3 (ipykernel)
    language: python
    name: python3
---

<!-- #region -->
# __Create a speech recognition model to dictate an audio file__

**Understanding Speech Recognition Technology**
--- 
👉 Speech recognition technology, also known as computer speech recognition or speech-to-text transformation, allows programs to process human speech in text format. Recently, it has been used in a wide range of fields, including automobiles, medical fields, and daily life using artificial intelligence speakers or smartphones. Recently, speech recognition technology using machine learning algorithms integrates grammar, syntax, structure, audio and voice signal composition to understand and process speech.


**In this tutorial**
--- 
👉 Librispeech [Panayotov et al. 2015] is one of the most widely used large-scale English speech data in speech recognition research and is the result of the user-participating audiobook project [LibriVox project](https://librivox.org/). About 1,000 hours of recorded audio book data sampled at 16 kHz was processed and created. The target table for the tutorial consists of a path and script for a pre-uploaded voice file. This tutorial aims to convert voice files into text.

__Precautions__    
> - The audio file format currently supported by ThanoSQL is ''.It's wav, '.flac'.
> - A column representing the audio file path and a column representing the text corresponding to the target must exist in the table.
> - The base model (Wav2Vec2En) of that speech recognition model uses a GPU. Depending on the size and batch size of the model used, GPU memory may be low. In this case, try using a smaller model or reducing the batch size.
<!-- #endregion -->

## __0. Preparing a dataset__


To use the query syntax of ThanoSQL, you must create an API token and run the query below, as mentioned in the [ThanoSQL Workspace](https://docs.thanosql.ai/quick_start/how_to_use_ThanoSQL/#5-thanosql).

```python
%load_ext thanosql
%thanosql API_TOKEN=<Issued_API_TOKEN>
```

```python
%%thanosql
COPY librispeech_train 
OPTIONS(overwrite =True)
FROM "tutorial_data/librispeech_data/librispeech_train.csv"
```

```python
%%thanosql
COPY librispeech_test 
OPTIONS(overwrite =True)
FROM "tutorial_data/librispeech_data/librispeech_test.csv"
```

__OPTIONS__ : 

When __overwrite is true__, the user can create a data table with the same name as the previously created data table.  
On the other hand, when __overwrite is False__, the user cannot create a data table with the same name as the previously created data table.


## __1. Check the dataset__


To proceed with this tutorial, we use the librispech_train table stored in the ThanoSQL DB. Run the query statement below to verify the contents of the table.

```python
%%thanosql
SELECT *
FROM librispeech_train
LIMIT 5
```

__Understanding data__ 
- audio : location path of the audio file
- text : Target value of the corresponding voice (Target, Script)

```python
%%thanosql
PRINT AUDIO 
AS
SELECT audio_path
FROM librispeech_train
LIMIT 3
```

## __2. Predict Speech Recognition Results Using Pretrained Models__


First, let's predict the results with the model we prepared in advance. Running the following query statement allows you to predict results using the tutorial_image_classification model, a pre-trained speech recognition model.

```python
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

<!-- #region tags=[] -->
## __3. Create a speech recognition model__
<!-- #endregion -->

Create a speech recognition model using the librispech_train dataset that you saw in the previous step. Run the query syntax below to create a model named my_speech_recognition_model.

```python
%%thanosql
BUILD MODEL my_speech_recognition_model
USING Wav2Vec2En
OPTIONS (
    audio_col='audio_path',  
    text_col='text',  
    epochs=1,  
    batch_size=4,
    overwrite= True  
    )
AS
SELECT *
FROM librispeech_train
```

__Query Details__ 
- "__BUILD MODEL__" Create and train my_speech_recognition_model using the query syntax.
- "__USING__" The query syntax specifies the use of `Wav2Vec2En` as the base model.
- "__OPTIONS__" Specifies the options used to create the model through the query syntax.
    - "audio_col" : Name of the column containing the audio path to be used for learning
    - "text_col" :  Name of column containing script information for audio
    - "epochs" : Number of times to learn all learning datasets


__OPTIONS__ : 

When __overwrite is true__, the user can create a data table with the same name as the previously created data table.  
On the other hand, when __overwrite is False__, the user cannot create a data table with the same name as the previously created data table.


## __4. Predict speech recognition results using the model you created__


Use the speech recognition model you created in the previous step to predict the target (script) of a particular speech (data table not used for learning, librispech_test). After performing the query below, the prediction results are stored in the predicated column and returned.

```python
%%thanosql
PREDICT USING my_speech_recognition_model
OPTIONS (
    audio_col='audio_path'
    )
AS
SELECT *
FROM librispeech_test
```

__Query Details__
- Use the my_speech_recognition_model created in the previous step with the query syntax "__PREDICTUSING__".
- Specify the options to use for prediction via the "__OPTIONS__" query syntax.
    - "audio_col" : Name of the column containing the audio path to be used for prediction
