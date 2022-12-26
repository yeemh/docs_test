---
title: Compute
---

# __Compute__

## __1. Compute 문__

사용자는 "__Compute__" 구문을 사용하여 ThanoSQL의 "__Compute__" 함수를 통해 데이터를 처리합니다.

## __2. Compute 함수__

### __2-1. groupby_mean__

"__groupby_mean__"은 데이터 테이블에 임베딩과 라벨 열이 존재하면 라벨별로 그룹화하고 임베딩 값을 평균화하는 기능입니다.

__groupby_mean 구문__

```sql
query_statement:
    query_expr

FUNCTION compute.groupby_mean
OPTIONS (
    expression [ , ...]
    )
{ AS (query_expr) | FROM {file_path_expression} }
```

__OPTIONS 절__

```sql
OPTIONS (
    (label_col=column_name),
    (emb_col=column_name),
    [result_col=expression]
    )
```

"__OPTIONS__" 절은 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.

- "label_col": 데이터 테이블에서 라벨의 정보를 담은 컬럼의 이름입니다. (str)
- "emb_col": 데이터 테이블에서 수치화 정보를 담은 컬럼의 이름입니다. (str)
- "result_col": 데이터 테이블에서 임베딩 값들의 평균을 담을 컬럼 이름을 설정합니다. (str, optional, default: 'mean_emb_result')

__groupby_mean 예시__

```sql
%%thanosql
FUNCTION compute.groupby_mean
OPTIONS (
    label_col='label',
    emb_col='convert_result',
    result_col='result_col'
    )
AS
SELECT * FROM mnist_train_test
```

### __2-2. similarities__

"__similarities__"는 라벨이 지정되지 않은 데이터의 평균 임베딩 값을 사용하여 유사성을 구하고 데이터에 라벨을 지정하는 기능입니다.

__similarities 구문__

```sql
query_statement:
    query_expr

FUNCTION compute.similarities
OPTIONS (
    expression [ , ...]
    )
{ AS (query_expr) | FROM {file_path_expression} }
```

__OPTIONS 절__

```sql
OPTIONS (
    (target_emb_col=column_name),
    (src_table=expression),
    (src_emb_col=column_name),
    (src_label_col=column_name)
    [topk=VALUE]
    )
```

"__OPTIONS__" 절은 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.

- "target_emb_col": 데이터 테이블에서 라벨을 지정할 열의 이름입니다. (str)
- "src_table": 평균 임베딩 값에 대한 정보가 포함된 테이블의 이름입니다. (str)
- "src_emb_col": 데이터 테이블에서 임베딩 값을 담은 컬럼의 이름입니다. (str)
- "src_label_col": 데이터 테이블에서 라벨을 담은 컬럼의 이름입니다. (str)
- "topk": 제공되는 라벨의 수를 지정합니다. (int, optional, default: 1)

__similarities 예시__

```sql
%%thanosql
FUNCTION compute.similarities
OPTIONS (
    target_emb_col='convert_result',
    src_table='mnist_train_test',
    src_emb_col='convert_result',
    src_label_col='label',
    topk=3
    )
AS
SELECT * FROM mnist_train_test
```