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

# __Create a classification model using AutoML__


**Understanding the classification** 
--- 
👉 Classification is a form of machine learning used to predict the category (or class) to which the target belongs. For example, both binary classifications that classify men or women and multiple classifications that predict animal species (dogs, cats, rabbits, etc.) are included in the classification task.

**In this tutorial**
--- 
👉 Create a survival predictive classification model using the Titanic: Machine Learning from Disaster dataset for beginners of Kaggle, a representative machine learning competition platform. The objectives of the competition are as follows (FYI, data from the competition are the list of passengers who were on board during the actual Titanic incident on April 15, 1912)


## __0. Preparing a dataset__


To use the query syntax of ThanoSQL, you must create an API token and run the query below, as mentioned in the [ThanoSQL Workspace](https://docs.thanosql.ai/quick_start/how_to_use_ThanoSQL/#5-thanosql).

```python tags=[]
%load_ext thanosql
%thanosql API_TOKEN=<Issued_API_TOKEN>
```

```python
%%thanosql
COPY titanic_train 
OPTIONS (overwrite=True)
FROM "tutorial_data/titanic_data/titanic_train.csv"
```

```python
%%thanosql
COPY titanic_test 
OPTIONS (overwrite=True)
FROM "tutorial_data/titanic_data/titanic_test.csv"
```

__OPTIONS__ : 

When __overwrite is true__, the user can create a data table with the same name as the previously created data table.  
On the other hand, when __overwrite is False__, the user cannot create a data table with the same name as the previously created data table.


## __1.Check Dataset__



To create a survivor predictive classification model, we use the Titanic_train table stored in the ThanoSQL DB. Check the contents of the table while executing the query statement below.

```python
%%thanosql
SELECT * 
FROM titanic_test 
LIMIT 5 
```

**Understanding data**   
-  passengerid  : Passengers ID
-  survived  : boarding passengers
-  pclass  : Passenger Ticket Rating
-  name  : Boarding passenger
-  sex  : Passenger gender
-  age  : Age of passengers
-  sibsp  : Number of siblings or spouses on board
-  parch  : Number of parents or children on board
-  ticket  : Ticket number
-  fare  : fare  
-  cabin  : Cabin  
-  embarked  : Boarding point or port

In this tutorial, we will proceed with model learning except for the name, ticket, and cabin columns that require data preprocessing using additional query statements.


## __2. Create a classification model__


Create a Survivor Predictive Classification Model using the titanic_train data identified in the previous step. Run the query syntax below to create a model named titanic_classification.

```python tags=[]
%%thanosql
BUILD MODEL tutorial_automl_classification 
USING AutomlClassifier 
OPTIONS (
    target='survived', 
    impute_type='iterative',  
    features_to_drop=["name", 'ticket', 'passengerid', 'cabin'],
    time_left_for_this_task = 300,
    overwrite = True
    ) 
AS 
SELECT * 
FROM titanic_train
```

__Query Details__

- Create and train a model called <mark style="background-color:#E9D7FD">titanic_automl_classification</mark> using the query syntax "_BUILD MODEL___".
- For "target" in "__OPTIONS__", write the name of the column that is the target value in the classification prediction model. For "imput_type", it means the processing for empty values in the dataset. "features_to_drop" allows you to create a machine learning model by writing down data that you do not want to use for learning.


## __3. Evaluate the generated model__


Run the query statement below to evaluate the performance of the prediction model you created in the previous step.

```python tags=[]
%%thanosql 
EVALUATE USING tutorial_automl_classification 
OPTIONS (
    target = 'survived'
    )
AS 
SELECT * 
FROM titanic_train
```

__Query Details__
- Evaluate a model called 'titanic_automl_classification' built using the query syntax "__EVALUATE USING__".
- For "target" in "__OPTIONS__", write the name of the column being the target value in the classification prediction model.


## __4. Predict survivors using the generated model__


Use the survivor prediction model created in the previous step to predict survival based on occupant information. Use a dataset for testing (data table not used for learning, Titanic_test).

```python
%%thanosql 
PREDICT USING tutorial_automl_classification
AS 
SELECT * 
FROM titanic_test
```

__Query Details__

- Use the "PREDICT USING" query syntax to predict the tutorial_automl_classification model created in the previous step. "PREDICT" requires no special processing because it follows the procedure of the model created.
