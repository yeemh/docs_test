---
title: 비정형 특성 추가하기
---

# __비정형 특성 추가하기 (CONVERT USING)__

## 시작 전 사전 정보

- 마지막 수정날짜 : {{ git_revision_date_localized }}

## __1. CONVERT USING 쿼리 구문 개요__

사용자는 "__CONVERT USING__"  구문은 이미지, 비디오, 음성 등 비정형 데이터의 정보를 이용해서 수치화 알고리즘을 사용하여 벡터 형식으로 변환하고 이 값을 사용할 데이터 세트에 추가합니다.

## __2. CONVERT USING 쿼리 구문__

```sql
CONVERT USING [사용할 인공지능 모델]
OPTIONS(
    table_name=[저장될 테이블 명]
    )
AS 
[사용할 데이터 세트]
```

## __3. CONVERT USING 쿼리 구문 예시__ 

### __3.1 `Color_descriptor` 알고리즘을 사용한 이미지 수치화__ 
아래 예는 [비정형 데이터 변환하기(CREATE TABLE)](/how-to_guides/Thanosql_query/CREATE_TABLE_SYNTAX/)에서 만들었던 색 특징 추출 모델을 사용하여 수치화한 결과를 ThanoSQL DB 에 저장되어 있는 "color_descriptor_table_test" 테이블에 새로운 칼럼을 추가하여 저장합니다.

```sql
%%thanosql
CONVERT USING Color_Descriptor
OPTIONS(
    table_name= "color_descriptor_table_test"
    )
AS 
SELECT * 
FROM color_descriptor_table_test
```
[![IMAGE](/img/thanosql_syntax/query/CONVERT/img1.png)](/img/thanosql_syntax/query/CONVERT/img1.png)

### __3.2 `clip_en` 알고리즘을 사용한 이미지 수치화__
아래 예는 `clip_en` 알고리즘을 사용하여 수치화한 결과를 ThanoSQL DB 상에 저장된 "mnist_dataset" 테이블에 새로운 칼럼을 추가하여 저장합니다.
```sql
%%thanosql
CONVERT USING clip_en
OPTIONS(
    image_col='image_path', 
    table_name='mnist_dataset', 
    batch_size=128
    )
AS 
SELECT * 
FROM mnist_dataset
```