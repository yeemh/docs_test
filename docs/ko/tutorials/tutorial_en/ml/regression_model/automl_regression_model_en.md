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

# __Create a regression model using AutoML__

<!-- #region -->
**Understanding Regression Tasks**
--- 
👉 A regression task is a form of machine learning that is used to predict numbers whose targets are continuous. For example, given weather data, it can be used to predict tomorrow's temperature or to predict house prices in a specific area.


**In this tutorial**
--- 
👉 Create a regression model for predicting bicycle demand using the Bike Sharing Demand dataset for beginners of Kaggle, a leading machine learning competition platform. The objectives of the competition are as follows (FYI, the data for the competition are based on the date and time, temperature, humidity and wind speed from 2011 to 2012)

- Predict the number of bike rentals per hour on a specific date
<!-- #endregion -->

## __0. Preparing a dataset__


To use the query syntax of ThanoSQL, you must create an API token and run the query below, as mentioned in the [ThanoSQL Workspace](https://docs.thanosql.ai/quick_start/how_to_use_ThanoSQL/#5-thanosql).

```python tags=[]
%load_ext thanosql
%thanosql API_TOKEN=<Issued_API_TOKEN>
```

```python tags=[]
%%thanosql
COPY bike_sharing_train 
OPTIONS(overwrite= True)
FROM "tutorial_data/bike_sharing_data/bike_sharing_train.csv"
```

```python tags=[]
%%thanosql
COPY bike_sharing_test 
OPTIONS(overwrite = True)
FROM "tutorial_data/bike_sharing_data/bike_sharing_test.csv"
```

__OPTIONS__ : 

When __overwrite is true__, the user can create a data table with the same name as the previously created data table.  
On the other hand, when __overwrite is False__, the user cannot create a data table with the same name as the previously created data table.


## __1. Check the dataset__


To proceed with this tutorial, we use the bike_sharing_train table stored in the ThanoSQL DB. Run the query statement below to verify the contents of the table.

```python tags=[]
%%thanosql
SELECT * 
FROM bike_sharing_train 
LIMIT 5
```

__Understanding data__ 

__bike_sharing_train__ dataset contains information on the number of bicycle leases during the 1-hour interval based on date and time, temperature, humidity and wind speed from January 2011 to December 2012.
-  datetime : Date by hour  
-  season : Seasons (1=spring, 2=summer, 3=fall, 4=winter)
-  holiday : Holidays (0 = non-holiday, 1 = national holidays, etc.)
-  workingday   : Working day (0 = weekends and holidays; 1 = weekdays except weekends and non-holiday )
-  weather   : Weather  
-  temp   : Temperature  
-  atemp   : Sensory temperature  
-  humidity   : Relative humidity  
-  windspeed   : Wind speed  
-  count   : number of rentals


## __2. Create a regression model__


Create a bike demand prediction regression model using the bike_sharing_train dataset you saw in the previous step. Run the query syntax below to create a model named bike_regession.  

(Expected time required for query: 8 min)

```python tags=[]
%%thanosql
BUILD MODEL tutorial_automl_regressor
USING AutomlRegressor
OPTIONS (
    target='count', 
    impute_type='simple', 
    datetime_attribs=['datetime'],
    time_left_for_this_task = 300,
    overwrite = True
    ) 
AS
SELECT *
FROM bike_sharing_train
```

- Use the query syntax "__BUILD MODEL__" to create and train a model called bike_regession.
- For "target" in "__OPTIONS___", write the name of the column that is the target value of the regression prediction model. For "imput_type", it means the processing for empty values in the dataset. "datetime_attribs" allows you to create machine learning models by writing down data in date format.


__Overwrite is True__, users can create a data table with the same name as the previously created data table. 
__Overwrite is False__, user cannot create a data table with the same name as the previously created data table.


## __3. Evaluating Generated Models__


Run the query statement below to evaluate the performance of the prediction model you created in the previous step.

```python tags=[]
%%thanosql
EVALUATE USING tutorial_automl_regressor 
OPTIONS (
    target='count'
    ) 
AS
SELECT *
FROM bike_sharing_train
```

__Query Details__
- Evaluate the bike_regression model built using the query syntax "__EVALUATE USING__". 
- "Target" in "__OPTIONS__", write the name (count) of the column that is the target value of the regression prediction model.


## __4. Predict the number of bike rentals using the generated model__


Use the demand forecast model you created in the previous step to predict the number of bicycle rentals for 10 data in the test dataset (data table not used for learning, bike_sharing_test).

```python tags=[]
%%thanosql
PREDICT USING tutorial_automl_regressor 
AS
SELECT *
FROM bike_sharing_test
LIMIT 10
```

__Query Details__

- Use the query syntax "__PREDICTUSING__" to predict the <mark style="background-color:#E9D7FD">bike_regression</mark> model. 
- "__PREDICT__" requires no special processing because it follows the procedure of the generated model.
