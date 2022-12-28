---
title: DELETE MODEL
---

# __DELETE MODEL__

## __1. DELETE MODEL 문__

사용자는 "__DELETE MODEL__" 구문을 사용하여 ThanoSQL 워크스페이스 내에서 생성한 모델을 삭제할 수 있습니다.

## __2. DELETE MODEL 구문__

```sql
DELETE MODEL (model_name_expression)
```

## __3. DELETE MODEL 예시__

```sql
%%thanosql
DELETE MODEL mymodel
```

!!! note "'__DELETE MODEL__ 문'을 사용할 수 있는 인공지능 모델"
    - ThanoSQL 워크스페이스 내에서 생성, 업로드 또는 불러온 모든 모델