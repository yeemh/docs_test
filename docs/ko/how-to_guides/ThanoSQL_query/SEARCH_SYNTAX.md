---
title: SEARCH
---

# __SEARCH__

## __1. SEARCH 문__
"__SEARCH__" 쿼리 구문은 비정형 데이터에서 내용이나 의미 또는 유사도 등을 검색합니다.

## __2. SEARCH 구문__

```sql
%%thanosql

SEARCH [사용자 지정 데이터 테이블 이름]
USING [사용할 인공지능 모델]
AS [사용할 데이터 세트]
```

## __3. SEARCH 예시__

아래 쿼리는 이미지 수치화 인공지능 모델인 `Color_Descriptor`를 사용하여 유사 이미지에 대한 검색을 진행합니다. 

```sql
%%thanosql
SEARCH IMAGE images='tutorial/image_search/images/20150617_132435.jpg' 
USING Color_Descriptor 
AS 
SELECT * 
FROM color_descriptor_table_test
```
[![IMAGE](/img/thanosql_syntax/query/SEARCH/SEARCH_img1.png)](/img/thanosql_syntax/query/SEARCH/SEARCH_img1.png)
