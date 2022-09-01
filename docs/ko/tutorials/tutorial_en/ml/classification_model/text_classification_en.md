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

# __Create a text classification model__

**Understanding the classification** 
--- 
👉 Classification is a form of machine learning used to predict the category (or class) to which the target belongs. For example, both binary classifications that classify men or women and multiple classifications that predict animal species (dogs, cats, rabbits, etc.) are included in the classification task.

**NLP (Natural Language Processing)** 
--- 
NLP can be classified as natural language understanding and natural language generation depending on the purpose of the operation. Understanding natural language refers to the process of converting a natural language that a person understands into a value that a computer can understand. On the other hand, natural language generation further refers to the process of converting computer-readable values into natural language so that people can understand them.

**In this tutorial**
--- 
👉 Create a model that categorizes the emotions of movie reviews using Kaggle's IMDB Movie Reviews dataset, a representative machine learning competition platform. This dataset consists of 50,000 movie review text and a target for positive or negative emotions. Based on the movie rating, a value less than 5 is expressed as negative and a value greater than 7 is expressed as positive, and each individual film does not have more than 30 review results.

__Tutorial Precautions__    
> - The text classification model can be used to predict one target in one text.
> - There must be a column representing the text and a column representing the target value of the text.
> - The base model of the corresponding text classification model (`ELECTRA`) uses a GPU. Depending on the size and batch size of the model used, GPU memory may be low. In this case, try using a smaller model or reducing the batch size.


## __0. Preparing a data set__


To use the query syntax of ThanoSQL, you must create an API token and run the query below, as mentioned in the [ThanoSQL Workspace](https://docs.thanosql.ai/quick_start/how_to_use_ThanoSQL/#5-thanosql).

```python
%load_ext thanosql
%thanosql API_TOKEN=<Issued_API_TOKEN>
```

```python
%%thanosql
COPY movie_review_train
OPTIONS (overwrite=True) 
FROM "tutorial_data/movie_review_data/movie_review_train.csv"
```

```python
%%thanosql
COPY movie_review_test 
OPTIONS (overwrite=True) 
FROM "tutorial_data/movie_review_data/movie_review_test.csv"
```

__OPTIONS__ : 

When __overwrite is true__, the user can create a data table with the same name as the previously created data table.  
On the other hand, when __overwrite is False__, the user cannot create a data table with the same name as the previously created data table.


## __1.Check Data Set__

To create a movie review emotion classification model, we use the movie_review_train table stored in the ThanoSQL DB. Run the query statement below to verify the contents of the table.

```python
%%thanosql
SELECT *
FROM movie_review_train
LIMIT 5
```

__Understanding data__ 
- review : movie review text
- sentiment : Target indicating whether the review is positive or negative


## __2. Predict Movie Review Sentiment Classification Results Using Pretrained Models__

First, let's predict the results with the models we learned in advance. Run the following query statement to use the tutorial_text_classification model to predict movie review classification results.

```python
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

## __3. Create a text classification model__

Create a text classification model using the movie_review_train dataset that you saw in the previous step. Run the query syntax below to create a model named my_movie_review_classifier.

(Expected time required for query execution: 3 min)

```python
%%thanosql
BUILD MODEL my_movie_review_classifier
USING ElectraEn
OPTIONS (
    text_col='review',
    label_col='sentiment',
    epochs=1,
    batch_size=4,
    overwrite= True
    )
AS
SELECT *
FROM movie_review_train
```

__Query Details__
- "__BUILD MODEL__" Use the query syntax to create and learn a model called <mark style="background-color:#E9D7FD">my_movie_review_classifier</mark>.
- "__USING__" The query syntax specifies the use of `ElectraEn` as the base model.
- "__OPTIONS__" Specifies the options used to create the model through the query syntax. "text_col" is the name of the column containing the text to be used for learning, and "label_col" is the name of the column containing information about the target. Specify how many times you want to repeat with "epochs". "batch_size" is the size of a bundle of data sets read in one learning.

__overwrite is True__, users can create a data table with the same name as the previously created data table.
__overwrite is False__, user cannot create a data table with the same name as the previously created data table.


## __4. Predict Movie Review Sentiment Classification Results Using Generated Model__

Use the text classification prediction model you created in the previous step to predict the target value of a specific review (data table not used for learning, movie_review_test).

```python
%%thanosql
PREDICT USING my_movie_review_classifier
OPTIONS (
    text_col='review'
    )
AS
SELECT *
FROM movie_review_test
```

__Query Details__
- Use the "PREDICT USING" query syntax to predict the my_movie_review_classifier model created in the previous step.
- Specify the options to use for prediction through "OPTIONS". review is the name of the column containing the text to be used for the prediction. The prediction results are stored in the predicated column and returned.
