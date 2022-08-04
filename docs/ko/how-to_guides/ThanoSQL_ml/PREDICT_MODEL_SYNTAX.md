---
title: 모델 적용하기
---

# __모델 적용하기 (PREDICT USING)__

## 시작 전 사전 정보

- 마지막 수정날짜 : {{ git_revision_date_localized }}

## __1. PREDICT USING 구문 개요__

사용자는 "__PREDICT USING__" 구문을 사용하여 테스트 데이터 세트에 인공지능 모델을 적용하여 예측, 분류, 추천 등의 작업을 수행할 수 있습니다.  

## __2. PREDICT USING 구문__ 

```sql
%%thanosql
PREDICT USING [기존 학습한 모델 이름]
OPTIONS ([모델 별 추론시 필요한 옵션값])
AS
[사용할 테스트 데이터 세트]
```

## __3. PREDICT USING 구문 예시__ 
[텍스트 분류 모델 만들기](/tutorials/thanosql_ml/classification/classification_Electra/)에서 해당 알고리즘 쿼리 구문 사용 예시를 확인하실 수 있습니다.

```sql
%%thanosql
PREDICT USING my_movie_review_classifier
OPTIONS (
    text_col='review'
    )
AS
SELECT *
FROM movie_review_test```
```

!!! note "__쿼리 세부 정보__" 

    "__OPTIONS__" 절은 이미지 모델에서 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.
    - "text_col" : 데이터 테이블에서 분류의 대상이 될 텍스트를 담은 컬럼을 설정합니다. (DEFAULT : "text")


