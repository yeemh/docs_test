---
title: SEARCH
---

# __SEARCH__

## __1. SEARCH Statement__

The "__SEARCH__" statement allows users to search for content, meaning, or similarity from the unstructured data table.

## __2. SEARCH Syntax__

```sql
query_statement:
    query_expr

SEARCH { IMAGE | AUDIO | VIDEO | TEXT | KEYWORD }
USING (model_name_expression)
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```

## __3. SEARCH Example__

!!! note
    - Examples are specific to one model, and the required option values ​​or the dataset used may differ from model to model. For a detailed description of each model, refer to the [ThanoSQL Model Statement Reference](/en/how-to_guides/reference/#thanosql-model-statement-reference)
    - Because the example only works when a specific model and dataset are present, it may not run normally if copied and used as is.

```sql
%%thanosql
SEARCH IMAGE
USING my_image_search_model
OPTIONS (
    search_by='image',
    search_input='thanosql-dataset/mnist_data/test/923.jpg',
    emb_col='convert_result',
    result_col='search_result'
    )
AS
SELECT * 
FROM mnist_test
```

!!! note "AI models that can be used with '__SEARCH__ statement'"
    - SimCLR Model - SimCLR
    - CLIP Model - CLIP
    - XCLIP Model - XCLIP
    - SBERT Model - SBERTKo, SBERTEn

