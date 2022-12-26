---
title: CONVERT
---

# __CONVERT__

## __1. CONVERT Statement__

The "__CONVERT__" statement allows users to convert unstructured data (image|audio|video|text) to vector format using a vectorization algorithm.

## __2. CONVERT Syntax__

```sql
query_statement:
    query_expr

CONVERT USING (model_name_expression)
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```

!!! note "__Query Details__"
    - The "__OPTIONS__" clause allows you to change the value of a parameter. The definition of each parameter is as follows.
        - "table_name": the table name to be stored in the ThanoSQL workspace database. If a previously used table is specified, the existing table will be replaced by the new table with a 'convert_result' column. If not specified, result dataframe will not be saved as a data table (str, optional)

## __3. CONVERT Example__

!!! note
    - Examples are specific to one model, and the required option values ​​or the dataset used may differ from model to model. For a detailed description of each model, refer to the [ThanoSQL Model Statement Reference](/en/how-to_guides/reference/#thanosql-model-statement-reference)
    - Because the example only works when a specific model and dataset are present, it may not run normally if copied and used as is.


```sql
%%thanosql
CONVERT USING tutorial_search_clip
OPTIONS (
    table_name='unsplash_data',
    image_col='image_path', 
    convert_type='image',
    batch_size=128,
    result_col='convert_result'
    )
AS 
SELECT *
FROM unsplash_data
```

!!! note "AI models that can be used with '__CONVERT__ statement'"
    - SimCLR Model - SimCLR
    - CLIP Model - CLIP
    - XCLIP Model - XCLIP
    - SBERT Model - SBERTKo, SBERTEn

