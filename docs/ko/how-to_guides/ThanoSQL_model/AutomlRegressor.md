---
title: AutomlRegressor
---

# __AutomlRegressor__

__표기법 규칙__ 

- 괄호 `()`는 ^^리터럴^^ 괄호를 나타냅니다.  
- 중괄호 {}는 옵션 조합을 묶는 데 사용됩니다.  
- 대괄호 `[]`는 선택적 절을 나타냅니다.   
- 대괄호 [ , ... ] 안에 있는 쉼표 다음에 오는 줄임표는 앞의 항목이 쉼표로 구분된 
목록으로 반복될 수 있음을 의미합니다.
- 세로 막대 `|`는 논리 `OR`를 나타냅니다.  
- VALUE는 값을 의미합니다. 

!!! note "" 
    __리터럴__ : 고정되거나 변경할 수 없는 값을 의미하며 상수(Constant)라고도 불립니다. 
    > 각 리터럴은 테이블에서 컬럼과 같은 특별한 자료형을 가지고 있습니다.


## __BUILD MODEL 구문__ 

이 "__BUILD MODEL__" 구문을 사용하여 인공지능 모델을 개발할 수 있습니다. 
"__BUILD MODEL__" 표현식은 "__AS__" 뒤에 나오는 query_expr을 통해 정의된 데이터 세트를 학습할 수 있습니다. 

``` sql
query_statement:
    query_expr

BUILD MODEL (model_name_expression) 
USING AutomlRegressor
OPTIONS (
    expression [ , ...]
    )
AS 
(query_expr)
``` 

!!! faq ""
    본 쿼리를 통해서 USING 뒤에 나오는 베이스 인공지능 모델을 model_name_expression 이름으로 저장합니다. 


__OPTIONS 절__

```sql
OPTIONS(
    target = column_name,
    [impute_type = {"simple" | "iterative"}],
    [features_to_drop = [column_name, ...]],
    [datetime_attribs = [column_name, ...]],
    [outlier_method = {"pca" | "iso" | "knn"}],
    [time_left_for_this_task = VALUE],
    [overwrite = {True | False}]
    )
```

"__OPTIONS__" 절은 AutomlRegressor 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다. 

- "target" : 데이터 테이블에서 회귀 예측 모델에 목푯값이 되는 컬럼을 설정합니다.
- "impute_type" : 데이터 테이블에서 빈 값을 처리하는 방법을 설정합니다. (DEFAULT : "simple")
> "simple" : 빈 값에 대해 범주형 변수는 최빈값으로, 연속형 변수는 평균으로 처리합니다.  
> "iterative" : 빈 값에 대해 나머지 속성을 통해 예측하는 알고리즘을 적용하여 처리합니다.
- "features_to_drop" : 데이터 테이블에서 학습에 이용하지 못하는 컬럼을 설정합니다.
- "datetime_attribs" : 데이터 테이블에서 날짜에 해당하는 컬럼을 설정합니다. 
- "outlier_method" : 데이터 테이블에서 이상치 처리하는 방법을 설정합니다. (DEFAULT : "iso")
> "pca" : 주어진 데이터 테이블에 대해서 Principal Component Analysis(PCA, 주성분 분석)를 이용하여 차원을 축소하고 복원을 하는 과정을 통해 비정상 샘플을 검출합니다.  
> "iso" : 주어진 데이터 테이블에 대해서 Isolation Forest를 사용하여 트리 기반으로 랜덤하게 데이터 테이블을 분기하며 모든 관측치를 고립시키며 비정상 샘플을 검출합니다. (변수가 많은 데이터 세트에서도 효율적으로 작동합니다)  
> "knn" : K-NN 기반 접근법으로 각 데이터 사이의 거리를 기반으로 비정상 샘플을 검출합니다.
- "time_left_for_this_task" : 적합한 회귀 예측 모델을 찾는데 소요되는 시간을 의미합니다. 값이 클수록 적합한 모델을 찾을 가능성이 커집니다.(DEFAULT : 300)
- "overwrite" : 동일 이름의 모델이 존재하는 경우 덮어쓰기 가능 유무를 설정합니다. True일 경우 기존 모델은 새로운 모델로 변경됩니다. (DEFAULT : False)


__BUILD MODEL 예시__

[Auto-ML을 사용하여 예측 모델 만들기](/tutorials/thanosql_ml/regression/automl_regression/)에서 해당 알고리즘 구문 사용 예시를 확인하실 수 있습니다. 

```sql
%%thanosql
BUILD MODEL bike_regression
USING AutomlRegressor
OPTIONS (
    target='count', 
    impute_type='simple', 
    datetime_attribs=['datetime'],
    overwrite = True
    ) 
AS
SELECT *
FROM bike_sharing_train
```

## __FIT MODEL 구문__

이 "__FIT MODEL__" 구문을 사용하여 인공지능 모델을 재학습할 수 있습니다. "__FIT MODEL__" 표현식은 "__AS__" 뒤에 나오는 query_expr을 통해 정의된 데이터 세트를 재학습할 수 있습니다.

``` sql
query_statement:
    query_expr

FIT MODEL (model_name_expression) 
USING (model_name_expression)
OPTIONS (
    expression [ , ...]
    )
AS 
(query_expr)
``` 

!!! faq ""
    본 쿼리를 통해서 USING 뒤에 나온 인공지능 모델을 model_name_expression 이름으로 저장하고 같은 이름에 새로운 모델을 저장합니다.

 __OPTIONS 절__

```sql
OPTIONS(
    target = column_name,
    [impute_type = {"simple" | "iterative"}],
    [features_to_drop = [column_name, ...]],
    [datetime_attribs = [column_name, ...]],
    [outlier_method = {"pca" | "iso" | "knn"}],
    [time_left_for_this_task = VALUE],
    [overwrite = {True | False}]
    )
```

"__OPTIONS__" 절은 AutomlRegressor 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다. 

- "target" : 데이터 테이블에서 회귀 예측 모델에 목푯값이 되는 컬럼을 설정합니다.
- "impute_type" : 데이터 테이블에서 빈 값을 처리하는 방법을 설정합니다. (DEFAULT : "simple")
> "simple" : 빈 값에 대해 범주형 변수는 최빈값으로, 연속형 변수는 평균으로 처리합니다.  
> "iterative" : 빈 값에 대해 나머지 속성을 통해 예측하는 알고리즘을 적용하여 처리합니다.
- "features_to_drop" : 데이터 테이블에서 학습에 이용하지 못하는 컬럼을 설정합니다.
- "datetime_attribs" : 데이터 테이블에서 날짜에 해당하는 컬럼을 설정합니다. 
- "outlier_method" : 데이터 테이블에서 이상치 처리하는 방법을 설정합니다. (DEFAULT : "iso")
> "pca" : 주어진 데이터 테이블에 대해서 Principal Component Analysis(PCA, 주성분 분석)를 이용하여 차원을 축소하고 복원을 하는 과정을 통해 비정상 샘플을 검출합니다.  
> "iso" : 주어진 데이터 테이블에 대해서 Isolation Forest를 사용하여 트리 기반으로 랜덤하게 데이터 테이블을 분기하며 모든 관측치를 고립시키며 비정상 샘플을 검출합니다. (변수가 많은 데이터 세트에서도 효율적으로 작동합니다)  
> "knn" : K-NN 기반 접근법으로 각 데이터 사이의 거리를 기반으로 비정상 샘플을 검출합니다.
- "time_left_for_this_task" : 적합한 회귀 예측 모델을 찾는데 소요되는 시간을 의미합니다. 값이 클수록 적합한 모델을 찾을 가능성이 커집니다.(DEFAULT : 300)
- "overwrite" : 동일 이름의 모델이 존재하는 경우 덮어쓰기 가능 유무를 설정합니다. True일 경우 기존 모델은 새로운 모델로 변경됩니다. (DEFAULT : False)


 __FIT MODEL 예시__

[FIT MODEL](/how-to_guides/ThanoSQL_query/FIT_MODEL_SYNTAX/)에서 해당 알고리즘 구문 사용 예시를 확인하실 수 있습니다. 

```sql
%%thanosql
FIT MODEL fit_test_regressor
USING bike_regression
OPTIONS (
    target = 'count',
    impute_type='simple',
    datetime_attribs = ['datetime'],
    time_left_for_this_task=300,
    overwrite = True
    )
AS
SELECT *
FROM bike_sharing_train
```

## __TRANSFORM 구문__

이 "__TRANSFORM__" 구문을 사용하여 테스트 데이터 세트에 인공지능 모델 생성시 사용한 동일한 전처리 방법을 적용할 수 있습니다. "__TRANSFORM__" 표현식은 "__AS__" 뒤에 나오는 query_expr을 통해 정의한 데이터 세트를 전처리할 수 있습니다.

``` sql
query_statement:
    query_expr

TRANSFORM USING (model_name_expression) 
AS 
(query_expr)
``` 

!!! faq ""
    본 쿼리를 통해서 USING 뒤에 나오는 인공지능 모델인 model_name_expression 을 사용합니다.

 __TRANSFORM 예시__

[TRANSFORM](/how-to_guides/ThanoSQL_query/TRANSFORM_MODEL_SYNTAX/)에서 해당 알고리즘 구문 사용 예시를 확인하실 수 있습니다. 

```sql
%%thanosql
TRANSFORM USING bike_regression 
AS 
SELECT * 
FROM bike_sharing_test 
```

## __PREDICT 구문__

이 "__PREDICT__" 구문을 사용하여 테스트 데이터 세트에 인공지능 모델을 적용하여 예측, 분류, 추천 등의 작업을 수행할 수 있습니다. "__PREDICT__" 표현식은 "__AS__" 뒤에 나오는 query_expr을 통해 정의한 데이터 세트를 전처리할 수 있습니다.

``` sql
query_statement:
    query_expr

PREDICT USING (model_name_expression) 
AS 
(query_expr)
``` 
!!! faq ""
    본 쿼리를 통해서 USING 뒤에 나오는 인공지능 모델을 사용합니다.

 __PREDICT 예시__

[Auto-ML을 사용하여 예측 모델 만들기](/tutorials/thanosql_ml/regression/automl_regression/)에서 해당 알고리즘 구문 사용 예시를 확인하실 수 있습니다. 

```sql
%%thanosql
PREDICT USING bike_regression 
AS
SELECT *
FROM bike_sharing_test
```
## __EVALUATE 구문__

이 "__EVALUATE__" 구문을 사용하여 인공지능 모델에 대한 평가 작업을 수행할 수 있습니다. "__EVALUATE__" 표현식은 "__AS__" 뒤에 나오는 query_expr을 통해 정의한 데이터 세트를 평가합니다.

``` sql
query_statement:
    query_expr

EVALUATE USING (model_name_expression) 
OPTIONS (
    expression
    )
AS 
(query_expr)
``` 

!!! faq ""
    본 쿼리를 통해서 USING 뒤에 나오는 인공지능 모델을 사용합니다.

 __OPTIONS 절__

```sql
OPTIONS(
    target = column_name
    )
```

"__OPTIONS__" 절은 AutomlRegressor의 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다. 

- "target" : 데이터 테이블에서 회귀 예측 모델에 목푯값이 되는 컬럼을 설정합니다.

 __EVALUATE 예시__

[Auto-ML을 사용하여 예측 모델 만들기](/tutorials/thanosql_ml/regression/automl_regression/)에서 해당 알고리즘 구문 사용 예시를 확인하실 수 있습니다. 

```sql
%%thanosql
EVALUATE USING bike_regression 
OPTIONS (
    target='count'
    ) 
AS
SELECT *
FROM bike_sharing_train
```

