---
title: PREDICT
---

# __PREDICT__

## __1. PREDICT Statement__

The "__PREDICT__" statement allows users to apply models to perform prediction, classification, recommendation, and more.


## __2. PREDICT Syntax__

```sql
%%thanosql
PREDICT USING [built_model_name]
OPTIONS ([option_values_required_for_inference])
AS
[test_dataset_to_use]
```

## __3. PREDICT Example__
You can find the example of the following query statement in the [Create a text classification model](/en/tutorials/thanosql_ml/classification/text_classification/).

````sql
%%thanosql
PREDICT USING my_movie_review_classifier
OPTIONS (
    text_col='review'
    )
AS
SELECT *
FROM movie_review_test```
````

!!! note "__Query Details__"
    - The "__OPTIONS__" clause can change the value of a parameter from the default value in an image model. The meaning of each parameter is as follows:
        - "text_col" : Column containing the text to be classified by the model. (DEFAULT : "text")
