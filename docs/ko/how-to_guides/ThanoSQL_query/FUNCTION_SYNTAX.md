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

!!! note "쿼리 세부 정보"
    - "__OPTIONS__" 절에서 매개변수의 값을 기본값으로부터 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.
        - "table_name": ThanoSQL 워크스페이스 데이터베이스 내에 저장될 테이블 이름입니다. 기존에 사용한 테이블 이름으로 지정할 경우, 기존 테이블은 결과값을 포함한 컬럼을 추가한 테이블로 대체됩니다. 지정하지 않을 시 테이블을 저장하지 않습니다. (str, optional)


!!! note "'__FUNCTION__ 문'을 사용할 수 있는 ThanoSQL의 모듈과 함수"
    - compute
        - groupby_emb_mean
        - similarity
    - preprocess
        - video_to_df