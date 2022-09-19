---
title: EVALUATE
---

# __EVALUATE__

## __1. EVALUATE Statement__

The "__EVALUATE__" statement allows users to evaluate performance of their models.

## __2. EVALUATE Syntax__ 

```sql
%%thanosql
EVALUATE USING [built_model_name]
OPTIONS ([option_value_required_for_evaluation])
AS
[dataset_to_use]
```

!!! warning
    If the dataset you want to use does not have a target, you cannot evaluate the performance of the model.

## __3. EVALUATE Example__
The example below demonstrates how to use the "__EVALUATE__" statement to evaluate the <mark style="background-color:#E9D7FD ">test_classifier</mark> model built with the [BUILD MODEL](/en/how-to_guides/ThanoSQL_query/BUILD_MODEL_SYNTAX/) statement.

```sql
%%thanosql
EVALUATE USING test_classifier
OPTIONS (
    target='survived'
    )
AS
SELECT *
FROM titanic_train
```

[![IMAGE](/img/thanosql_ml/classification/automl_classification/img2.png)](/img/thanosql_ml/classification/automl_classification/img2.png)

!!! note "__Query Details__"  
    For the "target" value in the "__OPTIONS__" clause, specify the name of the target column(<mark style="background-color:#D7D0FF">survived</mark>) of the classification model.

!!! faq "__Metrics__"
     Depending on the situation, metrics can differ for each model. For example, simple classification with two target values and multiple classification with three or more target values will use different evaluation metrics such as the ones listed below.


| Model      | Metrics                     |
| :-----------: | :-----------------------------------------------: |
| `AutomlClassifier` (simple classification) | Accuracy, ROCAUC, Recall, Precision, f1-score, Kappa, MCC  |
| `AutomlClassifier` (multiple classification)       | Accuracy, macro-Recall, macro-Precision, macro-f1-score|
| `AutomlRegressor`    | MAE, MSE, R2, RMSLE, MAPE|