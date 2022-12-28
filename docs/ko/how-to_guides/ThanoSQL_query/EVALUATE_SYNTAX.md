---
title: EVALUATE
---

# __EVALUATE__

## __1. EVALUATE 문__

사용자는 "__EVALUATE__" 구문을 사용하여 인공지능 모델에 대한 성능을 평가할 수 있습니다.

## __2. EVALUATE 구문__

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
    - 사용할 데이터 세트에 목푯값(target)이 없을 경우, 모델에 대한 성능을 평가할 수 없습니다.

## __3. EVALUATE 예시__

!!! note
    - 예시는 한 모델에 특정된 것으로 필요한 옵션 값이나 사용되는 데이터 세트는 모델별로 다를 수 있습니다. 각 모델에 대한 자세한 설명은 [ThanoSQL Model Statement Reference](/ko/how-to_guides/reference/#thanosql-model-statement-reference)를 참고해 주세요.
    - 예시는 특정 모델과 데이터 세트가 존재해야만 작동하므로 그대로 복사하여 사용할 시 정상적으로 실행되지 않을 수 있습니다.

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
    
!!! faq "__평가 지표 정보__"
    - 평가 지표의 경우, 상황별로 다르게 모델마다 설정되어 있습니다. 예를 들어, 분류 모델의 경우 목푯값이 2가지의 경우인 단순 분류와 3가지 이상의 경우인 다중 분류에 따라 서로 다른 평가 지표를 사용합니다. 

    | Model      | 사용하는 평가 지표                          |
    | :-----------: | :-----------------------------------------------: |
    | `AutomlClassifier`(단순 분류) | Accuracy, ROCAUC, Recall, Precision, f1-score, Kappa, MCC|
    | `AutomlClassifier`(다중 분류)       | Accuracy, macro-Recall, macro-Precision, macro-f1-score|
    | `AutomlRegressor`    | MAE, MSE, R2, RMSLE, MAPE|

!!! note "'__EVALUATE__ 문'을 사용할 수 있는 인공지능 모델"
    - AutoML 분류 모델 - AutomlClassifier
    - AutoML 회귀 모델 - AutomlRegressor
    - ConvNeXT 모델 - ConvNeXt_Tiny, ConvNeXt_Base
    - EfficientNet 모델 - EfficientNetV2S, EfficientV2M
    - Albert 모델 - AlbertKo, AlbertEn
    - Electra 모델 - ElectraKo, ElectraEn
    - Wav2Vec2 모델 - Wav2Vec2Ko, Wav2Vec2En
    - Whisper 모델 - Whisper