---
title: 모델 적용을 위해 데이터 전처리하기
---

# __모델 적용을 위해 데이터 전처리하기 (TRANSFORM USING)__

## 시작 전 사전 정보

- 마지막 수정날짜 : {{ git_revision_date_localized }}

## __1. TRANSFORM USING 구문 개요__

사용자는 "__TRANSFORM USING__" 구문을 사용하여 테스트 데이터 세트에 인공지능 모델 생성시 사용한 동일한 전처리 방법을 적용할 수 있습니다. 

## __2. TRANSFORM USING 구문__ 
```sql
%%thanosql
TRANSFORM USING [기존 학습한 모델 이름]
AS
[사용할 테스트 데이터 세트]
```

## __3. TRANSFORM USING 구문 예시__ 
아래 예는 "__TRANSFORM USING__" 구문을 사용하여 사용자가 [모델 학습하기](/how-to_guides/ThanoSQL_ml/BUILD_MODEL_SYNTAX/)에서 만들었던 <mark style="background-color:#E9D7FD ">test_classifier</mark> 분류 모델과 동일한 처리를  <mark style="background-color:#FFEC92 ">titanic_test</mark> 데이터 세트에 적용합니다.

```sql
%%thanosql
TRANSFORM USING test_classifier 
AS 
SELECT * 
FROM titanic_test 
```
