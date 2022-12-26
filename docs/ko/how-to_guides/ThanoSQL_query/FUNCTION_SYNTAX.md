---
title: FUNCTION
---

# __FUNCTION__

## __1. FUNCTION 문__

사용자는 "__FUNCTION__" 구문을 사용하여 "__FUNCTION__" 모듈에서 ThnoSQL의 함수를 사용하여 데이터를 처리 할 수 있습니다.

## __2. FUNCTION 구문__

"__AS__" 절을 사용하는 "__FUNCTION__" 구문

```sql
query_statement:
    query_expr

FUNCTION {function_module_expression}.{function_name_expression}
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```

"__FROM__" 절을 사용하는 "__FUNCTION__" 구문

```sql
FUNCTION {function_module_expression}.{function_name_expression}
OPTIONS (
    expression [ , ...]
    )
FROM {file_path_expression}
```

!!! note "'__FUNCTION__ 문'을 사용할 수 있는 ThanoSQL의 모듈과 함수"
    - compute
        - groupby_mean
        - similiarities
    - preprocess
        - video_to_df