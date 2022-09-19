---
title: Create a Regression Model Using AutoML
---

# __Create a Regression Model Using AutoML__

- Tutorial Difficulty : ★☆☆☆☆
- 5 min read
- Languages : [SQL](https://en.wikipedia.org/wiki/SQL) (100%)
- File location : tutorial_en/thanosql_ml/regression/automl_regression.ipynb
- References : [(Kaggle) Bike Sharing Demand](https://www.kaggle.com/competitions/bike-sharing-demand/overview)

## Tutorial Introduction

<div class="admonition note">
    <h4 class="admonition-title">Understanding Regression</h4>
    <p>A regression is a type of <a href="https://en.wikipedia.org/wiki/Machine_learning">machine learning (ML)</a> that is used to predict numbers with sequential target values. For example, the model can be used to predict tomorrow's temperature or predict housing prices in a particular area.</p>
</div>

When a company spends a certain amount on advertising, sales performance data from similar past cases can be used to predict advertising performance. All <a href="https://en.wikipedia.org/wiki/Feature_(machine_learning)">Features</a> that can be converted into data, such as the features of the product to be advertised, the product selling period, information about the surrounding market, sales volume information of competitors, the definition of the target customer group, and the market trend of the industry group, can be used as input data. By changing the adjustable information in the input data, you can predict optimal sales performance and adjust the advertising cost according to the forecast performance. You can use these regression models to improve ad performance and continuously increase sales.

__The following are examples and applications of the ThanoSQL regression model.__

 - Stock price prediction using stock market price, closing price, high price, low price, related stocks, KOSPI index, related news, etc. (finance)
 - Prediction of failure probability and lifespan of equipments using sensor data such as temperature, vibration, and sound (manufacturing)
 - Prediction of solar energy generation using weather, temperature, cloudiness, insolation, etc. (energy)
 - Forecast using demand trends, oil price, and exchange rate fluctuations (raw materials) <br>

<div class="admonition note">
    <h4 class="admonition-title">In this tutorial</h4>
    <p>👉 Create a bike demand regression model using the <mark style="background-color:#FFD79C"><strong>Bike Sharing Demand</strong></mark> dataset from <a href="https://www.kaggle.com/">Kaggle</a>, a machine learning contest platform. The goals of this contest are as follows (The data for this competition is based on information such as date and time, temperature, humidity, and wind speed from 2011 to 2012.)</p>
</div>

__Predicting the number of bike rentals per hour on a specific date__

ThanoSQL provides automated machine learning (__Auto-ML__) tools. This tutorial uses Auto-ML to predict the number of bike rentals. ThanoSQL's Auto-ML automates the process for model development and enables data collection and storage along with machine learning model development and distribution (end-to-end machine learning pipelines) using a single language.

__The advantages of using ThanoSQL's automated machine learning are:__

1. Implementation and deployment of machine learning solutions without extensive programming or data science knowledge
2. Saving time and resources for deployment of development models
3. Quickly solve problems using the data you have for decision-making

Now, let's use ThanoSQL to create a simple regression model to predict the number of bike rentals on a certain data.

## __0. Prepare Dataset__

As mentioned in the [ThanoSQL Workspace](https://docs.thanosql.ai/en/getting_started/how_to_use_ThanoSQL/#5-thanosql-workspace), you must create an API token and run the query below to execute the query of ThanoSQL. 


```python
%load_ext thanosql
%thanosql API_TOKEN=<Issued_API_TOKEN>
```

### __Prepare Dataset__


```python
%%thanosql
GET THANOSQL DATASET bike_sharing_data
OPTIONS (overwrite=True)
```

    Success


<div class="admonition note">
    <h4 class="admonition-title">Query Details</h4>
    <ul>
        <li>"<strong>GET THANOSQL DATASET</strong>" downloads the specified dataset to the workspace. </li>
        <li>"<strong>OPTIONS</strong>" specifies the option values to be used for the <strong>GET THANOSQL DATASET</strong> clause.
        <ul>
            <li>"overwrite" : Determines whether to overwrite a dataset if it already exists. If set as True, the old dataset is replaced with the new dataset (True|False, DEFAULT : False) </li>
        </ul>
        </li>
    </ul>
</div>


```python
%%thanosql
COPY bike_sharing_train 
OPTIONS (overwrite=True)
FROM "thanosql-dataset/bike_sharing_data/bike_sharing_train.csv"
```

    Success



```python
%%thanosql
COPY bike_sharing_test 
OPTIONS (overwrite=True)
FROM "thanosql-dataset/bike_sharing_data/bike_sharing_test.csv"
```

    Success


<div class="admonition note">
    <h4 class="admonition-title">Query Details</h4>
    <ul>
        <li>"<strong>COPY</strong>" specifies the name of the dataset to be saved as a database table. </li>
        <li>"<strong>OPTIONS</strong>" specifies the option values to be used for the <strong>COPY</strong> clause.
        <ul>
           <li>"overwrite" : Determines whether to overwrite a table if it already exists. If set as True, the old table is replaced with the new table (True|False, DEFAULT : False) </li>
        </ul>
        </li>
    </ul>
</div>

## __1. Check Dataset__

To create the regression model, we use the <mark style="background-color:#FFEC92 ">bike_sharing_train</mark> table located in the ThanoSQL database. Run the query below to check the contents of the table.


```python
%%thanosql
SELECT * 
FROM bike_sharing_train 
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
      <th>datetime</th>
      <th>season</th>
      <th>holiday</th>
      <th>workingday</th>
      <th>weather</th>
      <th>temp</th>
      <th>atemp</th>
      <th>humidity</th>
      <th>windspeed</th>
      <th>count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2011-01-01 00:00:00</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>9.84</td>
      <td>14.395</td>
      <td>81</td>
      <td>0</td>
      <td>16</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2011-01-01 01:00:00</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>9.02</td>
      <td>13.635</td>
      <td>80</td>
      <td>0</td>
      <td>40</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2011-01-01 02:00:00</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>9.02</td>
      <td>13.635</td>
      <td>80</td>
      <td>0</td>
      <td>32</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2011-01-01 03:00:00</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>9.84</td>
      <td>14.395</td>
      <td>75</td>
      <td>0</td>
      <td>13</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2011-01-01 04:00:00</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>9.84</td>
      <td>14.395</td>
      <td>75</td>
      <td>0</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>



<div class="admonition note">
    <h4 class="admonition-title">Understanding the Data</h4>
    <p>The <mark style="background-color:#FFEC92 "><strong>bike_sharing_train</strong></mark> dataset contains information of the number of bicycle rented for an hour based on information such as date and time, temperature, humidity, and wind speed from January 2011 to December 2012.</p>
    <ul>
        <li><mark style="background-color:#D7D0FF ">datetime</mark> : date by hour</li>
        <li><mark style="background-color:#D7D0FF ">season</mark> : seasons (1=spring, 2=summer, 3=fall, 4=winter)</li>
        <li><mark style="background-color:#D7D0FF ">holiday</mark> : holidays (0 = non-holiday, 1 = national holidays, etc.)</li>
        <li><mark style="background-color:#D7D0FF ">workingday</mark> : workday (0 = weekends and holidays; 1 = weekends and non-holiday weekdays)</li>
        <li><mark style="background-color:#D7D0FF ">weather</mark> : weather</li>
        <li><mark style="background-color:#D7D0FF ">temp</mark> : temperature</li>
        <li><mark style="background-color:#D7D0FF ">atemp</mark> : sensory temperature</li>
        <li><mark style="background-color:#D7D0FF ">humidity</mark> : relative humidity</li>
        <li><mark style="background-color:#D7D0FF ">windspeed</mark> : wind speed</li>
        <li><mark style="background-color:#D7D0FF ">count</mark> : number of rentals</li>
    </ul>
</div>

## __2. Create a Regression Model__

To create a bike demand regression model with the name <mark style="background-color:#E9D7FD ">bike_regression</mark> using the <mark style="background-color:#FFEC92 "><strong>bike_sharing_train</strong></mark> dataset, run the following query. 

(Estimated time required for query execution: 8 min)


```python
%%thanosql
BUILD MODEL bike_regression
USING AutomlRegressor
OPTIONS (
    target='count', 
    impute_type='simple', 
    datetime_attribs=['datetime'],
    time_left_for_this_task=300,
    overwrite=True
    ) 
AS
SELECT *
FROM bike_sharing_train
```

    Building model...
    Success


<div class="admonition note">
    <h4 class="admonition-title">Query Details</h4>
    <ul>
        <li>"<strong>BUILD MODEL</strong>" creates and trains a model named <mark style="background-color:#E9D7FD ">bike_regression</mark>.</li>
        <li>"<strong>OPTIONS</strong>" specifies the option values used to create the model.
        <ul> 
            <li>"target" : the name of the column containing the target value of the regression model </li>
            <li>"impute_type" : determines how empty values ​​(NaNs) are handled ('simple'|'iterative' , DEFAULT: 'simple') </li>
            <li>"features_to_drop" : selects columns that cannot be used for training </li>
            <li>"time_left_for_this_task" : the total time given to find a suitable regression model (DEFAULT: 300)</li>
            <li>"overwrite" : determines whether to overwrite a model if it already exists. If set as True, the old model is replaced with the new model (True|False, DEFAULT : False) </li>
    </ul>
</div>


## __3. Evaluate the Model__

To evaluate the performance of the model created in the previous step, run the following query.


```python
%%thanosql
EVALUATE USING bike_regression 
OPTIONS (
    target='count'
    ) 
AS
SELECT *
FROM bike_sharing_train
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
      <td>MAE</td>
      <td>73.5580</td>
    </tr>
    <tr>
      <th>1</th>
      <td>MSE</td>
      <td>9688.0873</td>
    </tr>
    <tr>
      <th>2</th>
      <td>R2</td>
      <td>0.3367</td>
    </tr>
    <tr>
      <th>3</th>
      <td>RMSLE</td>
      <td>1.3192</td>
    </tr>
    <tr>
      <th>4</th>
      <td>MAPE</td>
      <td>0.4755</td>
    </tr>
  </tbody>
</table>
</div>



<div class="admonition note">
    <h4 class="admonition-title">Query Details</h4>
    <ul>
        <li>"<strong>EVALUATE USING</strong>" evaluates the <mark style="background-color:#E9D7FD ">bike_regression</mark> model. </li>
        <li>"<strong>OPTIONS</strong>" specifies the option values used to evaluate the model.
        <ul> 
            <li>"target" : the name of the column containing the target value of the regression model. </li>
        </ul>
        </li>
    </ul>
</div>

<div class="admonition warning">
    <h4 class="admonition-title">Dataset for Evaluation</h4>
    <p>Normally, train datasets should not be used for evaluation. However, for this tutorial, the train dataset is used for convenience.</p>
</div>

## __4. Predict Bike Rental Quantity__

To use the regression model created in the previous step for prediction of <mark style="background-color:#FFEC92 ">bike_sharing_test</mark>, run the following query.


```python
%%thanosql
PREDICT USING bike_regression 
AS
SELECT *
FROM bike_sharing_test
LIMIT 10
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
      <th>datetime</th>
      <th>season</th>
      <th>holiday</th>
      <th>workingday</th>
      <th>weather</th>
      <th>temp</th>
      <th>atemp</th>
      <th>humidity</th>
      <th>windspeed</th>
      <th>predicted</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2011-01-20 00:00:00</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>10.66</td>
      <td>11.365</td>
      <td>56</td>
      <td>26.0027</td>
      <td>110.261271</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2011-01-20 01:00:00</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>10.66</td>
      <td>13.635</td>
      <td>56</td>
      <td>0.0000</td>
      <td>95.456726</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2011-01-20 02:00:00</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>10.66</td>
      <td>13.635</td>
      <td>56</td>
      <td>0.0000</td>
      <td>95.456726</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2011-01-20 03:00:00</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>10.66</td>
      <td>12.880</td>
      <td>56</td>
      <td>11.0014</td>
      <td>99.237931</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2011-01-20 04:00:00</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>10.66</td>
      <td>12.880</td>
      <td>56</td>
      <td>11.0014</td>
      <td>99.237931</td>
    </tr>
    <tr>
      <th>5</th>
      <td>2011-01-20 05:00:00</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>9.84</td>
      <td>11.365</td>
      <td>60</td>
      <td>15.0013</td>
      <td>96.628108</td>
    </tr>
    <tr>
      <th>6</th>
      <td>2011-01-20 06:00:00</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>9.02</td>
      <td>10.605</td>
      <td>60</td>
      <td>15.0013</td>
      <td>87.987842</td>
    </tr>
    <tr>
      <th>7</th>
      <td>2011-01-20 07:00:00</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>9.02</td>
      <td>10.605</td>
      <td>55</td>
      <td>15.0013</td>
      <td>88.986353</td>
    </tr>
    <tr>
      <th>8</th>
      <td>2011-01-20 08:00:00</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>9.02</td>
      <td>10.605</td>
      <td>55</td>
      <td>19.0012</td>
      <td>92.677368</td>
    </tr>
    <tr>
      <th>9</th>
      <td>2011-01-20 09:00:00</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>2</td>
      <td>9.84</td>
      <td>11.365</td>
      <td>52</td>
      <td>15.0013</td>
      <td>114.897041</td>
    </tr>
  </tbody>
</table>
</div>



<div class="admonition note">
    <h4 class="admonition-title">Query Details</h4>
    <ul>
        <li>"<strong>PREDICT USING</strong>" predicts the outcome using the <mark style="background-color:#E9D7FD ">bike_regression</mark>
    </ul>
</div>

## __5. In Conclusion__

In this tutorial, we created a bicycle demand regression model using the <mark style="background-color:#FFD79C">Bike Sharing Demand</mark> dataset from [Kaggle](https://www.kaggle.com). As this is a beginner-level tutorial, we focused on the process rather than accuracy. If you'd like to learn more about building advanced regression models, you should check out our intermediate tutorials. 

For the next step, the [Creating an Intermediate Regression Model] tutorial takes a deeper dive into the "__OPTIONS__" clause to improve accuracy. For the intermediate tutorial, we will create sophisticated regression models using the various "__OPTIONS__" provided by ThanoSQL's AutoML. In the advanced level, you will have the chance to vectorize unstructured data and include it as a train element in AutoML to create an even more sophisticated regression model.

- [How to Upload to ThanoSQL DB](https://docs.thanosql.ai/en/getting_started/data_upload/)
- [Create an intermediate image classification model]
- [Image conversion and creating My model using Auto-ML]
- [Deploying My image classification model](https://docs.thanosql.ai/en/how-to_guides/reference/)

<div class="admonition tip">
    <h4 class="admonition-title">Inquiries about deploying a model for your own service</h4>
    <p>If you have any difficulties creating your own model using ThanoSQL or applying it to your service, please feel free to contact us below😊</p>
    <p>For inquiries regarding building a regression model: contact@smartmind.team</p>
</div>
