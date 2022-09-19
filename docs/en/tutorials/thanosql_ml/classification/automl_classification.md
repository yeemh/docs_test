---
title: Create a Classification Model Using AutoML
---

# __Create a Classification Model Using AutoML__

- Tutorial difficulty : ★☆☆☆☆
- 4 min read
- Languages : [SQL](https://en.wikipedia.org/wiki/SQL) (100%)
- File location : tutorial_en/thanosql_ml/classification/automl_classification.ipynb
- References : [(Kaggle) Titanic - Machine Learning from Disaster](https://www.kaggle.com/competitions/titanic/overview)

## Tutorial Introduction

<div class="admonition note">
    <h4 class="admonition-title">Understanding Classification</h4>
    <p>Classification is a type of <a href="https://en.wikipedia.org/wiki/Machine_learning">Machine Learning</a> that predicts which category (Category or Class) the target belongs to. For example, both binary classifications (used for classifying men or women) and multiple classifications (used to predict animal species such as dogs, cats, rabbits, etc.) are included in the classification tasks. <br></p>
</div>

To predict whether or not a potential customer will react positively to a particular marketing promotion in your company, you can use your customer's [Customer Relationship Management (CRM)](https://en.wikipedia.org/wiki/Customer_relationship_management) data (demographic information, customer behavior/search data, etc.). In this case, the <a href="https://en.wikipedia.org/wiki/Feature_(machine_learning)">features</a> expressed in the CRM data are used as the input data, and the target value, which is the value to be predicted, is whether the target customer's response to the promotion is positive (1 or True) or negative (0 or False). By using this classification model, you can predict the reaction of customers who have not been exposed to advertisements and target the appropriate customers, thereby continuously increasing marketing efficiency.

__The following are examples and applications of the ThanoSQL classification model.__

- The classification model enables early detection of current user deviations and allows proactive response to problems (deviations). Collected data can help you identify the features of leaving customers and allow you to take appropriate action by discovering leaving customers in advance. This can help prevent customer defections and increase sales.

- You can predict the [Market Segmentation](https://en.wikipedia.org/wiki/Market_segmentation) involved in your online platform. Most service users have different characteristics, behaviors, and needs. Classification models utilize the users' features to identify granular groups and enable them to develop strategies tailored to them.  

<div class="admonition note">
    <h4 class="admonition-title">In this tutorial</h4>
    <p>👉 Create a classification model for survivors using the <mark style="background-color:#FFD79C"> <strong>Titanic: Machine Learning from Disaster</strong></mark> dataset from the machine learning contest platform <a href="https://www.kaggle.com/">Kaggle</a>. The goals of this competition are as follows:
    (For reference, the data for the event is a list of real passengers who were on board during the Titanic incident on April 15, 1912.)</p>
</div>

__Predicting Passengers Who Would Survive The Titanic Incident__

ThanoSQL provides automated machine learning (__Auto-ML__) tools. This tutorial uses Auto-ML to predict passengers who would survive the Titanic incident. ThanoSQL's Auto-ML automates the process for model development and enables data collection and storage along with machine learning model development and distribution (end-to-end machine learning pipelines) using a single language.

__Automated ML has the following advantages:__

1. Implementation and deployment of machine learning solutions without extensive programming or data science knowledge
2. Saving time and resources for deployment of development models
3. Quickly solve problems using the data you have for decision-making

Now let's use ThanoSQL to create a classification model that predicts passengers who would survive the Titanic incident.

## __0. Prepare Dataset__

As mentioned in the [ThanoSQL Workspace](https://docs.thanosql.ai/en/getting_started/how_to_use_ThanoSQL/#5-thanosql-workspace), you must create an API token and run the query below to execute the query of ThanoSQL. 


```python
%load_ext thanosql
%thanosql API_TOKEN=<Issued_API_TOKEN>
```

### __Prepare Dataset__


```python
%%thanosql
GET THANOSQL DATASET titanic_data
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
COPY titanic_train 
OPTIONS (overwrite=True)
FROM "tutorial_data/titanic_data/titanic_train.csv"
```

    Success



```python
%%thanosql
COPY titanic_test 
OPTIONS (overwrite=True)
FROM "thanosql-dataset/titanic_data/titanic_test.csv"
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

## __1. Check Dataset__

To create the survivor classification model, we use the <mark style="background-color:#FFEC92 "><strong>titanic_train</strong></mark> table located in the ThanoSQL database. Run the query below to check the contents of the table.


```python
%%thanosql
SELECT * 
FROM titanic_train
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
      <th>passengerid</th>
      <th>survived</th>
      <th>pclass</th>
      <th>name</th>
      <th>sex</th>
      <th>age</th>
      <th>sibsp</th>
      <th>parch</th>
      <th>ticket</th>
      <th>fare</th>
      <th>cabin</th>
      <th>embarked</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>0</td>
      <td>3</td>
      <td>Braund, Mr. Owen Harris</td>
      <td>male</td>
      <td>22</td>
      <td>1</td>
      <td>0</td>
      <td>A/5 21171</td>
      <td>7.2500</td>
      <td>None</td>
      <td>S</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>1</td>
      <td>1</td>
      <td>Cumings, Mrs. John Bradley (Florence Briggs Th...</td>
      <td>female</td>
      <td>38</td>
      <td>1</td>
      <td>0</td>
      <td>PC 17599</td>
      <td>71.2833</td>
      <td>C85</td>
      <td>C</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>1</td>
      <td>3</td>
      <td>Heikkinen, Miss. Laina</td>
      <td>female</td>
      <td>26</td>
      <td>0</td>
      <td>0</td>
      <td>STON/O2. 3101282</td>
      <td>7.9250</td>
      <td>None</td>
      <td>S</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>1</td>
      <td>1</td>
      <td>Futrelle, Mrs. Jacques Heath (Lily May Peel)</td>
      <td>female</td>
      <td>35</td>
      <td>1</td>
      <td>0</td>
      <td>113803</td>
      <td>53.1000</td>
      <td>C123</td>
      <td>S</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>0</td>
      <td>3</td>
      <td>Allen, Mr. William Henry</td>
      <td>male</td>
      <td>35</td>
      <td>0</td>
      <td>0</td>
      <td>373450</td>
      <td>8.0500</td>
      <td>None</td>
      <td>S</td>
    </tr>
  </tbody>
</table>
</div>



<div class="admonition note">
    <h4 class="admonition-title">Understanding the Data</h4>
    <p>The <mark style="background-color:#FFEC92 "><strong>tianic_train</strong></mark> dataset contains the following columns.</p>
    <ul>
        <li><mark style="background-color:#D7D0FF">passengerid</mark> : passenger ID</li>
        <li><mark style="background-color:#D7D0FF">survived</mark> : whether the passenger on board survived</li>
        <li><mark style="background-color:#D7D0FF">pclass</mark> : passenger ticket class</li>
        <li><mark style="background-color:#D7D0FF">name</mark> : passenger name</li>
        <li><mark style="background-color:#D7D0FF">sex</mark> : passenger gender</li>
        <li><mark style="background-color:#D7D0FF">age</mark> : passenger age</li>
        <li><mark style="background-color:#D7D0FF">sibsp</mark> : number of siblings or spouses on board</li>
        <li><mark style="background-color:#D7D0FF">parch</mark> : number of parents or children on board</li>
        <li><mark style="background-color:#D7D0FF">ticket</mark> : ticket number</li>
        <li><mark style="background-color:#D7D0FF">fare</mark> : fare</li>
        <li><mark style="background-color:#D7D0FF">cabin</mark> : cabin number</li>
        <li><mark style="background-color:#D7D0FF">embarked</mark> : boarding location or port</li>
    </ul>
</div>

In this tutorial, we will exclude the <mark style="background-color:#D7D0FF">name</mark>, <mark style="background-color:#D7D0FF">ticket</mark>, and <mark style="background-color:#D7D0FF">cabin</mark> columns since they require additional data preprocessing.

## __2. Create a Classification Model__

To create a survivor classification model with the name <mark style="background-color:#E9D7FD ">titanic_automl_classification</mark> using the <mark style="background-color:#FFEC92 ">titanic_train</mark> dataset, run the following query. 
(Estimated duration of query execution: 8 min)


```python
%%thanosql
BUILD MODEL titanic_automl_classification
USING AutomlClassifier 
OPTIONS (
    target='survived', 
    impute_type='iterative',  
    features_to_drop=['name', 'ticket', 'passengerid', 'cabin'],
    time_left_for_this_task=300,
    overwrite=True
    ) 
AS 
SELECT * 
FROM titanic_train
```

    Building model...
    Success


<div class="admonition note">
    <h4 class="admonition-title">Query Details</h4>
    <ul>
        <li>"<strong>BUILD MODEL</strong>" creates and trains a model named <mark style="background-color:#E9D7FD ">titanic_automl_classification</mark>.</li>
        <li>"<strong>OPTIONS</strong>" specifies the option values used to create the model.
        <ul> 
            <li>"target" : the name of the column containing the target value of the classification model </li>
            <li>"impute_type" : determines how empty values ​​(NaNs) are handled ('simple'|'iterative' , DEFAULT: 'simple') </li>
            <li>"features_to_drop" : selects columns that cannot be used for training </li>
            <li>"time_left_for_this_task" : the total time given to find a suitable classification model (DEFAULT: 300)</li>
            <li>"overwrite" : determines whether to overwrite a model if it already exists. If set as True, the old model is replaced with the new model (True|False, DEFAULT : False) </li>
    </ul>
</div>


## __3. Evaluate the Model__

To evaluate the performance of the model created in the previous step, run the following query.


```python
%%thanosql 
EVALUATE USING titanic_automl_classification 
OPTIONS (
    target = 'survived'
    )
AS
SELECT *
FROM titanic_train
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
      <th>metrics</th>
      <th>value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>accuracy</td>
      <td>0.895623</td>
    </tr>
    <tr>
      <th>1</th>
      <td>ROCAUC</td>
      <td>0.896712</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Recall</td>
      <td>0.900322</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Precision</td>
      <td>0.818713</td>
    </tr>
    <tr>
      <th>4</th>
      <td>f1-score</td>
      <td>0.857580</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Kappa</td>
      <td>0.775499</td>
    </tr>
    <tr>
      <th>6</th>
      <td>MCC</td>
      <td>0.777680</td>
    </tr>
  </tbody>
</table>
</div>



<div class="admonition note">
    <h4 class="admonition-title">Query Details</h4>
    <ul>
        <li>"<strong>EVALUATE USING</strong>" evaluates the <mark style="background-color:#E9D7FD ">titanic_automl_classification</mark> model. </li>
        <li>"<strong>OPTIONS</strong>" specifies the option values used to evaluate the model.
        <ul> 
            <li>"target" : the name of the column containing the target value of the classification model. </li>
        </ul>
        </li>
    </ul>
</div>

<div class="admonition warning">
    <h4 class="admonition-title">Dataset for Evaluation</h4>
    <p>Normally, train datasets should not be used for evaluation. However, for this tutorial, the train dataset is used for convenience.</p>
</div>

## __4. Predict Survivors__

To use the classification model created in the previous step for prediction of <mark style="background-color:#FFEC92 "><strong>titanic_test</strong></mark>, run the following query.


```python
%%thanosql 
PREDICT USING titanic_automl_classification
AS 
SELECT * 
FROM titanic_test
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
      <th>passengerid</th>
      <th>pclass</th>
      <th>name</th>
      <th>sex</th>
      <th>age</th>
      <th>sibsp</th>
      <th>parch</th>
      <th>ticket</th>
      <th>fare</th>
      <th>cabin</th>
      <th>embarked</th>
      <th>predicted</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>892</td>
      <td>3</td>
      <td>Kelly, Mr. James</td>
      <td>male</td>
      <td>34.5</td>
      <td>0</td>
      <td>0</td>
      <td>330911</td>
      <td>7.8292</td>
      <td>None</td>
      <td>Q</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>893</td>
      <td>3</td>
      <td>Wilkes, Mrs. James (Ellen Needs)</td>
      <td>female</td>
      <td>47.0</td>
      <td>1</td>
      <td>0</td>
      <td>363272</td>
      <td>7.0000</td>
      <td>None</td>
      <td>S</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>894</td>
      <td>2</td>
      <td>Myles, Mr. Thomas Francis</td>
      <td>male</td>
      <td>62.0</td>
      <td>0</td>
      <td>0</td>
      <td>240276</td>
      <td>9.6875</td>
      <td>None</td>
      <td>Q</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>895</td>
      <td>3</td>
      <td>Wirz, Mr. Albert</td>
      <td>male</td>
      <td>27.0</td>
      <td>0</td>
      <td>0</td>
      <td>315154</td>
      <td>8.6625</td>
      <td>None</td>
      <td>S</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>896</td>
      <td>3</td>
      <td>Hirvonen, Mrs. Alexander (Helga E Lindqvist)</td>
      <td>female</td>
      <td>22.0</td>
      <td>1</td>
      <td>1</td>
      <td>3101298</td>
      <td>12.2875</td>
      <td>None</td>
      <td>S</td>
      <td>0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>413</th>
      <td>1305</td>
      <td>3</td>
      <td>Spector, Mr. Woolf</td>
      <td>male</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>A.5. 3236</td>
      <td>8.0500</td>
      <td>None</td>
      <td>S</td>
      <td>0</td>
    </tr>
    <tr>
      <th>414</th>
      <td>1306</td>
      <td>1</td>
      <td>Oliva y Ocana, Dona. Fermina</td>
      <td>female</td>
      <td>39.0</td>
      <td>0</td>
      <td>0</td>
      <td>PC 17758</td>
      <td>108.9000</td>
      <td>C105</td>
      <td>C</td>
      <td>1</td>
    </tr>
    <tr>
      <th>415</th>
      <td>1307</td>
      <td>3</td>
      <td>Saether, Mr. Simon Sivertsen</td>
      <td>male</td>
      <td>38.5</td>
      <td>0</td>
      <td>0</td>
      <td>SOTON/O.Q. 3101262</td>
      <td>7.2500</td>
      <td>None</td>
      <td>S</td>
      <td>0</td>
    </tr>
    <tr>
      <th>416</th>
      <td>1308</td>
      <td>3</td>
      <td>Ware, Mr. Frederick</td>
      <td>male</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>359309</td>
      <td>8.0500</td>
      <td>None</td>
      <td>S</td>
      <td>0</td>
    </tr>
    <tr>
      <th>417</th>
      <td>1309</td>
      <td>3</td>
      <td>Peter, Master. Michael J</td>
      <td>male</td>
      <td>NaN</td>
      <td>1</td>
      <td>1</td>
      <td>2668</td>
      <td>22.3583</td>
      <td>None</td>
      <td>C</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
<p>418 rows × 12 columns</p>
</div>



<div class="admonition note">
    <h4 class="admonition-title">Query Details</h4>
    <ul>
        <li>"<strong>PREDICT USING</strong>" predicts the outcome using the <mark style="background-color:#E9D7FD ">titanic_automl_classification</mark>
    </ul>
</div>

## __5. In Conclusion__

In this tutorial, we created a Titanic survivor classification model using the <mark style="background-color:#FFD79C"><strong>Titanic: Machine Learning from Disaster</strong></mark> dataset from [Kaggle](https://www.kaggle.com/). As this is a beginner-level tutorial, we focused on the process rather than accuracy. If you'd like to learn more about building advanced classification models, you should check out our intermediate tutorials. 

For the next step, the [Creating an Intermediate Classification Model] tutorial takes a deeper dive into the "__OPTIONS__" clause to improve accuracy. For the intermediate tutorial, we will create sophisticated classification models using the various "__OPTIONS__" provided by ThanoSQL's AutoML. In the advanced level, you will have the chance to vectorize unstructured data and include it as a train element in AutoML to create an even more sophisticated classification model.

- [How to Upload to ThanoSQL DB](https://docs.thanosql.ai/en/getting_started/data_upload/)
- [Creating an Intermediate Image Classification Model]
- [Image conversion and creating My model using Auto-ML]
- [Deploy My Image Classification model](https://docs.thanosql.ai/en/how-to_guides/reference/)

<div class="admonition tip">
    <h4 class="admonition-title">Inquiries about deploying a model for your own service</h4>
    <p>If you have any difficulties creating your own model using ThanoSQL or applying it to your service, please feel free to contact us below😊</p>
    <p>For inquiries regarding building a classification model: contact@smartmind.team</p>
</div>
