---
title: BUILD MODEL 
---

# __BUILD MODEL__

## __1. BUILD MODEL Statement__

The "__BUILD MODEL__" statement enables users to create a desired AI model without any expertise in data science. 

## __2. BUILD MODEL Syntax__

```sql
%%thanosql
BUILD MODEL [custom_model_name]
USING [AI_model_to_use]
OPTIONS ([option_values_​​required_when_creating_an_AI_model])
AS
[dataset_to_use]

```

!!! NOTE
    Option values used in the "__OPTIONS__" clause are different for each AI model. Specific values needed for each model are listed in [reference](/en/how-to_guides/reference/).

## __3. BUILD MODEL Example__

### __3-1. Using the Auto_ML Model to Create a Classification Model__
The example below demonstrates how to use ThanoSQL's ["AutomlClassifier"](/en/how-to_guides/ThanoSQL_model/AutomlClassifier/) and the "__BUILD MODEL__" statement to create a user-defined <mark style="background-color:#E9D7FD ">Titanic classification</mark> model. If you are interested in learning more about the entire procedure, check out [Create a classification model using AutoML](/en/tutorials/thanosql_ml/classification/automl_classification/).


```sql
%%thanosql
BUILD MODEL titanic_classification
USING AutomlClassifier
OPTIONS (
    target='survived',
    impute_type='iterative',
    features_to_drop=["name", 'ticket', 'passengerid', 'cabin'],
    overwrite = True
    )
AS
SELECT *
FROM titanic_train
```

!!! note "AI models that can be used with '__BUILD MODEL__ statement'"
    - Auto-ML Classification model - AutomlClassifier
    - Auto-ML Regression model - AutomlRegressor
    - ConvNeXT Model - ConvNeXt_Tiny , ConvNeXt_Base
    - EfficientNet Model - EfficientNetV2S , EfficientV2M
    - Albert Model - AlbertKo, AlbertEn
    - Electra Model - ElectraKo , ElectraEn
    - Wav2Vec2 Model - Wav2Vec2Ko , Wav2Vec2En
