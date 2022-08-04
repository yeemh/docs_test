---
title: 저장한 모델 확인하기
---

# __저장한 모델 확인하기 (LIST)__

## 시작 전 사전 정보

- 마지막 수정날짜 : {{ git_revision_date_localized }}

## __1. LIST 구문 개요__

사용자는 "__LIST__" 구문을 사용하여 현재 ThanoSQL의 Pre-built 모델들("THANOSQL MODEL")과 사용자가 만든 모델들("MODEL")을 확인할 수 있습니다. 

## __2. LIST 구문__

"__LIST MODEL__" 구문은 사용자가 만든 모델들의 리스트를 확인합니다.

!!! Failure "Caution"
    단, 저장한 모델이 없는 경우에는 오류가 발생합니다.

```sql
%%thanosql
LIST MODEL
```
<a href = "/img/thanosql_syntax/query/LIST/img1.png">
    <img src = "/img/thanosql_syntax/query/LIST/img1.png"> </img>
</a>

"__LIST THANOSQL MODEL__" 구문은 ThanoSQL의 Pre-built 모델들의 리스트를 확인합니다.

```sql
%%thanosql
LIST THANOSQL MODEL
```
<a href = "/img/thanosql_syntax/query/LIST/img2.png">
    <img src = "/img/thanosql_syntax/query/LIST/img2.png"> </img>
</a>

"__LIST THANOSQL TUTORIAL__" 구문은 ThanoSQL에 저장되어 있는 Tutorial들의 리스트를 확인합니다.

```sql
%%thanosql
LIST THANOSQL TUTORIAL
```
<a href = "/img/thanosql_syntax/query/LIST/img3.png">
    <img src = "/img/thanosql_syntax/query/LIST/img3.png"> </img>
</a>

"__LIST THANOSQL DATASET__" 구문은 ThanoSQL의 Dataset들의 리스트를 확인합니다.

```sql
%%thanosql
LIST THANOSQL DATASET
```
<a href = "/img/thanosql_syntax/query/LIST/img4.png">
    <img src = "/img/thanosql_syntax/query/LIST/img4.png"> </img>
</a>

"__LIST TABLE__" 구문은 사용자가 만든 테이블들의 리스트를 확인합니다.

!!! Failure "Caution"
    단, 생성된 테이블이 없는 경우에는 오류가 발생합니다.

```sql
%%thanosql
LIST TABLE
```
<a href = "/img/thanosql_syntax/query/LIST/img5.png">
    <img src = "/img/thanosql_syntax/query/LIST/img5.png"> </img>
</a>