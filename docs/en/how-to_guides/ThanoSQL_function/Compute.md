---
title: Compute
---

# __Compute__

## __1. Compute Statement__

The "__Compute__" statement allows users to process data using ThanoSQL's "__Compute__" functions.

## __2. Compute Functions__

### __2-1. groupby_mean__

The "__groupby_mean__" is a function that groups by labels if embeddings and a label column exist in the data table and averages the embedding values.

__groupby_mean Syntax__

```sql
query_statement:
    query_expr

FUNCTION compute.groupby_mean
OPTIONS (
    expression [ , ...]
    )
{ AS (query_expr) | FROM {file_path_expression} } 
```

__OPTIONS Clause__

```sql
OPTIONS (
    (label_col=column_name),
    (emb_col=column_name),
    [result_col=expression]
    )
```

The "__OPTIONS__" clause allows you to change the value of a parameter. The definition of each parameter is as follows.

- "label_col": the name of the column containing information about the label (str)
- "emb_col": the name of the column containing information about the embedding (str)
- "result_col": the name of the column containing the embeddings' mean values (str, optional, default: 'mean_emb_result')


__groupby_mean Example__

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

The "__similarities__" is a function that uses the average embedding value of the unlabeled data to obtain similarity and label the data. 

__similarities Syntax__

```sql
query_statement:
    query_expr

FUNCTION compute.similarities
OPTIONS (
    expression [ , ...]
    )
{ AS (query_expr) | FROM {file_path_expression} } 
```

__OPTIONS Clause__

```sql
OPTIONS (
    (target_emb_col=column_name),
    (src_table=expression),
    (src_emb_col=column_name),
    (src_label_col=column_name)
    [topk=VALUE]
    )
```

The "__OPTIONS__" clause allows you to change the value of a parameter. The definition of each parameter is as follows.

- "target_emb_col": the name of the column to be labeled (str)
- "src_table": the name of the table containing information about the average embedding value (str)
- "src_emb_col": the name of the column containing the embedding value (str)
- "src_label_col": the name of the column containing the label (str)
- "topk": the number of label to be provided (int, optional, default: 1)

__similarities Example__

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

