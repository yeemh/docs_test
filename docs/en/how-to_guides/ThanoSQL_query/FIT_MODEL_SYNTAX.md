---
title: FIT MODEL
---

# __FIT MODEL__

## __1. FIT MODEL Statement__

The "__FIT MODEL__" statement allows users to retrain a model with new data without having any expertise in data science.

## __2. FIT MODEL Syntax__

```sql
%%thanosql
FIT MODEL [custom_model_name]
USING [built_model_name | pre-trained_AI_model_name ]
OPTIONS ([option_values_required_when_creating_an_AI_model])
AS
[dataset_to_use]
```

!!! NOTE
    Option values used in the "__OPTIONS__" clause are different for each model. Specific values needed for each model are listed in [reference](/en/how-to_guides/reference/). 

!!! warning
    The Auto-ML model is retrained by exclusively switching the dataset instead of changing the parameter values of the model to keep consistency with option values in the "__OPTIONS__" clause.
## __3. FIT MODEL Example__
The example below demonstrates how to create a <mark style="background-color:#E9D7FD ">fit_test_classifier</mark> model by retraining the <mark style="background-color:#E9D7FD ">test_classifier</mark> model with a new dataset and a "__FIT_MODEL__" statement. 

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
