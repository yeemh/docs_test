---
title: Create a Text Classification Model
---

# __Create a Text Classification Model__

- Tutorial Difficulty: ★☆☆☆☆
- 10 min read
- Languages : [SQL](https://en.wikipedia.org/wiki/SQL) (100%)
- File location : tutorial_en/thanosql_ml/classification/text_classification.ipynb
- References : [(Kaggle) IMDB Movie Reviews](https://www.kaggle.com/code/lakshmi25npathi/sentiment-analysis-of-imdb-movie-reviews/data), [ELECTRA: Pre-training Text Encoders as Discriminators Rather Than Generators](https://arxiv.org/abs/2003.10555)

## Tutorial Introduction

<div class="admonition note">
    <h4 class="admonition-title">Understanding Classification</h4>
    <p>Classification is a type of <a href="https://en.wikipedia.org/wiki/Machine_learning">Machine Learning</a> that predicts which category (Category or Class) the target belongs to. For example, both binary classifications (used for classifying men or women) and multiple classifications (used to predict animal species such as dogs, cats, rabbits, etc.) are included in the classification tasks. <br></p>
</div>

[Natural Language Processing (NLP)](https://en.wikipedia.org/wiki/Natural_language_processing) is a branch of artificial intelligence that uses machine learning to process and interpret text-based data.

<div class="admonition tip">
    <h4 class="admonition-title">What is <a href="https://en.wikipedia.org/wiki/Natural_language_processing">Natural Language Processing (NLP)</a></h4>
    <p>Depending on the task, NLP can be divided into two categories: Natural Language Understanding (NLU) and Natural Language Generation (NLG). NLU is the process of converting a person's natural language into a value that a computer can understand. NLG, on the other hand, refers to the process of translating computer-readable values into natural language that humans can understand.</p>
</div>

Recent advancements in pre-training techniques, such as [BERT](<https://en.wikipedia.org/wiki/BERT_(language_model)>) and [GPT-3](https://en.wikipedia.org/wiki/GPT-3), allow for the development of a common language comprehension model prior to fine-tuning for specific NLP tasks, such as emotional analysis or question-and-answer.

<div class="admonition summary">
    <p>This means that you can use data more efficiently by minimizing <a href="https://en.wikipedia.org/wiki/Labeled_data">data labeling</a> operations for large datasets.</p>
</div>

ThanoSQL includes a variety of pre-trained AI models along with various functions that allow users to easily create their own text classification models even with limited data labeling. Users can use this to extract potentially useful insights from text data that would otherwise be difficult and apply them to a wide range of applications.

__The following are examples and applications of the ThanoSQL text classification model.__

- The ThanoSQL text classification model makes it easy to utilize text classification models to build a chatbot and analyze sentiment of text in a bulletin board. This will later enable the customer to connect with the appropriate customer service representative.

- The ThanoSQL text classification model allows news or post sharing services to categorize their published contents into groups. Additionally, it provides sentiment analysis of grouped items, enabling effective handling of issues that could arise unexpectedly from customer frustration response.

<div class="admonition note">
    <h4 class="admonition-title">In this tutorial</h4>
    <p>👉 Create a model to classify the emotions of movie reviews using the <mark style="background-color:#FFD79C">IMDB Movie Reviews</mark> dataset from <a href="https://www.kaggle.com/">Kaggle</a>. This dataset consists of 50,000 movie review texts and each reviews are rated as either positive or negative. Based on the movie rating, a value less than 5 is expressed as negative and a value greater than 7 is expressed as positive. Each film has no more than 30 reviews.</p>
</div>

<div class="admonition warning">
    <h4 class="admonition-title">Tutorial Precautions</h4>
    <ul>
        <li>A text classification model can be used to predict one target value (Target, Category) from one text value.</li>
        <li>Both a column representing the text and a column representing the target value of the text must exist.</li>
        <li>The base model of the corresponding text classification model (<code>ELECTRA</code>) uses GPU. Depending on the size and the batch size of the model used, GPU memory may be insufficient. In this case, try using a smaller model or reducing the batch size of the model.</li>
    </ul>
</div>

## __0. Prepare Dataset and Model__

As mentioned in the [ThanoSQL Workspace](https://docs.thanosql.ai/en/getting_started/how_to_use_ThanoSQL/#5-thanosql-workspace), you must create an API token and run the query below to execute the query of ThanoSQL. 


```python
%load_ext thanosql
%thanosql API_TOKEN=<Issued_API_TOKEN>
```

### __Prepare Dataset__


```python
%%thanosql
GET THANOSQL DATASET movie_review_data
OPTIONS (overwrite=True)
```

    Success


<div class="admonition note">
    <h4 class="admonition-title">Query Details</h4>
    <ul>
        <li>"<strong>GET THANOSQL DATASET</strong>" downloads the specified dataset to the workspace. </li>
        <li>"<strong>OPTIONS</strong>" specifies the option values to be used for the <strong>GET THANOSQL DATASET</strong> clause.
        <ul>
            <li>"overwrite" : determines whether to overwrite a dataset if it already exists. If set as True, the old dataset is replaced with the new dataset (True|False, DEFAULT : False) </li>
        </ul>
        </li>
    </ul>
</div>


```python
%%thanosql
COPY movie_review_train
OPTIONS (overwrite=True) 
FROM "thanosql-dataset/movie_review_data/movie_review_train.csv"
```

    Success



```python
%%thanosql
COPY movie_review_test 
OPTIONS (overwrite=True) 
FROM "thanosql-dataset/movie_review_data/movie_review_test.csv"
```

    Success


<div class="admonition note">
    <h4 class="admonition-title">Query Details</h4>
    <ul>
        <li>"<strong>COPY</strong>" specifies the name of the dataset to be saved as a database table. </li>
        <li>"<strong>OPTIONS</strong>" specifies the option values to be used for the <strong>COPY</strong> clause.
        <ul>
           <li>"overwrite" : determines whether to overwrite a table if it already exists. If set as True, the old table is replaced with the new table (True|False, DEFAULT : False) </li>
        </ul>
        </li>
    </ul>
</div>

### __Prepare the Model__


```python
%%thanosql
GET THANOSQL MODEL tutorial_text_classification
OPTIONS (overwrite=True)
AS tutorial_text_classification
```

    Success


<div class="admonition note">
    <h4 class="admonition-title">Query Details </h4>
    <ul>
        <li>"<strong>GET THANOSQL MODEL</strong>" downloads the specified model to the workspace. </li>
        <li>"<strong>OPTIONS</strong>" specifies the option values to be used for the <strong>GET THANOSQL MODEL</strong> clause.
        <ul>
            <li>"overwrite" : determines whether to overwrite a model if it already exists. If set as True, the old model is replaced with the new model (True|False, DEFAULT : False) </li>
        </ul>
        </li>
        <li>"<strong>AS</strong>" names the given model. If not specified, the model will be named as the default <code>THANOSQL MODEL</code> name.</li>
    </ul>
</div>

## __1.Check Dataset__

To create a movie review sentiment classification model, we use the <mark style="background-color:#FFEC92 ">movie_review_train</mark> table from the ThanoSQL database. To check the table's contents, run the following query.


```python
%%thanosql
SELECT *
FROM movie_review_train
LIMIT 5
```




<div class="df_size">
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>review</th>
      <th>sentiment</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>This is the kind of movie that BEGS to be show...</td>
      <td>negative</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Bulletproof is quite clearly a disposable film...</td>
      <td>negative</td>
    </tr>
    <tr>
      <th>2</th>
      <td>A beautiful shopgirl in London is swept off he...</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>3</th>
      <td>VERY dull, obvious, tedious Exorcist rip-off f...</td>
      <td>negative</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Do we really need any more narcissistic garbag...</td>
      <td>negative</td>
    </tr>
  </tbody>
</table>
</div>



<div class="admonition note">
   <h4 class="admonition-title">Understanding the Data</h4>
   <ul>
      <li><mark style="background-color:#D7D0FF ">review</mark>: movie review in text format</li>
      <li><mark style="background-color:#D7D0FF ">sentiment</mark> : target value indicating whether the review has a positive or negative sentiment</li>
   </ul>
</div>

## __2. Predict Movie Review Sentiment Using Pretrained Model__

To predict the movie review sentiment classification result using the pretrained <mark style="background-color:#E9D7FD ">tutorial_text_classification</mark> model, run the following query.


```python
%%thanosql
PREDICT USING tutorial_text_classification
OPTIONS (
    text_col='review'
    )
AS
SELECT *
FROM movie_review_test
```




<div class="df_size">
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>review</th>
      <th>sentiment</th>
      <th>predicted</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>I read the book before seeing the movie, and t...</td>
      <td>positive</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>1</th>
      <td>"9/11," hosted by Robert DeNiro, presents foot...</td>
      <td>positive</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Yesterday I attended the world premiere of "De...</td>
      <td>positive</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Moonwalker is a Fantasy Music film staring Mic...</td>
      <td>positive</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Welcome to Oakland, where the dead come out to...</td>
      <td>positive</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>995</th>
      <td>I remember catching this movie on one of the S...</td>
      <td>negative</td>
      <td>negative</td>
    </tr>
    <tr>
      <th>996</th>
      <td>CyberTracker is set in Los Angeles sometime in...</td>
      <td>negative</td>
      <td>negative</td>
    </tr>
    <tr>
      <th>997</th>
      <td>There is so much that is wrong with this film,...</td>
      <td>negative</td>
      <td>negative</td>
    </tr>
    <tr>
      <th>998</th>
      <td>I am a firm believer that a film, TV serial or...</td>
      <td>positive</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>999</th>
      <td>I think vampire movies (usually) are wicked. E...</td>
      <td>negative</td>
      <td>negative</td>
    </tr>
  </tbody>
</table>
<p>1000 rows × 3 columns</p>
</div>



## __3. Create a Text Classification Model__

To create a text classification model with the name <mark style="background-color:#E9D7FD ">my_movie_review_classifier</mark> using the <mark style="background-color:#FFEC92 ">movie_review_train</mark> dataset, run the following query. 
(Estimated time required for query execution: 3 min)


```python
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

    Building model...
    Success


<div class="admonition note">
    <h4 class="admonition-title">Query Details</h4>
    <ul>
        <li>"<strong>BUILD MODEL</strong>" creates and trains a model named <mark style="background-color:#E9D7FD">my_movie_review_classifier</mark>.</li>
        <li>"<strong>USING</strong>" specifies <code>ElectraEn</code> as the base model.</li>
        <li>"<strong>OPTIONS</strong>" specifies the option values used to create the model.
        <ul> 
            <li> "text_col" : the name of the column containing the text to be used for the training. </li> 
            <li> "label_col" : the name of the column containing information about the target. </li> 
            <li>"epochs" : number of times to train with the training dataset.</li>
            <li> "batch_size" is the size of dataset bundle utilized in a single cycle of training.</li>
            <li>"overwrite" : determines whether to overwrite a model if it already exists. If set as True, the old model is replaced with the new model (True|False, DEFAULT : False) </li>
    </ul>
</div>

<div class="admonition tip">
    <p>In this example, we set "epochs" to 1 to train the model quickly. In general, larger number of "epochs" increases performance of the inference at the cost of the computation time.</p>
</div>

## __4. Predict Movie Review Sentiment__

To use the text classification model created in the previous step for prediction of <mark style="background-color:#FFEC92 ">movie_review_test</mark>, run the following query.


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




<div class="df_size">
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>review</th>
      <th>sentiment</th>
      <th>predicted</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>I read the book before seeing the movie, and t...</td>
      <td>positive</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>1</th>
      <td>"9/11," hosted by Robert DeNiro, presents foot...</td>
      <td>positive</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Yesterday I attended the world premiere of "De...</td>
      <td>positive</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Moonwalker is a Fantasy Music film staring Mic...</td>
      <td>positive</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Welcome to Oakland, where the dead come out to...</td>
      <td>positive</td>
      <td>negative</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>995</th>
      <td>I remember catching this movie on one of the S...</td>
      <td>negative</td>
      <td>negative</td>
    </tr>
    <tr>
      <th>996</th>
      <td>CyberTracker is set in Los Angeles sometime in...</td>
      <td>negative</td>
      <td>negative</td>
    </tr>
    <tr>
      <th>997</th>
      <td>There is so much that is wrong with this film,...</td>
      <td>negative</td>
      <td>negative</td>
    </tr>
    <tr>
      <th>998</th>
      <td>I am a firm believer that a film, TV serial or...</td>
      <td>positive</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>999</th>
      <td>I think vampire movies (usually) are wicked. E...</td>
      <td>negative</td>
      <td>negative</td>
    </tr>
  </tbody>
</table>
<p>1000 rows × 3 columns</p>
</div>



<div class="admonition note">
    <h4 class="admonition-title">Query Details</h4>
    <ul>
        <li>"<strong>PREDICT USING</strong>" predicts the outcome using the <mark style="background-color:#E9D7FD ">my_movie_review_classifier</mark>
        <li>"<strong>OPTIONS</strong>" specifies the option values to be used for prediction.
        <ul>
            <li>"review" : the column containing the text to be used for prediction.</li>
        </ul>
        </li>
    </ul>
</div>

## __5. In Conclusion__

In this tutorial, we created a text classification model using the <mark style="background-color:#FFD79C">IMDB Movie Reviews</mark> dataset. As this is a beginner-level tutorial, we focused on the process rather than accuracy. Text classification models can be improved in accuracy through fine tuning that is suitable for the user's needs. You can train the base model using your own data, or use a [Self-supervised Learning](https://en.wikipedia.org/wiki/Self-supervised_learning) model to vectorize and transform your data to create an automated machine learning (Auto-ML) for deployment. Create your own model and provide competitive services by combining various unstructured data (image, audio, video, etc.) and structured data with ThanoSQL.

For the next step, the [Creating an Intermediate Text Classification Model] tutorial takes a deeper dive into the text classification models. If you want to learn more about building your own text classification model for your service, proceed with the following tutorials.

- [How to Upload to ThanoSQL DB](https://docs.thanosql.ai/en/getting_started/data_upload/)
- [Creating an Intermediate Text Classification Model]
- [Create My model using text conversion and Auto-ML]
- [Deploy My text classification model](https://docs.thanosql.ai/en/how-to_guides/reference/)

<div class="admonition tip">
    <h4 class="admonition-title">Inquiries about deploying a model for your own service</h4>
    <p>If you have any difficulties creating your own model using ThanoSQL or applying it to your service, please feel free to contact us below😊</p>
    <p>For inquiries regarding building a text classification model: contact@smartmind.team</p>
</div>
