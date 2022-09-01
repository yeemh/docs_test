---
title: Create a text classification model
---

# **Create a text classification model**

## Preface

- Tutorial Difficulty: ★☆☆☆☆
- 10 min read
- Languages : [SQL](https://ko.wikipedia.org/wiki/SQL) (100%)
- File location : tutorial/tutorial_en/ml/classification_model/text_classification_en.ipynb
- References : [(Kaggle) IMDB Movie Reviews](https://www.kaggle.com/code/lakshmi25npathi/sentiment-analysis-of-imdb-movie-reviews/data), [ELECTRA: Pre-training Text Encoders as Discriminators Rather Than Generators](https://arxiv.org/abs/2003.10555)
- Updated Date : {{ git_revision_date_localized }}

## Tutorial Introduction

!!! note "Understanding Classification Operations"
    Classification is a form of [Machine Learning](https://en.wikipedia.org/wiki/Machine_learning) used to predict the category (Category or Class) to which the target belongs. For example, both binary classifications that classify men or women and multiple classifications that predict animal species (dogs, cats, rabbits, etc.) are included in the classification task.

[Natural Language Processing (NLP)](https://en.wikipedia.org/wiki/Natural_language_processing) is a branch of artificial intelligence that uses machine learning to process and interpret text-based data.

!!! tip "What is [Natural Language Processing (NLP)](https://en.wikipedia.org/wiki/Natural_language_processing)"
    NLP can be classified as Natural Language Understanding (NLU) and Natural Language Generation (NLG) depending on the purpose of the task. Understanding natural language refers to the process of converting a natural language that a person understands into a value that a computer can understand. On the other hand, natural language generation further refers to the process of converting computer-readable values into natural language so that people can understand them.

Recent advances in pre-training techniques such as [BERT](<https://en.wikipedia.org/wiki/BERT_(language_model)>) and [GPT-3](https://en.wikipedia.org/wiki/GPT-3) enable the building of a common model of language comprehension before fine-tuning for certain NLP tasks, such as emotional analysis or question-and-answer.

!!! summary ""
    This means that you can use more data efficiently by minimizing [data labeling](https://en.wikipedia.org/wiki/Labeled_data) operations for large data sets.

ThanoSQL offers a variety of pre-trained artificial intelligence models, and features that make it easy for users to create their own text classification models even with a small amount of data labeling. This allows users to extract potential insights from text data that are difficult to quantify features from properly trained text classification models and utilize them in a variety of services.

**The following are examples and utilization of the ThanoSQL text classification model.**

- The ThanoSQL text classification model makes it easy to use text classification models for chatbots, inquiries, emotional analysis of text in bulletins, or categorization. This will enable the customer to connect with the appropriate person in the future.

- The ThanoSQL text classification model allows you to classify groups of published content in news or post sharing services. Emotional analysis is possible by applying a text classification model to the comments of the published content. This work enables efficient management of problems that can suddenly become an issue or be caused by abusive language or slander.

!!! note "In this tutorial"
    :point_right: Create a model to classify the emotions of movie reviews using the <mark style="background-color:#FFD79C">IMDB Movie Reviews</mark> dataset from [Kaggle](https://www.kaggle.com/)). This dataset consists of 50,000 movie review texts and targets for positive or negative emotions. Based on the movie rating, a value less than 5 is expressed as negative and a value greater than 7 is expressed as positive, and each individual film does not have more than 30 review results.

!!! warning "Tutorial Precautions" 
    - A text classification model can be used to predict one target (Target, Category) in one text. 
    - There must be a column representing the text and a column representing the target value of the text. 
    - The base model of the corresponding text classification model (`ELECTRA`) uses a GPU. Depending on the size and batch size of the model used, GPU memory may be low. In this case, try using a smaller model or reducing the batch size.

## **0. Data Set Preparation**

To use the query syntax of ThanoSQL, you must create an API token and run the query below, as mentioned in [ThanoSQL Workspace](/en/getting_started/how_to_use_ThanoSQL/#5-thanosql-workspace).

```sql
%load_ext thanosql
```

```sql
%thanosql API_TOKEN=<Issued_API_TOKEN>
```

```sql
%%thanosql
COPY movie_review_train
OPTIONS(overwrite = True)
FROM "tutorial_data/movie_review_data/movie_review_train.csv"
```

```sql
%%thanosql
COPY movie_review_test
OPTIONS(overwrite = True)
FROM "tutorial_data/movie_review_data/movie_review_test.csv"
```

!!! note "**Query Details**"
    - Specifies the data set name to copy to the DB using the "**COPY**" query syntax.
    - Specify the options to use for **COPY** via the "**OPTIONS**" query syntax.
        - "overwrite" : Set whether or not a data set with the same name can be overwritten when it exists on the DB. If True, the existing dataset is changed to the new dataset (True|False, DEFAULT: False)

## **1. Check Data Set**

To create a movie review emotion classification model, we use the <mark style="background-color:#FFEC92">movie_review_train</mark> table stored in the ThanoSQL DB. Run the query statement below to verify the contents of the table.

```sql
%%thanosql
SELECT *
FROM movie_review_train
LIMIT 5
```

[![IMAGE](/img/thanosql_ml/classification/classification_electra/train_data.png)](/img/thanosql_ml/classification/classification_electra/train_data.png)

!!! note "**Understand data**"
    - <mark style="background-color:#D7D0FF">review</mark> : movie review text
    - <mark style="background-color:#D7D0FF">sentiment</mark> : whether the review is positive (target), negative (target)

## **2. Predict movie review sentiment classification results using pre-trained models**

First, let's predict the results with the models we learned in advance. Run the following query statement to predict movie review classification results using the <mark style="background-color:#E9D7FD">tutorial_text_classification</mark> model.

```sql
%%thanosql
PREDICT USING tutorial_text_classification
OPTIONS (
    text_col='review',
    label_col='sentiment'
    )
AS
SELECT *
FROM movie_review_test
```

[![IMAGE](/img/thanosql_ml/classification/classification_electra/predict_on_test_data_1.png)](/img/thanosql_ml/classification/classification_electra/predict_on_test_data_1.png)

## **3. Creating a Text Classification Model**

Create a text classification model using the <mark style="background-color:#FFEC92">movie_review_train</mark> dataset that you saw in the previous step. Run the query syntax below to create a model named <mark style="background-color:#E9D7FD">my_movie_review_classifier</mark>.  
(Expected time required for query execution: 3 min)

```sql
%%thanosql
BUILD MODEL my_movie_review_classifier
USING ElectraEn
OPTIONS (
  text_col='review',
  label_col='sentiment',
  epochs=1,
  batch_size=4,
  overwrite=True
  )
AS
SELECT *
FROM movie_review_train
```

!!! note "Query Details"
    - Use the "**BUILD MODEL**" query syntax to create and train a model called <mark style="background-color:#E9D7FD">my_movie_review_classifier</mark>.
    - The "**USING**" query syntax specifies the use of `ElectraEn` as the base model.
    - Specify the options to use for model creation via the "**OPTIONS**" query syntax. "text_col" is the name of the column containing the text to be used for learning, and "label_col" is the name of the column containing information about the target value. Specify how many times you want to repeat with "epochs". "batch_size" is the size of a bundle of data sets read in one learning.

!!! tip ""
    For quick learning, we have designated "epochs" as 1. In general, larger numbers take more computational time, but predictive performance increases as learning progresses.

!!! note ""
    When **overwrite is true**, the user can create a data table with the same name as the previously created data table.  
    On the other hand, when **overwrite is False**, the user cannot create a data table with the same name as the previously created data table.

## **4. Predict movie review sentiment classification results using generated models**

Use the text classification prediction model you created in the previous step to predict the target value of a specific review (data table not used for learning, <mark style="background-color:#FFEC92">movie_review_test</mark>).

```sql
%%thanosql
PREDICT USING my_movie_review_classifier
OPTIONS (
    text_col='review'
    )
AS
SELECT *
FROM movie_review_test
```

[![IMAGE](/img/thanosql_ml/classification/classification_electra/predict_on_test_data_2.png)](/img/thanosql_ml/classification/classification_electra/predict_on_test_data_2.png)

!!! note "Query Details"
    Use the <mark style="background-color:#E9D7FD">my_movie_review_classifier</mark> model created in the previous step with the "**PREDICT USING**" query syntax for prediction.
    "**OPTIONS**" to specify the options to use for prediction. <mark style="background-color:#D7D0FF">review</mark> is the name of the column containing the text to be used for prediction.
    The prediction results are stored and returned in the <mark style="background-color:#D7D0FF">predicted</mark> column.

## **5. In conclusion**

In this tutorial, we created a text classification model using the <mark style="background-color:#FFD79C">IMDB Movie Reviews</mark> dataset. As it is an entry-level tutorial, we proceeded with an operation-oriented explanation rather than a process explanation for improving accuracy. Text classification models can improve accuracy through precise tuning for each platform or service. You can learn the base model using your own data, or you can digitize and convert your data using the [Self-supervised Learning](https://en.wikipedia.org/wiki/Self-supervised_learning) model and distribute it using automated machine learning (Auto-ML) techniques. Combine a variety of unstructured data (image, audio, video, etc.) with numerical data to create your own model and provide competitive services.

The next step, the Create Intermediate Text Classification Model tutorial, is to take a deeper look at the text classification model. If you want to learn more about how to build your own text classification model for my services, go ahead with the following tutorials:

- [How to Upload to ThanoSQL DB](/en/how-to_guides/ThanoSQL_connecting/data_upload/)
- [Creating an Intermediate Text Classification Model]
- [Create My model using text conversion and Auto-ML]
- [Deploy My text classification model](/en/how-to_guides/ThanoSQL_connecting/thanosql_api/rest_api_thanosql_query/)

!!! tip "**Inquiries on model distribution for 'my own' service**"
    If you have difficulty creating your own model using ThanoSQL or applying it to service, please feel free to contact me below😊

    Inquiries about building a speech recognition model: contact@smartmind.team
