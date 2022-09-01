---
title: Create a regression model using AutoML
---

# **Create a regression model using AutoML**

## Preface

- Tutorial Difficulty : ★☆☆☆☆
- 5 min read
- Languages : [SQL](https://ko.wikipedia.org/wiki/SQL) (100%)
- File location : tutorial/tutorial_en/ml/regression_model/automl_regression_model_en.ipynb
- References : [(Kaggle) Bike Sharing Demand](https://www.kaggle.com/competitions/bike-sharing-demand/overview)
- Updated Date : {{ git_revision_date_localized }}

## Tutorial Introduction

!!! note "Understanding Regression Operations"
    A regression operation is a form of [machine learning (ML)](https://en.wikipedia.org/wiki/Machine_learning) that is used to predict numbers whose target has continuity. For example, given weather data, it can be used to predict tomorrow's temperature, or to predict housing prices in a particular area.

When a company uses a certain amount of money for advertising, it can use similar past sales performance data to predict the performance of advertising. From the characteristics of the product you want to advertise to when you sell the product, to the surrounding market information, competitor sales information, definition of target customer groups, and industry market trends, all dataable [Features](<https://en.wikipedia.org/wiki/Feature_(machine_learning)>) can be input. By changing controllable information from input data, you can predict optimal sales performance and adjust the cost of advertising based on predictive performance. This regression model can be used to improve advertising performance and increase sales continuously.

**Below are examples and utilization of the ThanoSQL regression model.**

- Stock price prediction using stock market price, closing price, high price, low price, related stock price, comprehensive stock index, related news, etc. (finance)
- Prediction of failure probability and remaining life using sensor data such as temperature, vibration, and sound of equipment/equipment (manufacturing)
- Prediction of solar energy generation using weather, temperature, cloudiness, insolation, etc. (energy)
- Demand forecasting using historical demand trends, oil prices and exchange rate fluctuations (raw materials) <br>

!!! note "In this tutorial"
    :point_right: Create a regression model for bike demand forecasting using the <mark style="background-color:#FFD79C">**Bike Sharing Demand**</mark> dataset for beginners at [Kaggle](https://www.kaggle.com/), a leading machine learning competition platform. The objectives of the competition are as follows (FYI, the data for the competition are based on the date and time, temperature, humidity and wind speed from 2011 to 2012)

**Predict the number of bicycles rented by time zone on a specific date**

ThanoSQL provides automated machine learning (**Auto-ML**) as a tool. This tutorial uses Auto-ML to estimate the number of bicycles rented. Auto-ML from ThanoSQL automates the process for model development and enables data collection and storage, machine learning model development and distribution (end-to-end machine learning pipeline) in just one language (**ThanoSQL**) without data science expertise.

**Using ThanoSQL's automated machine learning has the following advantages.**

1. Implementation and deployment of machine learning solutions without extensive programming or data science knowledge
2. Save time and resources for deployment of development models
3. It is possible to quickly solve problems using the data you have for decision-making

Now let's create a regression model that uses ThanoSQL to simply predict bicycle rental volumes.

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
COPY bike_sharing_train
OPTIONS(overwrite = True)
FROM "tutorial_data/bike_sharing_data/bike_sharing_train.csv"
```

```sql
%%thanosql
COPY bike_sharing_test
OPTIONS(overwrite = True)
FROM "tutorial_data/bike_sharing_data/bike_sharing_test.csv"
```

!!! note "**Query Details**"
    - Specifies the data set name to copy to the DB using the "**COPY**" query syntax.
    - Specify the options to use for **COPY** via the "**OPTIONS**" query syntax.
        - "overwrite" : Set whether or not a data set with the same name can be overwritten when it exists on the DB. If True, the existing dataset is changed to the new dataset (True|False, DEFAULT: False)

## **1. Check Data Set**

To proceed with this tutorial, we use the <mark style="background-color:#FFEC92">bike_sharing_train</mark> table stored in the ThanoSQL DB. Run the query statement below to verify the contents of the table.

```sql
%%thanosql
SELECT *
FROM bike_sharing_train
LIMIT 5
```

[![IMAGE](/img/thanosql_ml/regression/img1.png)](/img/thanosql_ml/regression/img1.png)

!!! note "**Understanding Data**"
    <mark style="background-color:#FFEC92 ">**bike_sharing_train**</mark> The dataset contains information on the number of bicycle rental cycles during the one-hour interval based on date and time, temperature, humidity, and wind speed from January 2011 to December 2012.

    - <mark style="background-color:#D7D0FF ">datetime</mark> : Date by hour
    - <mark style="background-color:#D7D0FF ">season</mark> : Seasons (1=spring, 2=summer, 3=fall, 4=winter)
    - <mark style="background-color:#D7D0FF ">holiday</mark> : Holidays (0 = non-holiday, 1 = national holidays, etc.)
    - <mark style="background-color:#D7D0FF ">workingday</mark> : Workday (0 = weekends and holidays; 1 = weekends and non-holiday weekdays)
    - <mark style="background-color:#D7D0FF ">weather</mark> : Weather
    - <mark style="background-color:#D7D0FF ">temp</mark> : Temperature
    - <mark style="background-color:#D7D0FF ">atemp</mark> : Sensory temperature
    - <mark style="background-color:#D7D0FF ">humidity</mark> : Relative humidity
    - <mark style="background-color:#D7D0FF ">windspeed</mark> : Wind speed
    - <mark style="background-color:#D7D0FF ">count</mark> : number of rentals

## **2. Create a regression model**

Use the <mark style="background-color:#FFEC92">**bike_sharing_train**</mark> data set that you saw in the previous step to create a regression model for forecasting bicycle demand. Run the query syntax below to create a model named <mark style="background-color:#E9D7FD">bike_regression</mark>.  
(Expected time required for query execution: 8 min)

```sql
%%thanosql
BUILD MODEL bike_regression
USING AutomlRegressor
OPTIONS (
 target='count',
 impute_type='simple',
 datetime_attribs=['datetime'],
 time_left_for_this_task = 300,
 overwrite=True
 )
AS
SELECT *
FROM bike_sharing_train
```

!!! note "**Query Details**"
    - Create and train a model called <mark style="background-color:#E9D7FD">bike_regression</mark> using the query syntax "**BUILD MODEL**".
    - Specify the options to use for model creation via the "**OPTIONS**" query syntax.
        - "datetime_attribs" : A list of column names containing data in date format
        - "time_left_for_this_task" : Time taken to find a suitable regression prediction model (DEFAULT : 300)
        - "overwrite" : If a model with the same name exists, it can be overwritten. If True, the old model is replaced with the new model. (True|False, DEFAULT : False)

!!! warning
    When generating the Auto-ML regression prediction model, if you use any parameter other than the one specified in [OPTIONS](/en/how-to_guides/OPTIONS/#2-automlregressor-algorithm), the model can be generated, but all the values set are ignored.

## **3. Evaluating Generated Models**

Run the query statement below to evaluate the performance of the prediction model you created in the previous step.

```sql
%%thanosql
EVALUATE USING bike_regression
OPTIONS (
  target='count'
  )
AS
SELECT *
FROM bike_sharing_train
```

[![IMAGE](/img/thanosql_ml/regression/img2.png)](/img/thanosql_ml/regression/img2.png)

!!! note "**Query Details**" 
    - Evaluate the <mark style="background-color:#E9D7FD">bike_regression</mark> model built using the "**EVALUATE USING**" query syntax.
    - Use the "**OPTIONS**" query syntax to specify the options to use for the evaluation.
        - "target" : The column name containing the target value of the regression prediction model

## **4. Predict bicycle rental quantity using the generated model**

Use the demand prediction model you created in the previous step to predict the number of bicycle rentals for 10 data in the test dataset (data table not used for learning, <mark style="background-color:#FFEC92">bike_sharing_test</mark>).

```sql
%%thanosql
PREDICT USING bike_regression
AS
SELECT *
FROM bike_sharing_test
LIMIT 10
```

[![IMAGE](/img/thanosql_ml/regression/img3.png)](/img/thanosql_ml/regression/img3.png)
!!! note "__Query Details__"  
    - Use the "__PREDICT USING__" query syntax to use the <mark style="background-color:#E9D7FD">bike_regression</mark> model for prediction. 
    - For "__PREDICT__", you do not need any special option values because you follow the procedure of the generated model.

## **5. In Conclusion**

In this tutorial, we used the <mark style="background-color:#FFD79C">Bike Sharing Demand</mark> dataset from [Kaggle](https://www.kaggle.com) to create a regression model for forecasting demand for bicycles. As it is an entry-level tutorial, we explained the overall process rather than the process for improving accuracy. If you want to learn more about building an enhanced regression model, we recommend that you proceed with an intermediate tutorial.

In the next [Create Intermediate Regression Task Model] tutorial, we'll discuss "**OPTIONS**" to improve accuracy. Complete the intermediate and advanced phases and create a regression prediction model for your own service/product. In the intermediate phase, we will leverage the various "**OPTIONS**" provided by Auto-ML of ThanoSQL to create sophisticated regression prediction models. In addition, after completing the intermediate stage, the advanced stage can quantify unstructured data and include it as a learning element in Auto-ML to create a regression prediction model.

- [How to Upload to ThanoSQL DB](/en/how-to_guides/ThanoSQL_connecting/data_upload/)
- [Creating an Intermediate Image Classification Model]
- [Image conversion and creating My model using Auto-ML]
- [Deploy My image classification model](/en/how-to_guides/ThanoSQL_connecting/thanosql_api/rest_api_thanosql_query/)

!!! tip "**Inquiries on model distribution for 'my own' service**"
    If you have difficulty creating your own model using ThanoSQL or applying it to service, please feel free to contact me below😊

    Inquiries about building a speech recognition model: contact@smartmind.team
