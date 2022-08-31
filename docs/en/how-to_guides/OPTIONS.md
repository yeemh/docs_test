---
title: ThanoSQL Algorithm Query Syntax
---

# **ThanoSQL Algorithm Query Syntax**

## Preface

- Updated Date : {{ git_revision_date_localized }}

The query syntax retrieves one or more expressions and returns a table of calculated results. This page describes the syntax for the algorithm query used by ThanoSQL.

**Notation Conventions**

- Parentheses `()` indicate ^^literal^^ parentheses.
- Braces {} are used to bind combinations of options.
- The square bracket `[]` indicates an optional clause.
- An ellipsis following a comma in square brackets [ , ... ] means that the preceding item can be repeated as a comma-separated list.
- The vertical bar `|` represents the logic `OR`.
- VALUE means a value.

!!! note ""
    **literal** : A fixed or unchangeable value, also known as Constant.
    > Each literal has a special data type, such as a column, in the table.

## **1. AutomlClassifier Algorithm**

### **BUILD MODEL Query Syntax**

Use this "**BUILD MODEL**" query syntax to develop an AI model.
The "**BUILD MODEL**" expression allows you to learn defined data sets through query_expr after "**AS**".

```sql
query_statement:
    query_expr

BUILD MODEL (model_name_expression)
USING AutomlClassifier
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```

**OPTIONS clause**

```sql
OPTIONS(
    target = column_name,
    [impute_type = {"simple" | "iterative"}],
    [features_to_drop = [column_name, ...]],
    [datetime_attribs = [column_name, ...]],
    [outlier_method = {"pca" | "iso" | "knn"}],
    [time_left_for_this_task = VALUE],
    [overwrite = {True | False}]
    )
```

The "**OPTIONS**" clause allows you to change the value of the parameters in the AutomlClassifier from their default values. The meaning of each parameter is as follows.

- "target" : Set the column that is the target value for the classification prediction model in the data table.
- "impute_type" : Sets how empty values are handled in the data table.(DEFAULT : "simple")
> "simple" : For empty values, categorical variables are treated as the least value and continuous variables as the mean.  
> "iterative" : For empty values, process by applying an algorithm that predicts through the remaining properties.
- "features_to_drop" : Sets the columns that are not available for learning in the data table.
- "datetime_attribs" : Sets the column corresponding to the date in the data table.
- "outlier_method" : Sets how outliers are handled in the data table.(DEFAULT : "iso")
> "pca" : Detect abnormal samples by reducing and restoring dimensions using Principal Component Analysis (PCA) for a given data table.  
> "iso" : For a given data table, use Isolation Forest to randomly branch the data table on a tree basis, isolate all observations, and detect abnormal samples. (It works efficiently on data sets with many variables.)  
>  "knn" : A K-NN-based approach detects abnormal samples based on the distance between each data.
- "time_left_for_this_task" : Indicates the time it takes to find a suitable classification prediction model. The larger the value, the more likely it is to find a suitable model (DEFAULT : 300)
- "overwrite" :Sets the presence or absence of an overwriteable model with the same name if it exists. If true, the existing model will be replaced with the new model. (DEFAULT : False)

**BUILD MODEL Query Syntax example**

You can find examples of using the algorithm query syntax in the [Creating a classification model using Auto-ML](/en/tutorials/thanosql_ml/classification/automl_classification/).

```sql
%%thanosql
BUILD MODEL titanic_classification
USING AutomlClassifier
OPTIONS (
    target='survived',
    impute_type='iterative',
    features_to_drop=["name", 'ticket', 'passengerid', 'cabin'],
    outlier_method = 'pca',
    overwrite = True
    )
AS
SELECT *
FROM titanic_train
```

### **FIT MODEL Query Syntax**

This "**FIT MODEL**" query syntax lets you re-learn artificial intelligence models. The "**FIT MODEL**" expression allows you to re-learn defined data sets via query_expr after "**AS**".

```sql
query_statement:
    query_expr

FIT MODEL (model_name_expression)
USING (model_name_expression)
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```

**OPTIONS clause**

```sql
OPTIONS(
    target = column_name,
    [impute_type = {"simple" | "iterative"}],
    [features_to_drop = [column_name, ...]],
    [datetime_attribs = [column_name, ...]],
    [outlier_method = {"pca" | "iso" | "knn"}],
    [time_left_for_this_task = VALUE],
    [overwrite = {True | False}]

    )
```

The "**OPTIONS**" clause allows you to change the value of the parameters in the AutomlClassifier from the default value. The meaning of each parameter is as follows.

- "target" : In the data table, set the columns that target the classification prediction model.
- "impute_type" : Sets how the data table handles empty values.
  (DEFAULT : "simple")
> "simple" : For empty values, categorical variables are treated as the least value and continuous variables as the mean.  
> "iterative" : For empty values, process by applying an algorithm that predicts through the remaining properties.
- "features_to_drop" : Sets the columns that are not available for learning in the data table.
- "datetime_attribs" : Sets the column corresponding to the date in the data table.
- "outlier_method" : Sets how outliers are handled in the data table. (DEFAULT : "iso")
> "pca" : Detect abnormal samples by reducing and restoring dimensions using Principal Component Analysis (PCA) for a given data table.  
> "iso" : For a given data table, Isolation Forest is used to randomly branch the data table on a tree basis, isolate all observations, and detect abnormal samples (even on a data set with many variables)  
> "knn" :A K-NN-based approach detects abnormal samples based on the distance between each data.
- "time_left_for_this_task" : Indicates the time it takes to find a suitable classification prediction model. The larger the value, the more likely it is to find a suitable model (DEFAULT : 300)
- "overwrite" : Sets the presence or absence of an overwriteable model with the same name if it exists. If true, the existing model will be replaced with the new model. (DEFAULT : False)

**FIT MODEL Query Syntax example**

You can find examples of using the algorithm query syntax in [Retrain the model](/en/how-to_guides/ThanoSQL_ml/FIT_MODEL_SYNTAX/)

```sql
%%thanosql
FIT MODEL fit_test_classifier
USING test_classifier
OPTIONS (
    target = 'survived',
    impute_type='simple',
    features_to_drop=["name", 'ticket', 'passengerid', 'cabin'],
    overwrite=True
    )
AS
SELECT *
FROM titanic_train
```

### **TRANSFORM USING Query Syntax**

This "**TRANSFORM MODEL**" query syntax can be used to apply the same preprocessing method used to create AI models to test datasets. The "**TRANSFORM MODEL**" expression can preprocess the dataset defined by query_expr after "**AS**".

```sql
query_statement:
    query_expr

TRANSFORM USING (model_name_expression)
AS
(query_expr)
```

**TRANSFORM USING Query Syntax eaxmple**

An example of using the algorithm query syntax can be found in [Preprocessing data for model application](/en/how-to_guides/ThanoSQL_ml/TRANSFORM_MODEL_SYNTAX/).

```sql
%%thanosql
TRANSFORM USING test_classifier
AS
SELECT *
FROM titanic_test
```

### **PREDICT USING Query Syntax**

Use this "**PREDICT USING**" query syntax to apply artificial intelligence models to test datasets to perform prediction, classification, recommendation, and more. The "**PREDICT USING**" expression can preprocess the dataset defined by query_expr after "**AS**".

```sql
query_statement:
    query_expr

PREDICT USING (model_name_expression)
AS
(query_expr)
```

**PREDICT USING Query Syntax example**

You can find examples of using the algorithm query syntax in [Create Classification Model using Auto-ML](/en/tutorials/thanosql_ml/classification/automl_classification/).

```sql
%%thanosql
PREDICT USING titanic_classification
AS
SELECT *
FROM titanic_test
```

### **EVALUATE USING Query Syntax**

You can use this "**EVALUATE USING**" query syntax to perform evaluation on the AI model. The "**EVALUATE USING**" expression evaluates the dataset defined by query_expr after "**AS**".

```sql
query_statement:
    query_expr

EVALUATE USING (model_name_expression)
OPTIONS (
    target = expression
    )
AS
(query_expr)
```

**OPTIONS clause**

```sql
OPTIONS(
    target = column_name
    )
```

The "**OPTIONS**" clause allows you to change the value of the parameters in the AutomlClassifier from their default values. The meaning of each parameter is as follows.

- "target" : In the data table, set the column that is the target value for the classification prediction model.

**EVALUATE USING Query Syntax example**

You can find examples of using the algorithm query syntax in [Create Classification Model using Auto-ML](/en/tutorials/thanosql_ml/classification/automl_classification/).

```sql
%%thanosql
EVALUATE USING titanic_classification
OPTIONS (
    target = 'survived'
    )
AS
SELECT *
FROM titanic_train
```

## **2. AutomlRegressor Algorithm**

### **BUILD MODEL Query Syntax**

Use this "**BUILD MODEL**" query syntax to develop an AI model.
The "**BUILD MODEL**" expression allows you to learn defined data sets through query_expr after "**AS**".

```sql
query_statement:
    query_expr

BUILD MODEL (model_name_expression)
USING AutomlRegressor
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```

**OPTIONS 절**

```sql
OPTIONS(
    target = column_name,
    [impute_type = {"simple" | "iterative"}],
    [features_to_drop = [column_name, ...]],
    [datetime_attribs = [column_name, ...]],
    [outlier_method = {"pca" | "iso" | "knn"}],
    [time_left_for_this_task = VALUE],
    [overwrite = {True | False}]
    )
```

The "**OPTIONS**" clause allows you to change the value of the AutomlRegressor parameter from its default value. The meaning of each parameter is as follows.

- "target" : Sets the column in the data table to target the regression prediction model.
- "impute_type" : Sets how the data table handles empty values. (DEFAULT: "simple")
> "simple" : For empty values, categorical variables are treated as the least value and continuous variables as the mean.  
> "iterative" : applies an algorithm that predicts through the remaining properties for empty values.
- "features_to_drop" : Sets columns not available for learning in the data table.
- "datetime_attribs" : Sets the column corresponding to the date in the data table.
- "outlier_method" : Sets how the data table handles outliers. (DEFAULT: "iso")
> "pca" : Detect abnormal samples by reducing and restoring dimensions using Principal Component Analysis (PCA) for a given data table.  
> "iso" : For a given data table, we use Isolation Forest to randomly branch the data table on a tree basis, isolate all observations, and detect abnormal samples. (It works efficiently on a data set with many variables)  
> "knn" : A K-NN-based approach detects abnormal samples based on the distance between each data.
- "time_left_for_this_task" : means the time it takes to find a suitable classification prediction model. The larger the value, the more likely it is to find a suitable model (DEFAULT : 300)
- "overwrite" : Sets whether or not a model with the same name can be overwritten if it exists. If true, the existing model will be changed to the new model. (DEFAULT: False)

**BUILD MODEL Query Syntax Example**

An example of using the algorithm query syntax can be found in [Create a prediction model using Auto-ML](/en/tutorials/thanosql_ml/regression/automl_regression/).

```sql
%%thanosql
BUILD MODEL bike_regression
USING AutomlRegressor
OPTIONS (
    target='count',
    impute_type='simple',
    datetime_attribs=['datetime'],
    overwrite = True
    )
AS
SELECT *
FROM bike_sharing
```

### **FIT MODEL Query Syntax**

This "**FIT MODEL**" query syntax lets you re-learn artificial intelligence models. The "**FIT MODEL**" expression allows you to re-learn defined data sets via query_expr after "**AS**".

```sql
query_statement:
    query_expr

FIT MODEL (model_name_expression)
USING (model_name_expression)
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```

**OPTIONS 절**

```sql
OPTIONS(
    target = column_name,
    [impute_type = {"simple" | "iterative"}],
    [features_to_drop = [column_name, ...]],
    [datetime_attribs = [column_name, ...]],
    [outlier_method = {"pca" | "iso" | "knn"}],
    [time_left_for_this_task = VALUE],
    [overwrite = {True | False}]
    )
```

The "**OPTIONS**" clause allows you to change the value of the AutomlRegressor parameter from its default value. The meaning of each parameter is as follows.

- "target" : Sets the column in the data table to target the regression prediction model.
- "impute_type" : Sets how the data table handles empty values. (DEFAULT: "simple")
> "simple" : For empty values, categorical variables are treated as the least value and continuous variables as the mean.  
> "iterative" : applies an algorithm that predicts through the remaining properties for empty values.  
- "features_to_drop" : Sets columns not available for learning in the data table.
- "datetime_attribs" : Sets the column corresponding to the date in the data table.
- "outlier_method" : Sets how the data table handles outliers. (DEFAULT: "iso")
> "pca" : Detect abnormal samples by reducing and restoring dimensions using Principal Component Analysis (PCA) for a given data table.  
> "iso" : For a given data table, we use Isolation Forest to randomly branch the data table on a tree basis, isolate all observations, and detect abnormal samples. (It works efficiently on a data set with many variables)  
> "knn" : A K-NN-based approach detects abnormal samples based on the distance between each data.
- "time_left_for_this_task" : means the time it takes to find a suitable classification prediction model. The larger the value, the more likely it is to find a suitable model (DEFAULT : 300)
- "overwrite" : Sets whether or not a model with the same name can be overwritten if it exists. If true, the existing model will be changed to the new model. (DEFAULT: False)

**FIT MODEL Query Syntax Example**

An example of using the algorithm query syntax can be found in [Re-learning the model](/en/how-to_guides/ThanoSQL_ml/FIT_MODEL_SYNTAX/)

```sql
%%thanosql
FIT MODEL fit_test_classifier
USING test_classifier
OPTIONS (
    target = 'survived',
    impute_type='simple',
    features_to_drop=["name", 'ticket', 'passengerid', 'cabin'],
    overwrite = True

    )
AS
SELECT *
FROM titanic_train
```

### **TRANSFORM USING Query Syntax**

This "**TRANSFORM MODEL**" query syntax can be used to apply the same preprocessing method used to create AI models to test datasets. The "**TRANSFORM MODEL**" expression can preprocess the dataset defined by query_expr after "**AS**".

```sql
query_statement:
    query_expr

TRANSFORM USING (model_name_expression)
AS
(query_expr)
```

**TRANSFORM USING Query Syntax example**

An example of using the algorithm query syntax can be found in [Preprocessing data for model application](/en/how-to_guides/ThanoSQL_ml/TRANSFORM_MODEL_SYNTAX/).

```sql
%%thanosql
TRANSFORM USING test_classifier
AS
SELECT *
FROM titanic_test
```

### **PREDICT USING Query Syntax**

Use this "**PREDICT USING**" query syntax to apply artificial intelligence models to test datasets to perform prediction, classification, recommendation, and more.
The "**PREDICT USING**" expression can preprocess the dataset defined by query_expr after "**AS**".

```sql
query_statement:
    query_expr

PREDICT USING (model_name_expression)
AS
(query_expr)
```

**Example PREDICT USING Query Syntax**

An example of using the algorithm query syntax can be found in [Create a prediction model using Auto-ML](/en/tutorials/thanosql_ml/regression/automl_regression/).

```sql
%%thanosql
PREDICT USING bike_regression
AS
SELECT *
FROM bike_sharing_test
```

### **EVALUATE USING Query Syntax**

You can use this "**EVALUATE USING**" query syntax to perform evaluation on the AI model. The "**EVALUATE USING**" expression evaluates the dataset defined by query_expr after "**AS**".

```sql
query_statement:
    query_expr

EVALUATE USING (model_name_expression)
OPTIONS (
    expression
    )
AS
(query_expr)
```

**OPTIONS 절**

```sql
OPTIONS(
    target = column_name
    )
```

The "**OPTIONS**" clause allows you to change the value of the parameters in the AutomlRegressor from their default values. The meaning of each parameter is as follows.

- "target" : In the data table, set the columns that target the classification prediction model.

**Example EVALUATE USING Query Syntax**

An example of using the algorithm query syntax can be found in [Create a prediction model using Auto-ML](/en/tutorials/thanosql_ml/regression/automl_regression/).

```sql
%%thanosql
EVALUATE USING bike_regression
OPTIONS (
    target='count'
    )
AS
SELECT *
FROM bike_sharing
```

## **3. ConvNeXt, EfficientNet Algorithm**

### **BUILD MODEL Query Syntax**

Use this "**BUILD MODEL**" query syntax to develop an AI model.
The "**BUILD MODEL**" expression allows you to learn defined data sets through query_expr after "**AS**".

```sql
query_statement:
    query_expr

BUILD MODEL (model_name_expression)
USING { ConvNeXt_Tiny | ConvNeXt_Base | EfficientNetV2S | EfficientNetV2M }
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```

**OPTIONS Clause**

```sql
OPTIONS(
    (image_col = column_name),
    (label_col = column_name),
    [batch_size = VALUE],
    [epochs = VALUE],
    [learning_rate = VALUE],
    [overwrite = {True | False}]
    )
```

The "**OPTIONS**" clause allows you to change the value of a parameter from its default value in an image model. The meaning of each parameter is as follows.

- "image_col" : Sets the column containing the path of the image in the data table. (DEFAULT: "image")
- "label_col" : Sets the column containing the path of the label in the data table. (DEFAULT : "label")
- "batch_size" : The size of a bundle of data sets read in one learning. (DEFAULT : 16)
- "epochs" : Sets how many times the data set is repeated in total. (DEFAULT : 3)
- "learning_rate" : The learning rate of the model. (DEFAULT : ConvNeXt=0.0001, EfficientNetV2=0.001)
- "overwrite" : Sets whether or not a model with the same name can be overwritten if it exists. If true, the existing model will be changed to the new model. (DEFAULT: False)

**Example BUILD MODEL Query Syntax**

An example of using the algorithm query syntax can be found in [Create Image Classification Model](/en/tutorials/thanosql_ml/classification/classification_convnext/).

```sql
%%thanosql
BUILD MODEL my_product_classifier
USING ConvNeXt_Tiny
OPTIONS (
  image_col='image_path',
  label_col='div_l',
  epochs=1,
  overwrite=True
  )
AS
SELECT *
FROM product_image_train
```

### **FIT MODEL Query Syntax**

This "**FIT MODEL**" query syntax lets you re-learn artificial intelligence models. The "**FIT MODEL**" expression allows you to re-learn defined data sets via query_expr after "**AS**". In this case, the label of the data used for re-learning should be the same as the label used for previous learning.

```sql
query_statement:
    query_expr

FIT MODEL (model_name_expression)
USING (model_name_expression)
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```

**OPTIONS Clause**

```sql
OPTIONS(
    (image_col = column_name),
    (label_col = column_name),
    [batch_size = VALUE],
    [epochs = VALUE],
    [learning_rate = VALUE],
    [overwrite = {True | False}]
    )
```

The "**OPTIONS**" clause allows you to change the value of a parameter from its default value in an image model. The meaning of each parameter is as follows.

- "image_col" : Sets the column containing the path of the image in the data table. (DEFAULT: "image")
- "label_col" : Sets the column containing the path of the label in the data table. (DEFAULT : "label")
- "batch_size" : The size of a bundle of data sets read in one learning. (DEFAULT : 16)
- "epochs" : Sets how many times the data set is repeated in total. (DEFAULT : 3)
- "learning_rate" : The learning rate of the model. (DEFAULT : ConvNeXt=0.0001, EfficientNetV2=0.001)
- "overwrite" : Sets whether or not a model with the same name can be overwritten if it exists. If true, the existing model will be changed to the new model. (DEFAULT: False)

### **PREDICT USING Query Syntax**

Use this "**PREDICT USING**" query syntax to apply artificial intelligence models to test datasets to perform prediction, classification, recommendation, and more. The "**PREDICT USING**" expression can preprocess the dataset defined by query_expr after "**AS**".

```sql
query_statement:
    query_expr

PREDICT USING (model_name_expression)
OPTIONS (
    expression
    )
AS
(query_expr)
```

**OPTIONS Clause**

```sql
OPTIONS(
    (image_col = column_name),
    [batch_size = VALUE]
    )
```

The "**OPTIONS**" clause allows you to change the value of a parameter from its default value in an image model. The meaning of each parameter is as follows.

- "image_col" : Sets the column containing the path of the image in the data table. (DEFAULT: "image")
- "batch_size" : The size of a bundle of data sets read in one learning. (DEFAULT : 16)

**Example PREDICT USING Query Syntax**

An example of using the algorithm query syntax can be found in [Create Image Classification Model](/en/tutorials/thanosql_ml/classification/classification_convnext/).

```sql
%%thanosql
PREDICT USING my_product_classifier
OPTIONS (
    image_col='image_path'
    )
AS
SELECT *
FROM product_image_test
```

### **EVALUATE USING Query Syntax**

You can use this "**EVALUATE USING**" query syntax to perform evaluation on the AI model. The "**EVALUATE USING**" expression evaluates the dataset defined by query_expr after "**AS**".

```sql
query_statement:
    query_expr

EVALUATE USING (model_name_expression)
OPTIONS (
    expression
    )
AS
(query_expr)
```

**OPTIONS Clause**

```sql
OPTIONS(
    (image_col = column_name),
    [batch_size = VALUE]
    )
```

The "**OPTIONS**" clause allows you to change the value of a parameter from its default value in an image model. The meaning of each parameter is as follows.

- "image_col" : Sets the column containing the path of the image in the data table. (DEFAULT: "image")
- "batch_size" : The size of a bundle of data sets read in one learning. (DEFAULT : 16)

## **4. Albert, Electra Model**

### **BUILD MODEL Query Syntax**

Use this "**BUILD MODEL**" query syntax to develop an AI model.
The "**BUILD MODEL**" expression allows you to learn defined data sets through query_expr after "**AS**".

```sql
query_statement:
    query_expr

BUILD MODEL (model_name_expression)
USING {AlbertKo | AlbertEn | ElectraKo | ElectraEn}
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```

**OPTIONS Clause**

```sql
OPTIONS(
    (text_col = column_name),
    (label_col = column_name),
    [batch_size = VALUE],
    [epochs = VALUE],
    [learning_rate = VALUE],
    [overwrite = {True | False}]
    )
```

The "**OPTIONS**" clause allows you to change the value of a parameter from its default value in an image model. The meaning of each parameter is as follows.

- "text_col" : Sets the column containing the text to be classified in the data table. (DEFAULT: "text")
- "label_col" : Sets the column containing the path of the label in the data table. (DEFAULT : "label")
- "batch_size" : The size of a bundle of data sets read in one learning. (DEFAULT : 16)
- "epochs" : Sets how many times the data set is repeated in total. (DEFAULT : 3)
- "learning_rate" : The learning rate of the model. (DEFAULT : 0.0001)
- "overwrite" : Sets whether or not a model with the same name can be overwritten if it exists. If true, the existing model will be changed to the new model. (DEFAULT: False)

**BUILD MODEL Query Syntax Example**

An example of using the algorithm query syntax can be found in [Create a Text Classification Model](/en/tutorials/thanosql_ml/classification/classification_electra/).

```sql
%%thanosql
BUILD MODEL my_movie_review_classifier
USING ElectraEn
OPTIONS (
  text_col='review',
  label_col='sentiment',
  epochs=1,
  batch_size=4,
  overwrite = True
  )
AS
SELECT *
FROM movie_review_train
```

### **FIT MODEL Query Syntax**

This "**FIT MODEL**" query syntax lets you re-learn artificial intelligence models. The "**FIT MODEL**" expression allows you to re-learn defined data sets via query_expr after "**AS**". In this case, the label of the data used for re-learning should be the same as the label used for previous learning.

```sql
query_statement:
    query_expr

FIT MODEL (model_name_expression)
USING (model_name_expression)
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```

**OPTIONS Clause**

```sql
OPTIONS(
    (text_col = column_name),
    (label_col = column_name),
    [batch_size = VALUE],
    [epochs = VALUE],
    [learning_rate = VALUE],
    [overwrite = {True | False}]
    )
```

The "**OPTIONS**" clause allows you to change the value of a parameter from its default value in an image model. The meaning of each parameter is as follows.

- "text_col" : Sets the column containing the text to be classified in the data table. (DEFAULT: "text")
- "label_col" : Sets the column containing the path of the label in the data table. (DEFAULT : "label")
- "batch_size" : The size of a bundle of data sets read in one learning. (DEFAULT : 16)
- "epochs" : Sets how many times the data set is repeated in total. (DEFAULT : 3)
- "learning_rate" : The learning rate of the model. (DEFAULT : 0.0001)
- "overwrite" : Sets whether or not a model with the same name can be overwritten if it exists. If true, the existing model will be changed to the new model. (DEFAULT: False)

### **PREDICT USING Query Syntax**

Use this "**PREDICT USING**" query syntax to apply artificial intelligence models to test datasets to perform prediction, classification, recommendation, and more. The "**PREDICT USING**" expression can preprocess the dataset defined by query_expr after "**AS**".

```sql
query_statement:
    query_expr

PREDICT USING (model_name_expression)
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```

**Example PREDICT USING Query Syntax**

An example of using the algorithm query syntax can be found in [Create Text Classification Model](/en/tutorials/thanosql_ml/classification/classification_electra/).

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

 __OPTIONS Clause__

```sql
OPTIONS(
    (text_col = column_name),
    )
```

The "**OPTIONS**" clause allows you to change the value of a parameter from its default value in an image model. The meaning of each parameter is as follows.

- "text_col" : Sets the column containing the text to be classified in the data table. (DEFAULT: "text")

### **EVALUATE USING Query Syntax**

You can use this "**EVALUATE USING**" query syntax to perform evaluation on the AI model. The "**EVALUATE USING**" expression evaluates the dataset defined by query_expr after "**AS**".

```sql
query_statement:
    query_expr

EVALUATE USING (model_name_expression)
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```

**OPTIONS Clause**

```sql
OPTIONS(
    (text_col = column_name),
    [batch_size = VALUE]
    )
```

The "**OPTIONS**" clause allows you to change the value of a parameter from its default value in an image model. The meaning of each parameter is as follows.

- "text_col" : Sets the column containing the text to be classified in the data table. (DEFAULT: "text")

- "batch_size" : The size of a bundle of data sets read in one learning. (DEFAULT : 16)

## **5. Wav2Vec2 Model**

### **BUILD MODEL Query Syntax**

Use this "**BUILD MODEL**" query syntax to develop an AI model.
The "**BUILD MODEL**" expression allows you to learn defined data sets through query_expr after "**AS**".

```sql
query_statement:
    query_expr

BUILD MODEL (model_name_expression)
USING { Wav2Vec2Ko | Wav2Vec2En }
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```

**OPTIONS Clause**

```sql
OPTIONS(
    (audio_col = column_name),
    (text_col = column_name),
    [batch_size = VALUE],
    [epochs = VALUE],
    [learning_rate = VALUE],
    [overwrite = {True | False}]
    )
```

The "**OPTIONS**" clause allows you to change the value of a parameter from its default value in an image model. The meaning of each parameter is as follows.

- "audio_col" : Sets the column containing the path of the audio files in the data table (DEFAULT: "audio_path")
- "text_col" : Sets the column containing the audio script in the data table. (DEFAULT: "text")
- "batch_size" : The size of a bundle of data sets read in one learning. (DEFAULT : 16)
- "epochs" : Sets how many times the data set is repeated in total. (DEFAULT : 5)
- "learning_rate" : The learning rate of the model. (DEFAULT : 0.0001)
- "overwrite" : Sets whether or not a model with the same name can be overwritten if it exists. If true, the existing model will be changed to the new model. (DEFAULT: False)

**Example BUILD MODEL Query Syntax**

An example of using the algorithm query syntax can be found in [Create Speech Recognition Model](/en/tutorials/thanosql_ml/audio_recognition/audio_recognition_wav2vec/).

```sql
%%thanosql
BUILD MODEL my_speech_recognition_model
USING Wav2Vec2En
OPTIONS (
  audio_col='audio_path',
  text_col='text',
  epochs=1,
  batch_size=4 ,
  overwrite=True
  )
AS
SELECT *
FROM librispeech_train
```

### **FIT MODEL Query Syntax**

This "**FIT MODEL**" query syntax lets you re-learn artificial intelligence models. The "**FIT MODEL**" expression allows you to re-learn defined data sets via query_expr after "**AS**".

```sql
query_statement:
    query_expr

FIT MODEL (model_name_expression)
USING (model_name_expression)
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```

**OPTIONS 절**

```sql
OPTIONS(
    (audio_col = column_name),
    (text_col = column_name),
    [batch_size = VALUE],
    [epochs = VALUE],
    [learning_rate = VALUE],
    [overwrite = {True | False}]
    )
```

The "**OPTIONS**" clause allows you to change the value of a parameter from its default value in an image model. The meaning of each parameter is as follows.

- "audio_col" : Sets the column containing the path of the audio files in the data table (DEFAULT: "audio_path")
- "text_col" : Sets the column containing the audio script in the data table. (DEFAULT: "text")
- "batch_size" : The size of a bundle of data sets read in one learning. (DEFAULT : 16)
- "epochs" : Sets how many times the data set is repeated in total. (DEFAULT : 5)
- "learning_rate" : The learning rate of the model. (DEFAULT : 0.0001)
- "overwrite" : Sets whether or not a model with the same name can be overwritten if it exists. If true, the existing model will be changed to the new model. (DEFAULT: False)

### **PREDICT USING Query Syntax**

Use this "**PREDICT USING**" query syntax to apply artificial intelligence models to test datasets to perform prediction, classification, recommendation, and more. The "**PREDICT USING**" expression can preprocess the dataset defined by query_expr after "**AS**".

```sql
query_statement:
    query_expr

PREDICT USING (model_name_expression)
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```

**OPTIONS Clause**

```sql
OPTIONS(
    (audio_col = column_name),
    [batch_size = VALUE]
    )
```

The "**OPTIONS**" clause allows you to change the value of a parameter from its default value in an image model. The meaning of each parameter is as follows.

- "audio_col" : Sets the column containing the path of the audio files in the data table (DEFAULT: "audio")
- "batch_size" : The size of a bundle of data sets read in one learning. (DEFAULT : 16)

**Example PREDICT USING Query Syntax**

An example of using the algorithm query syntax can be found in [Create Speech Recognition Model](/en/tutorials/thanosql_ml/audio_recognition/audio_recognition_wav2vec/).

```sql
%%thanosql
PREDICT USING my_speech_recognition_model
OPTIONS (
  audio_col='audio_path'
  )
AS
SELECT *
FROM librispeech_test
```

### **EVALUATE USING Query Syntax**

You can use this "**EVALUATE USING**" query syntax to perform evaluation on the AI model. The "**EVALUATE USING**" expression evaluates the dataset defined by query_expr after "**AS**".

```sql
query_statement:
    query_expr

EVALUATE USING (model_name_expression)
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```

**OPTIONS Clause**

```sql
OPTIONS(
    (audio_col = column_name),
    (text_col = column_name),
    [batch_size = VALUE]
    )
```

The "**OPTIONS**" clause allows you to change the value of a parameter from its default value in an image model. The meaning of each parameter is as follows.

- "audio_col" : Sets the column containing the path of the audio files in the data table (DEFAULT: "audio_path")
- "text_col" : Sets the column containing the audio script in the data table. (DEFAULT: "text")
- "batch_size" : The size of a bundle of data sets read in one learning. (DEFAULT : 16)

## **6. SimCLR Model**

### **BUILD MODEL Query Syntax**

Use this "**BUILD MODEL**" query syntax to develop an AI model.
The "**BUILD MODEL**" expression allows you to learn defined data sets through query_expr after "**AS**".
​

```sql
BUILD MODEL (model_name_expression)
USING SimCLR
OPTIONS (
    expression [ , ...]
)
AS
(query_expr)
```

​
**OPTIONS Clause**
​

```sql
OPTIONS(
    (image_col = VALUE),
    (filename_col = VALUE),
    [label_col = VALUE],
    [max_epochs = VALUE],
    [batch_size = VALUE],
    [overwrite = {True | False}]
)
```

​
The "**OPTIONS**" clause allows you to change the values of parameters in the SimCLR quantization model from their default values. The meaning of each parameter is as follows.

- "image_col" : Sets the path of the image in the data table. (DEFAULT : "path")
- "filename_col" : Sets the column containing the image file name in the data table. (DEFAULT : "file_name")
- "label_col" : Column containing image label. (DEFAULT : "label")
- "max_epochs" : Sets the number of model learnings (DEFAULT : 5)
- "batch_size" : Sets the number of data in a bundle of data used during learning. (DEFAULT: 256)
- "overwrite" : Sets whether or not a model with the same name can be overwritten if it exists. If true, the existing model will be changed to the new model. (DEFAULT: False)

**Example BUILD MODEL Query Syntax**

You can find examples of using the algorithm query syntax in [Search Image as Image](/en/tutorials/thanosql_search/image_search/simclr_image_search/).
​

```sql
%%thanosql
BUILD MODEL my_image_search_model
USING SimCLR
OPTIONS (
    image_col="image_path",
    max_epochs=1,
    overwrite=True
    )
AS
SELECT *
FROM mnist_train
```

### **CREATE TABLE Query Syntax**

This "**CREATE TABLE**" query syntax enables digitization conversion using image folder paths without a table containing image-specific folder path information.
The "**CREATE TABLE**" expression quantifies the image files in the image folder path after "**FROM**" and stores them as a table.
​

```sql
​
CREATE TABLE (table_name_expression) 
USING (model_name_expression)
OPTIONS (
    expression [ , ...]
    )
FROM
(query_expr)
```

​
**OPTIONS Clause**
​

```sql
OPTIONS(
    (path_type = {'folder'|'file'}),
    (data_type = {'image'|'audio'|'video'}),
    (file_type = LIST),
    [overwrite = {True | False}]

    )
```

​
"**OPTIONS**" defines the attribute values of the image file for image quantification. The meaning of each parameter is as follows.

- "path_type" : Sets the type of file path where data is stored (folder|file)

- "data_type" : Sets the type of unstructured data you enter. (image|audio|video)

- "file_type" : Defines the extension of the destination file as a list (ex. ['.jpg'], '['.png'])
  ​
- "overwrite" : Sets whether or not a model with the same name can be overwritten if it exists. If true, the existing model will be changed to the new model. (DEFAULT: False)

### **CONVERT USING Query Syntax**

Use this "**CONVERT USING**" query syntax to store the digitized results as a new column in the existing dataset using a dataset containing the paths of existing images. Each time a new quantization model is used, a new quantization column is added to facilitate comparison by quantification results.
​

```sql
CONVERT USING (model_name_expression)
OPTIONS(
    expression [ , ...]
    )
AS
(query_expr)
```

**OPTIONS Clause**

```sql
OPTIONS(
    (table_name = VALUE)
    )
```

The "**OPTIONS**" clause allows you to change the values of parameters in the SimCLR quantization model from their default values. The meaning of each parameter is as follows.
​

- "table_name" : Sets the name of the table to store the new digitization results

**Example of CONVERT USING query syntax**

You can find examples of using the algorithm query syntax in [Search Image as Image](/en/tutorials/thanosql_search/image_search/simclr_image_search/).
​

```sql
%%thanosql
CONVERT USING my_image_search_model
OPTIONS (
    table_name= "mnist_test",
    image_col="image_path"
    )
AS
SELECT *
FROM mnist_test
```

### **SEARCH IMAGE Query Syntax**

You can use the "**SEARCH IMAGE**" query syntax to retrieve the desired image from the table that generated the digitization.

```sql
SEARCH IMAGE (images = expression)
USING (model_name_expression)
AS
(query_expr)
```

!!! note ""
    You must receive images as input from the user. The input must be a string (for example, 'a black cat', 'data/image/image01.jpg')

**Example SEARCH IMAGE Syntax**

You can find examples of using the algorithm query syntax in [Search Image as Image](/en/tutorials/thanosql_search/image_search/simclr_image_search/).

```sql
%%thanosql
SEARCH IMAGE images='tutorial_data/mnist_data/test/923.jpg'
USING my_image_search_model
AS
SELECT *
FROM mnist_test
```

## **7. CLIP Model**

### **CREATE TABLE Query Syntax**

You can use the syntax "**CREATE TABLE**" to generate a data table containing the quantization vectors of the image data.

```sql
CREATE TABLE (table_name_expression)
USING clip_en
OPTIONS (
    expression [ , ...]
    )
FROM
(query_expresison)
```

**OPTIONS Clause**

```sql
OPTIONS(
    (path_type = column_name),
    (data_type = column_name),
    [file_type = VALUE],
    [batch_size = VALUE],
    [overwrite = {True | False}]
    )
```

The "**OPTIONS**" clause allows you to change the value of a parameter in CLIP from its default value. The meaning of each parameter is as follows.

- "path_type" : Sets the column containing the path of the audio files in the data table (DEFAULT: "audio_path")
- "data_type" : Type of data.
- "file_type" : The format of the extension of the image.
- "batch_size" : The size of a bundle of data sets read from one prediction. (DEFAULT : 16)
- "overwrite" : Sets whether or not a model with the same name can be overwritten if it exists. If true, the existing model will be changed to the new model. (DEFAULT: False)

### **CONVERT USING Query Syntax**

The "**CONVERT USING**" query syntax converts image data from an existing table into a quantized vector and adds it to the data table to use.

```sql
CONVERT USING clip_en
OPTIONS(
    (table_name = expression),
    (image_col = column_name),
    [batch_size = VALUE]
    )
AS
(query_expr)
```

**OPTIONS Clause**

```sql
OPTIONS(
    (table_name=expression),
    (image_col=column_name),
    [batch_size=VALUE]
)
```

The "**OPTIONS**" clause allows you to change the value of a parameter from its default value in the model. The meaning of each parameter is as follows.

- "table_name" : The name of the new table to be created.
- "image_col" : The name of the column containing the path of the image in the table. (DEFAULT : 'image_path')
- "batch_size" : The size of a bundle of data sets read from one prediction. (DEFAULT : 16)

**Example of CONVERT USING syntax**

You can find examples of using the algorithm query syntax in [search by text](/en/tutorials/thanosql_search/image_search/clip_image_search/).

```sql
%%thanosql
CONVERT USING tutorial_search_clip
OPTIONS (
    image_col="image_path",
    table_name="unsplash_data",
    batch_size=128
    )
AS
SELECT *
FROM unsplash_data
```

### **SEARCH IMAGE Query Syntax**

You can use the "**SEARCH IMAGE**" query syntax to retrieve the desired image from the table that generated the digitization.

```sql
SEARCH IMAGE ({text|texts|image|images} = expression)
USING clip_en
AS
(query_expr)
```

!!! note ""
    You must receive one of text, texts, image, and images as input. Text and texts, image, and images are the same. The input must be string (e.g., 'a black cat', 'data/image/image01.jpg') or list of strings (e.g., ['a black cat', 'a orange cat'], ['data/image/image01.jpg', 'data/image/image02.jpg']).

**Example SEARCH IMAGE Syntax**

You can find examples of using the algorithm query syntax in [search by text](/en/tutorials/thanosql_search/image_search/clip_image_search/).

```sql
%%thanosql
SEARCH IMAGE text="a black cat"
USING tutorial_search_clip
AS
SELECT *
FROM unsplash_data
```
