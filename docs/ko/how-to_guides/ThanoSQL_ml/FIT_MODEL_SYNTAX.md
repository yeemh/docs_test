---
title: 모델 재학습하기
---

# __모델 재학습하기 (FIT MODEL)__

## 시작 전 사전 정보

- 마지막 수정날짜 : {{ git_revision_date_localized }}

## __1. FIT MODEL 구문 개요__ 

사용자는 데이터 과학(Data Science)에 대한 전문 지식이 없어도 간단하게 "__FIT MODEL__" 구문을 사용하여 모델에 새롭게 추가된 데이터 세트를 사용하여 학습할 수 있습니다.

## __2. FIT MODEL 구문__
```sql
%%thanosql
FIT MODEL [사용자 지정 모델 이름]
USING [기존 학습한 모델 이름 | 사전 학습된 인공지능 모델 이름 ]
OPTIONS ([, 인공지능 모델을 만들 때 필요한 옵션값])
AS
[사용할 데이터 세트]
``` 
!!! NOTE
    "__OPTIONS__"에서 사용하는 옵션값은 사용할 인공지능 모델에 따라 다르게 적용됩니다. 각 인공지능 모델에 대한 "__OPTIONS__"는 [다음 문서](/how-to_guides/modelling/OPTIONS/)에서 확인 가능합니다.

!!! warning
    다만 Auto-ML 모델의 경우, 기존 인공지능 모델이 가지고 있던 파라미터 값을 사용하는 것이 아닌 데이터 세트만 바꿔 "__OPTIONS__"에 사용된 옵션값에 따른 모델을 만듭니다.

## __3. FIT MODEL 구문 예시__
아래 예는 "__FIT MODEL__" 구문을 사용하여 사용자가 이전에 만들었던 <mark style="background-color:#E9D7FD ">test_classifier</mark> 모델에 새롭게 추가된 데이터 세트를 사용하여 학습한  <mark style="background-color:#E9D7FD ">fit_test_classifier</mark> 모델을 만듭니다.

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