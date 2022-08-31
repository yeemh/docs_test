---
title: 모델 학습하기
---

# __모델 학습하기 (BUILD MODEL)__ 

## 시작 전 사전 정보

- 마지막 수정날짜 : {{ git_revision_date_localized }}

## __1. BUILD MODEL 구문 개요__

사용자는 데이터 과학(Data Science)에 대한 전문 지식이 없어도 간단하게 "__BUILD MODEL__" 구문을 사용하여 원하는 인공지능 모델을 개발할 수 있습니다.

## __2. BUILD MODEL 구문__

```sql
%%thanosql
BUILD MODEL [사용자 지정 모델 이름]
USING [사용할 인공지능 모델]
OPTIONS ([인공지능 모델을 만들 때 필요한 옵션값])
AS 
[사용할 데이터 세트]

```

!!! NOTE
    "__OPTIONS__"에서 사용하는 옵션값은 사용할 인공지능 모델에 따라 다르게 적용됩니다. 각 인공지능 모델에 대한 "__OPTIONS__"는 [다음 문서](/how-to_guides/OPTIONS/)에서 확인 가능합니다.

## __3. BUILD MODEL 구문 예시__
### __3-1. 분류 모델 생성을 위한 Auto_ML 모델 사용__

아래 예는 "__BUILD MODEL__" 구문을 사용하여 사용자가 정의한 <mark style="background-color:#E9D7FD ">titanic_classification</mark> 모델을 ThanoSQL에서 제공하는 ["AutomlClassifier"](https://www.automl.org/automl/) 모델을 사용하여 분류 모델을 만듭니다. 전체 과정이 궁금하다면, [Auto-ML을 사용하여 타이타닉 생존자 분류 모델 만들기](/tutorials/thanosql_ml/classification/automl_classification/)를 진행해 보세요.

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

!!! note "'__BUILD MODEL__'을 통해 사용 가능한 인공지능 모델"
    - Auto-ML 분류 모델 - AutomlClassifier
    - Auto-ML 회귀 모델 - AutomlRegressor
    - ConvNeXT 모델 - ConvNeXt_Tiny , ConvNeXt_Base
    - EfficientNet 모델 - EfficientNetV2S , EfficientV2M
    - Albert 모델 - AlbertKo, AlbertEn 
    - Electra 모델 - ElectraKo , ElectraEn
    - Wav2Vec2 모델 - Wav2Vec2Ko , Wav2Vec2En


