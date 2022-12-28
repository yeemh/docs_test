---
title: AutomlClassifier
---

# __AutomlClassifier__

__Notation Conventions__

- Parentheses `()` indicate ^^literal^^ parentheses.
- Braces `{}` are used to bind combinations of options.
- The bracket `[]` indicates an optional clause.
- An ellipsis following a comma in brackets [,...] means that the preceding item can be repeated as a comma-separated list
- The vertical bar `|` represents the logic `OR`.
- VALUE represents a regular value.


!!! note ""
    - __literal__: a fixed or unchangeable value, also known as a Constant.
    > Each literal has a special data type such as column, in the table.



## __BUILD MODEL Syntax__

Use the "__BUILD MODEL__" statement to develop an AI model. The "__BUILD MODEL__" statement allows you to train a model using datasets defined with the query_expr that comes after the "__AS__" clause.

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


__OPTIONS Clause__

```sql
OPTIONS (
    (target_col=column_name),
    [features_to_drop=[column_name, ...]],
    [impute_type={'simple'|'iterative'}],
    [datetime_attribs=[column_name, ...]],
    [outlier_method={'knn'|'iso'|'pca'}],
    [time_left_for_this_task=VALUE],
    [overwrite={True|False}]
    )
```

The "__OPTIONS__" clause allows you to change the value of a parameter. The definition of each parameter is as follows.

- "target_col": the name of the column containing the target value of the classification model (str, default: 'target') 
- "features_to_drop": selects columns that cannot be used for training (list[str], optional)
- "impute_type": determines how empty values ​​(NaNs) are handled (str, optional, 'simple'|'iterative' , default: 'simple') 
> "simple": for empty values, categorical variables are treated as the most common value and continuous variables are treated as the mean  
> "iterative": applies an algorithm that predicts empty values with the remaining properties
- "datetime_attribs": selects columns corresponding to the date (list[str], optional)
- "outlier_method": determines how outliers are handled in the table. If None, the table includes outliers (str, optional, 'knn'|'iso'|'pca', default: None)
> "knn": use a K-NN-based approach to detect abnormal samples based on the distance between each data  
> "iso": use Isolation Forest to randomly branch the data table on a tree basis, isolate all observations, and detect abnormal samples (Works efficiently on datasets with many variables)  
> "pca": detect abnormal samples by reducing and restoring dimensions using the Principal Component Analysis(PCA)
- "time_left_for_this_task": the total time given to find a suitable classification model in seconds (int, optional, default: 60)
- "overwrite": determines whether to overwrite a model if it already exists. If set as True, the old model is replaced with the new model (bool, optional, True|False, default: False)


__BUILD MODEL Example__

An example "__BUILD MODEL__" query can be found in  [Create a Classification Model Using AutoML](/en/tutorials/Thanosql_ml/classification/automl_classification/).

```sql
%%thanosql
BUILD MODEL titanic_automl_classification
USING AutomlClassifier 
OPTIONS (
    target_col='survived', 
    impute_type='iterative',  
    features_to_drop=['name', 'ticket', 'passengerid', 'cabin'],
    time_left_for_this_task=300,
    overwrite=True
    ) 
AS 
SELECT * 
FROM titanic_train
```


## __FIT MODEL Syntax__

Use the "__FIT MODEL__" statement to retrain an AI models. The "__FIT MODEL__" statement allows you to retrain a model using datasets defined with the query_expr that comes after the "__AS__" clause. In this case, the label of the data used for retraining should be the same as the label used for the previous training.

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

__OPTIONS Clause__

```sql
OPTIONS (
    (target_col=column_name),
    [features_to_drop=[column_name, ...]],
    [impute_type={'simple'|'iterative'}],
    [datetime_attribs=[column_name, ...]],
    [outlier_method={'knn'|'iso'|'pca'}],
    [time_left_for_this_task=VALUE],
    [overwrite={True|False}]
    )
```

The "__OPTIONS__" clause allows you to change the value of a parameter. The definition of each parameter is as follows.

- "target_col": the name of the column containing the target value of the classification model (str, default: 'target') 
- "features_to_drop": selects columns that cannot be used for training (list[str], optional)
- "impute_type": determines how empty values ​​(NaNs) are handled (str, optional, 'simple'|'iterative' , default: 'simple') 
> "simple": for empty values, categorical variables are treated as the most common value and continuous variables are treated as the mean  
> "iterative": applies an algorithm that predicts empty values with the remaining properties
- "datetime_attribs": selects columns corresponding to the date (list[str], optional)
- "outlier_method": determines how outliers are handled in the table. If None, the table includes outliers (str, optional, 'knn'|'iso'|'pca', default: None)
> "knn": use a K-NN-based approach to detect abnormal samples based on the distance between each data  
> "iso": use Isolation Forest to randomly branch the data table on a tree basis, isolate all observations, and detect abnormal samples (Works efficiently on datasets with many variables)  
> "pca": detect abnormal samples by reducing and restoring dimensions using the Principal Component Analysis(PCA)
- "time_left_for_this_task": the total time given to find a suitable classification model in seconds (int, optional, default: 60)
- "overwrite": determines whether to overwrite a model if it already exists. If set as True, the old model is replaced with the new model (bool, optional, True|False, default: False)



## __PREDICT Syntax__

Use the "__PREDICT__" statement to apply AI models to perform prediction, classification, recommendation, and more. The "__PREDICT__" statement can preprocess the dataset defined by the query_expr that comes after the "__AS__" clause.

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


__OPTIONS Clause__

```sql
OPTIONS (
    [result_col=column_name],
    [table_name=expression] 
    )
```

The "__OPTIONS__" clause allows you to change the value of a parameter. The definition of each parameter is as follows.

- "result_col": the column that contains the predicted results (str, optional, default: 'predict_result')
- "table_name": the table name to be stored in the ThanoSQL workspace database. If a previously used table is specified, the existing table will be replaced by the new table with a 'predict_result' column. If not specified, the result dataframe will not be saved as a table (str, optional)


__PREDICT Example__

An example "__PREDICT__" query can be found in [Create a Classification Model Using AutoML](/en/tutorials/Thanosql_ml/classification/automl_classification/).

```sql
%%thanosql 
PREDICT USING titanic_automl_classification
OPTIONS (
    result_col='predict_result',
    table_name='titanic_test'
    )
AS 
SELECT * 
FROM titanic_test
```



## __EVALUATE Syntax__

Use the "__EVALUATE__" statement to evaluate the AI model. The "__EVALUATE__" expression evaluates a model using the dataset defined by the query_expr that comes after the "__AS__" clause.

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

__OPTIONS clause__

```sql
OPTIONS (
    (target_col=column_name)
    )
```

The "__OPTIONS__" clause allows you to change the value of a parameter. The definition of each parameter is as follows.

- "target_col": the name of the column containing the target value of the classification model (str, default: 'target')


__EVALUATE Example__

An example "__EVALUATE__" query can be found in [Create a Classification Model Using AutoML](/en/tutorials/Thanosql_ml/classification/automl_classification/).

```sql
%%thanosql 
EVALUATE USING titanic_automl_classification 
OPTIONS (
    target_col='survived'
    )
AS
SELECT *
FROM titanic_train
```