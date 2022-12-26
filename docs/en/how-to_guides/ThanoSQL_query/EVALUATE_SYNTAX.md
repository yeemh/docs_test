---
title: EVALUATE
---

# __EVALUATE__

## __1. EVALUATE Statement__

The "__EVALUATE__" statement allows users to evaluate performance of their models.

## __2. EVALUATE Syntax__ 

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

!!! warning
    - If the dataset you want to use does not contain a target, it is not possible to evaluate the performance of the model.

## __3. EVALUATE Example__

!!! note
    - Examples are specific to one model, and the required option values ​​or the dataset used may differ from model to model. For a detailed description of each model, refer to the [ThanoSQL Model Statement Reference](/en/how-to_guides/reference/#thanosql-model-statement-reference)
    - Because the example only works when a specific model and dataset are present, it may not run normally if copied and used as is.

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

!!! faq "__Metrics__"
    - Depending on the situation, metrics can differ for each model. For example, simple classification with two target values and multiple classification with three or more target values will use different evaluation metrics such as the ones listed below.


| Model      | Metrics                     |
| :-----------: | :-----------------------------------------------: |
| `AutomlClassifier` (simple classification) | Accuracy, ROCAUC, Recall, Precision, f1-score, Kappa, MCC  |
| `AutomlClassifier` (multiple classification)       | Accuracy, macro-Recall, macro-Precision, macro-f1-score|
| `AutomlRegressor`    | MAE, MSE, R2, RMSLE, MAPE|


!!! note "AI models that can be used with '__EVALUATE__ statement'"
    - AutoML Classification model - AutomlClassifier
    - AutoML Regression model - AutomlRegressor
    - ConvNeXT Model - ConvNeXt_Tiny, ConvNeXt_Base
    - EfficientNet Model - EfficientNetV2S, EfficientV2M
    - Albert Model - AlbertKo, AlbertEn
    - Electra Model - ElectraKo, ElectraEn
    - Wav2Vec2 Model - Wav2Vec2Ko, Wav2Vec2En
    - Whisper Model - Whisper