---
title: FUNCTION
---

# __FUNCTION__

## __1. FUNCTION Statement__

The "__FUNCTION__" statement allows users to process data using ThanoSQL's functoins from the "__FUNCTION__" modules.

## __2. FUNCTION Syntax__

The "__FUNCTION__" syntax with an "__AS__" clause. 

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

The "__FUNCTION__" syntax with an "__FROM__" clause. 

```sql
FUNCTION {function_module_expression}.{function_name_expression}
OPTIONS (
    expression [ , ...]
    )
FROM {file_path_expression}
```


!!! note "__Query Details__"
    - The "__OPTIONS__" clause allows you to change the value of a parameter. The definition of each parameter is as follows:
        - "table_name": the table name to be stored in the ThanoSQL workspace database. If a previously used table is specified, the existing table will be replaced by the new table with a column containing the results. If not specified, the result dataframe will not be saved as a table (str, optional)


!!! note "ThanoSQL's modules and functions that can be used with '__FUNCTION__ statement'"
    - compute
        - groupby_emb_mean
        - similarity
    - preprocess
        - video_to_df     