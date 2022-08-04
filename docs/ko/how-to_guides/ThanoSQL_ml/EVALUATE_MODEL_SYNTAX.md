---
title: 모델 평가하기
---

# __모델 평가하기 (EVALUATE USING)__

## 시작 전 사전 정보

- 마지막 수정날짜 : {{ git_revision_date_localized }}

## __1. EVALUATE USING 구문 개요__

사용자는 "__EVALUATE USING__" 구문을 사용하여 인공지능 모델에 대한 성능을 평가할 수 있습니다.  

## __2. EVALUATE USING 구문__ 
```sql
%%thanosql
EVALUATE USING [기존 학습한 모델 이름]
OPTIONS ([모델 별 평가시 필요한 옵션값])
AS
[사용할 데이터 세트]
```
!!! warning
    사용할 데이터 세트에 목푯값(target)이 없을 경우, 모델에 대한 성능을 평가할 수 없습니다. 

## __3. EVALUATE USING 구문 예시__ 
아래 예는 "__EVALUATE USING__" 구문을 사용하여 사용자가 [모델 학습하기](/how-to_guides/modelling/BUILD_MODEL_SYNTAX/)에서 만들었던 <mark style="background-color:#E9D7FD ">test_classifier</mark> 분류 모델을 평가합니다.

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

![IMAGE](/img/thanosql_ml/classification/automl/img2.png)

!!! note "__쿼리 세부 정보__"   
    "__EVALUATE USING__" 쿼리 구문을 사용하여 구축한  <mark style="background-color:#E9D7FD ">titanic_classification</mark>이라는 모델을 평가합니다. "__OPTIONS__"의 "target"에는 분류 예측 모델에 목푯값이 되는 컬럼의 이름(<mark style="background-color:#D7D0FF">survived</mark>)을 적어줍니다.
    
