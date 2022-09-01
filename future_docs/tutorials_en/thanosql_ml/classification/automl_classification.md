---
title: Create a classification model using AutoML
---

# **Create a classification model using AutoML**

## Preface

- Tutorial Difficulty : ★☆☆☆☆
- 4 min read
- Languages : [SQL](https://ko.wikipedia.org/wiki/SQL) (100%)
- File location : tutorial/tutorial_en/ml/classification_model/automl_classification_en.ipynb
- References : [(Kaggle) Titanic - Machine Learning from Disaster](https://www.kaggle.com/competitions/titanic/overview)
- Updated Date : {{ git_revision_date_localized }}

## Tutorial Introduction

!!! note "Understanding Classification Operations"
    Classification is a form of [Machine Learning](https://en.wikipedia.org/wiki/Machine_learning) used to predict the category (Category or Class) to which the target belongs. For example, both binary classifications that classify men or women and multiple classifications that predict animal species (dogs, cats, rabbits, etc.) are included in the classification task. <br>

[Customer Relationship Management(CRM)](https://en.wikipedia.org/wiki/Customer_relationship_management) data (demographic information, customer behavior/search data, etc.) is available to predict whether a prospect will respond positively to a particular marketing promotion in the enterprise. In this case, the [Feature](<https://en.wikipedia.org/wiki/Feature_(machine_learning)>) expressed in the CRM data (used as this input data and the target value that you want to predict is whether the target customer's response to the promotion is positive (1 or true) or negative (0 or false). Using this classification model, you can continue to increase marketing efficiency by predicting the responses of customers who are not exposed to marketing and exposing marketing to the right customers.

**Below are examples and utilization of the ThanoSQL classification model.**

- The classification model enables early detection of current user deviations and enables proactive response to problems (deviations). Historical data can help you identify the characteristics of your exodus and allow you to take appropriate action by discovering users who are likely to leave in advance. This can help prevent customer defections and increase sales.

- You can predict your [Segments](https://en.wikipedia.org/wiki/Market_segmentation) within the online platform. Most service users have different characteristics and have different behaviors and needs. Classification prediction models use the characteristics of service users to identify granular groups and enable them to develop strategies tailored to them.

!!! note "In this tutorial"
    :point_right:
    Create a survival predictive classification model using the <mark style="background-color:#FFD79C">**Titanic: Machine Learning from Disaster**</mark> dataset for beginners in [Kaggle](<(https://www.kaggle.com/)>), a leading machine learning competition platform.
    The goals of this competition are as follows.
    (FYI, the data for the competition are the list of passengers who were on board during the actual Titanic incident on April 15, 1912.)

**Predicting Passengers Who Can Survive Titanic**

ThanoSQL provides automated machine learning (**Auto-ML**) tools. This tutorial uses Auto-ML to predict passengers who can survive in the Titanic. Auto-ML from ThanoSQL automates the process for model development and enables data collection and storage, machine learning model development and distribution (end-to-end machine learning pipelines) in a single language without data science expertise.

**Automated ML has the following advantages:**

1. Implementation and deployment of machine learning solutions without extensive programming or data science knowledge
2. Saving time and resources for deployment of development models
3. It is possible to quickly solve problems using the data you have for decision-making

Now let's use ThanoSQL to create a classification model that predicts passengers who can simply survive in the Titanic.

## **0. Data set preparation**

To use the query syntax of ThanoSQL, you must create an API token and run the query below, as mentioned in [ThanoSQL Workspace](/en/getting_started/how_to_use_ThanoSQL/#5-thanosql-workspace).

```sql
%load_ext thanosql
```

```sql
%thanosql API_TOKEN=<Issued_API_TOKEN>
```

```sql
%%thanosql
COPY titanic_train
OPTIONS(overwrite=True)
FROM "tutorial_data/titanic_data/titanic_train.csv"
```

```sql
%%thanosql
COPY titanic_test
OPTIONS(overwrite=True)
FROM "tutorial_data/titanic_data/titanic_test.csv"
```

!!! note "**Query Details**"
    - Specifies the data set name to copy to the DB using the "**COPY**" query syntax.
    - Specify the options to use for **COPY** via the "**OPTIONS**" query syntax.
        - "overwrite" : Set whether or not a data set with the same name can be overwritten when it exists on the DB. If True, the existing dataset is changed to the new dataset (True|False, DEFAULT: False)

## **1. Check Data Set**

To create a survivor predictive classification model, we use the <mark style="background-color:#FFEC92 ">**titanic_train**</mark> table stored in the ThanoSQL DB. Check the contents of the table while executing the query statement below.

```sql
%%thanosql
SELECT *
FROM titanic_train
LIMIT 5
```

[![IMAGE](/img/thanosql_ml/classification/automl/img1.png)](/img/thanosql_ml/classification/automl/img1.png)

!!! note "**Understanding Data**"
    The <mark style="background-color:#FFEC92 ">**tianic_train**</mark> data set contains the following information.

    - <mark style="background-color:#D7D0FF ">passengerid</mark> : Passengers ID
    - <mark style="background-color:#D7D0FF ">survived</mark> : Passengers on board
    - <mark style="background-color:#D7D0FF ">pclass</mark> : Ticket rating for passengers on board
    - <mark style="background-color:#D7D0FF ">name</mark> : Passenger name
    - <mark style="background-color:#D7D0FF ">sex</mark> : Passenger gender
    - <mark style="background-color:#D7D0FF ">age</mark> : Passenger's age
    - <mark style="background-color:#D7D0FF ">sibsp</mark> : Number of brothers and sisters or spouses on board
    - <mark style="background-color:#D7D0FF ">parch</mark> : Number of parents or children on board
    - <mark style="background-color:#D7D0FF ">ticket</mark> : Ticket number
    - <mark style="background-color:#D7D0FF ">fare</mark> : Charge
    - <mark style="background-color:#D7D0FF ">cabin</mark> : The Cabin
    - <mark style="background-color:#D7D0FF">embarked</mark> : The destination or port of entry

In this tutorial, we will proceed with model learning except for <mark style="background-color:#D7D0FF">name</mark>, <mark style="background-color:#D7D0FF">ticket</mark>, and <mark style="background-color:#D7D0FF">cabin</mark> columns that require data preprocessing using additional query statements.

## **2. Create Classification Model**

Create a survivor predictive classification model using the <mark style="background-color:#FFEC92 ">Titanic_train</mark> data that you saw in the previous step. Run the query syntax below to create a model with the <mark style="background-color:#E9D7FD ">Titanic_automl_classification</mark> name.  
(Expected time required for query execution: 8 min)

```sql
%%thanosql
BUILD MODEL titanic_automl_classification
USING AutomlClassifier
OPTIONS (
    target='survived',
    impute_type='iterative',
    features_to_drop=['name', 'ticket', 'passengerid', 'cabin'],
    time_left_for_this_task = 300,
    overwrite = True
    )
AS
SELECT *
FROM titanic_train
```

!!! note "**Query Details**"
    - Create and train a model called <mark style="background-color:#E9D7FD">titanic_automl_classification</mark> using the query syntax "**BUILD MODEL**".
    - Specify the options to use for model creation via the "**OPTIONS**" query syntax.
        - "target" : Column name containing target value of classification model - "impute_type" : Set how to handle empty value (NaN) of data table ('simple'|'iterative', DEFAULT : 'simple')
        - "features_to_drop" : List of column names not available for learning in the data table
        - "time_left_for_this_task" : Time taken to find a suitable classification prediction model (DEFAULT: 300)
        - "overwrite" : Set whether a model with the same name exists. If true, the existing model is changed to a new model (True|False, DEFAULT: False)

!!! warning
    When creating the Auto-ML classification model, if you use any parameter other than the one specified in [OPTIONS](/en/how-to_guides/OPTIONS/#1-automlclassifier-algorithm), the model can be created, but all the values set are ignored.

## **3. Evaluating Generated Models**

Run the query statement below to evaluate the performance of the prediction model you created in the previous step.

```sql
%%thanosql
EVALUATE USING titanic_automl_classification
OPTIONS (
    target = 'survived'
    )
AS
SELECT *
FROM titanic_train
```

[![IMAGE](/img/thanosql_ml/classification/automl/img2.png)](/img/thanosql_ml/classification/automl/img2.png)

!!! note "**Query Details**"
    - Evaluate the model <mark style="background-color:#E9D7FD ">titanic_automl_classification</mark> built using the query syntax "**EVALUATE USING**".
    - Specify the options to use for model evaluation via the "**OPTIONS**" query syntax.
        - "target" : The name of the column that is the target value for the classification prediction model

## **4. Predict survivors using the generated model**

Use the survivor prediction model created in the previous step to predict survival based on occupant information. Use a dataset for testing (data table<mark style="background-color:#FFEC92 "> **titanic_test** </mark>not used for learning).

```sql
%%thanosql
PREDICT USING titanic_automl_classification
AS
SELECT *
FROM titanic_test
```

[![IMAGE](/img/thanosql_ml/classification/automl/img3.png)](/img/thanosql_ml/classification/automl/img3.png)

!!! note "**Query Details**"
    Use the <mark style="background-color:#E9D7FD ">titanic_automl_classification </mark>model created in the previous step for prediction using the **"PREDICT USING" **query syntax.
    "**PREDICT**" requires no special option as it follows the procedure of the model created.

## **5. In Conclusion**

In this tutorial, we created a Titanic survivor classification prediction model using the data <mark style="background-color:#FFD79C"> __Titanic: Machine Learning from Disaster__</mark> at [Kaggle](https://www.kaggle.com/). As it is an entry-level tutorial, we explained the overall process rather than the process for improving accuracy. If you want to learn more about building an improved classification model, we recommend that you proceed with an intermediate tutorial.

In the next [Create an Intermediate Classification Prediction Model] tutorial, we'll discuss "**OPTIONS**" to improve accuracy. Complete the intermediate and advanced phases and create a classification prediction model for your own service/product. In the intermediate phase, we will leverage the various "**OPTIONS**" provided by AutoML from ThanoSQL to create sophisticated classification prediction models. In addition, after completing the intermediate stage, the advanced stage can quantify unstructured data and include it as a learning element in AutoML to create a classification prediction model.

- [How to Upload to ThanoSQL DB](/en/how-to_guides/ThanoSQL_connecting/data_upload/)
- [Creating an Intermediate Image Classification Model]
- [Image conversion and creating 'My model' using Auto-ML]
- [Deploy My Image Classification model](/en/how-to_guides/ThanoSQL_connecting/thanosql_api/rest_api_thanosql_query/)

!!! tip "**Inquiries on model distribution for 'my own' service**"
    If you have difficulty creating your own model using ThanoSQL or applying it to service, please feel free to contact me below😊

    Inquiries about building a speech recognition model: contact@smartmind.team
