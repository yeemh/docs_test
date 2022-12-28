---
title: BUILD MODEL
---

# __BUILD MODEL__

## __1. BUILD MODEL 문__

사용자는 데이터 과학에 대한 전문 지식이 없어도 "__BUILD MODEL__" 구문을 사용하여 원하는 인공지능 모델을 개발할 수 있습니다.

## __2. BUILD MODEL 구문__

```sql
query_statement:
    query_expr

BUILD MODEL (model_name_expression)
USING {model_name_expression}
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```

!!! note "쿼리 세부 정보"
    - "__OPTIONS__" 절에서 매개변수의 값을 기본값으로부터 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.
        - "overwrite": 동일 이름의 모델이 존재하는 경우 덮어쓰기 가능 유무를 설정합니다. True일 경우 기존 모델은 새로운 모델로 변경됩니다. (bool, optional, True|False, default: False)

!!! warning ""
    "__USING__" 뒤에 작성하는 model_name_expression은 대/소문자의 영향을 받습니다.

## __3. BUILD MODEL 예시__

!!! note
    - 예시는 한 모델에 특정된 것으로 필요한 옵션 값이나 사용되는 데이터 세트는 모델별로 다를 수 있습니다. 각 모델에 대한 자세한 설명은 [ThanoSQL Model Statement Reference](/ko/how-to_guides/reference/#thanosql-model-statement-reference)를 참고해 주세요.
    - 예시는 특정 모델과 데이터 세트가 존재해야만 작동하므로 그대로 복사하여 사용할 시 정상적으로 실행되지 않을 수 있습니다.

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

!!! note "'__BUILD MODEL__ 문'을 사용할 수 있는 인공지능 모델"
    - AutoML 분류 모델 - AutomlClassifier
    - AutoML 회귀 모델 - AutomlRegressor
    - ConvNeXT 모델 - ConvNeXt_Tiny, ConvNeXt_Base
    - EfficientNet 모델 - EfficientNetV2S, EfficientV2M
    - Albert 모델 - AlbertKo, AlbertEn
    - Electra 모델 - ElectraKo, ElectraEn
    - Wav2Vec2 모델 - Wav2Vec2Ko, Wav2Vec2En
    - SimCLR 모델 - SimCLR
    - SBERT 모델 - SBERTKo, SBERTEn