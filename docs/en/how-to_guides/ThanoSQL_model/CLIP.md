---
title: CLIP
---

# __CLIP__

__Notation Conventions__

- Parentheses `()` indicate ^^literal^^ parentheses.
- Braces `{}` are used to bind combinations of options.
- The bracket `[]` indicates an optional clause.
- An ellipsis following a comma in brackets [,...] means that the preceding item can be repeated as a comma-separated list
- The vertical bar `|` represents the logic `OR`.
- VALUE represents a regular value.

!!! note ""
    - __literal__: a fixed or unchangeable value, also known as a Constant.
    > Each literal has a special data type such as column, in the table.

## __CONVERT Syntax__

Use the "__CONVERT__" statement to convert data into the vectors and add it to the table.

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

__OPTIONS Clause__

```sql
OPTIONS (
    [table_name=expression],
    (image_col=column_name),
    (text_col=column_name),
    (convert_type={'image'|'text'}),
    [batch_size=VALUE],
    [result_col=column_name]
    )
```

The "__OPTIONS__" clause allows you to change the value of a parameter. The definition of each parameter is as follows.

- "table_name": the table name to be stored in the ThanoSQL workspace database. If a previously used table is specified, the existing table will be replaced by the new table with a 'convert_result' column. If not specified, the result dataframe will not be saved as a table (str, optional)
- "image_col": the name of the column containing the image path (str, default: 'image_path')
- "text_col": the name of the column containing the text (str, default: 'text')
- "convert_type": file type for vectorization (str, 'image'|'text', default: 'image')
- "batch_size": the size of dataset bundle utilized in a single cycle of training (int, optional, default: 16) 
- "result_col": defines the column name that contains the vectorized results (str, optional, default: 'convert_result')


__CONVERT Example__

An example "__CONVERT__" query can be found in [Search Image by Text](/en/tutorials/thanosql_search/search_image_by_text/).

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

## __SEARCH IMAGE Syntax__

Use the "__SEARCH IMAGE__" statement to retrieve the desired image data.

```sql
query_statement:
    query_expr
    
SEARCH IMAGE 
USING (model_name_expression)
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```

__OPTIONS Clause__

```sql
OPTIONS (
    (search_by={image|text|audio|video}),
    (search_input=expression),
    (emb_col=column_name),
    [result_col=expression]
    )
```

The "__OPTIONS__" clause allows you to change the value of a parameter. The definition of each parameter is as follows.

- "search_by": defines the image|text|audio|video type to be used for the search (str)
- "search_input": defines the input to be used for the search (str)
- "emb_col": the column that contains the vectorized results (str)
- "result_col": defines the name of the column that contains the search results (str, optional, default: 'search_result')


__SEARCH IMAGE Example__

An example "__SEARCH IMAGE__" query can be found in [Search Image by Text](/en/tutorials/thanosql_search/search_image_by_text/).

```sql
%%thanosql
SEARCH IMAGE 
USING tutorial_search_clip
OPTIONS (
    search_by='text',
    search_input='a black cat',
    emb_col='convert_result',
    result_col='search_result'
    )
AS 
SELECT * 
FROM unsplash_data
```
