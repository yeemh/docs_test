---
title: 모델 삭제하기
---

# __모델 삭제하기 (DELETE MODEL)__

## 시작 전 사전 정보

- 마지막 수정날짜 : {{ git_revision_date_localized }}

## __1. DELETE MODEL 구문 개요__ 

사용자는 "__DELETE MODEL__" 구문을 사용하여 데이터 베이스에 만들어진 모델을 삭제할 수 있습니다. 

## __2. DELETE MODEL 구문__

```sql
%%thanosql
DELETE MODEL [삭제할 모델 이름]
```

## __3. DELETE MODEL 구문 예시__

아래 예는 "__DELETE MODEL__" 구문을 사용하여 사용자가 [모델 학습하기](/how-to_guides/modelling/BUILD_MODEL_SYNTAX/)에서 만들었던 <mark style="background-color:#E9D7FD ">titanic_classification</mark> 분류 모델을 데이터 베이스에서 삭제합니다.

```sql
%%thanosql
DELETE MODEL titanic_classification
```