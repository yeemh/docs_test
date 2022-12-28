---
title: LIST
---

# __LIST__

## __1. LIST 문__

사용자는 "__LIST__" 구문을 사용하여 가장 최신의 ThanoSQL의 Pre-built 모델("THANOSQL MODEL"), ThanoSQL의 데이터 세트("THANOSQL DATASET"), 사용자가 만든 데이터 테이블("TABLE")과 모델("MODEL") 리스트를 확인할 수 있습니다.

## __2. LIST 구문__

"__LIST MODEL__" 구문은 사용자가 만든 모델의 리스트를 확인합니다.

```sql
LIST MODEL
```
[![IMAGE](/img/thanosql_syntax/query/LIST/img1.png)](/img/thanosql_syntax/query/LIST/img1.png)

"__LIST THANOSQL MODEL__" 구문은 ThanoSQL Pre-built 모델의 리스트를 확인합니다.

```sql
LIST THANOSQL MODEL
```
[![IMAGE](/img/thanosql_syntax/query/LIST/img2.png)](/img/thanosql_syntax/query/LIST/img2.png)

"__LIST THANOSQL DATASET__" 구문은 ThanoSQL 데이터 세트의 리스트를 확인합니다.

```sql
LIST THANOSQL DATASET
```
[![IMAGE](/img/thanosql_syntax/query/LIST/img3.png)](/img/thanosql_syntax/query/LIST/img3.png)

"__LIST TABLE__" 구문은 사용자가 만든 데이터 테이블의 리스트를 확인합니다.

```sql
LIST TABLE
```
[![IMAGE](/img/thanosql_syntax/query/LIST/img4.png)](/img/thanosql_syntax/query/LIST/img4.png)

!!! note "LIST as a Subquery"
    - "__LIST__" 구문은 "__SELECT__"의 서브쿼리로 사용할 수 있습니다.
    - "__SELECT * FROM (LIST THANOSQL MODEL)__" 같이 "__LIST__" 구문을 서브쿼리로 사용함으로서 "__LIMIT__"과 "__WHERE__" 같은 SQL 구문들과 함께 사용할 수 있습니다. 