---
title: EVALUATE
---

# __EVALUATE__

## __1. EVALUATE 문__

사용자는 "__EVALUATE__" 구문을 사용하여 인공지능 모델에 대한 성능을 평가할 수 있습니다.  

## __2. EVALUATE 구문__ 
```sql
%%thanosql
EVALUATE USING [기존 학습한 모델 이름]
OPTIONS ([모델 별 평가시 필요한 옵션값])
AS
[사용할 데이터 세트]
```
!!! warning
    사용할 데이터 세트에 목푯값(target)이 없을 경우, 모델에 대한 성능을 평가할 수 없습니다. 

## __3. EVALUATE 예시__ 
아래 예는 "__EVALUATE__" 구문을 사용하여 사용자가 [BUILD MODEL](/how-to_guides/ThanoSQL_query/BUILD_MODEL_SYNTAX/)에서 만들었던 <mark style="background-color:#E9D7FD ">test_classifier</mark> 분류 모델을 평가합니다.

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

!!! note "__쿼리 세부 정보__"   
    "__EVALUATE__" 쿼리 구문을 사용하여 구축한  <mark style="background-color:#E9D7FD ">titanic_classification</mark>이라는 모델을 평가합니다. "__OPTIONS__"의 "target"에는 분류 예측 모델에 목푯값이 되는 컬럼의 이름(<mark style="background-color:#D7D0FF">survived</mark>)을 적어줍니다.
    
!!! faq "__평가 지표 정보__"
    평가 지표의 경우, 상황별로 다르게 모델마다 설정되어 있습니다. 분류 모델의 경우, 목푯값이 2가지의 경우인 단순 분류와 3가지 이상인 경우의 다중 분류에 따라 서로 다른 평가 지표를 사용합니다. 회귀 모델의 경우 항상 같은 평가 지표를 사용합니다.

    | Model      | 사용하는 평가 지표                          |
    | :-----------: | :-----------------------------------------------: |
    | `AutomlClassifier`(단순 분류) | Accuracy, ROCAUC, Recall, Precision, f1-score, Kappa, MCC  |
    | `AutomlClassifier`(다중 분류)       | Accuracy, macro-Recall, macro-Precision, macro-f1-score|
    | `AutomlRegressor`    | MAE, MSE, R2, RMSLE, MAPE|