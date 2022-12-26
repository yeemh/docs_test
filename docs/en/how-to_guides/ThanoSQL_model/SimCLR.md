---
title: SimCLR
---

# __SimCLR__

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


## __BUILD MODEL Syntax__

Use the "__BUILD MODEL__" statement to develop an AI model. The "__BUILD MODEL__" statement allows you to train a model using datasets defined with the query_expr that comes after the "__AS__" clause.

```sql
query_statement:
    query_expr

BUILD MODEL (model_name_expression)
USING SimCLR
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```

__OPTIONS Clause__
​
```sql
OPTIONS (
    (image_col=VALUE),
    [batch_size=VALUE],
    [max_epochs=VALUE],
    [pretrained={True|False}],
    [overwrite={True|False}]
)
```

The "__OPTIONS__" clause allows you to change the value of a parameter. The definition of each parameter is as follows.

- "image_col": the name of the column containing the image path to be used for the training (str, default: 'image_path')
- "batch_size": the size of dataset bundle utilized in a single cycle of training (int, optional, default: 256)
- "max_epochs": number of times to train with the training dataset (int, optional, default: 5)
- "pretrained": Sets whether or not to use pre-trained ImageNet weights (bool, optional, True|False, default: False)
- "overwrite": determines whether to overwrite a model if it already exists. If set as True, the old model is replaced with the new model (bool, optional, True|False, default: False)

__BUILD MODEL Example__

An example "__BUILD MODEL__" query can be found in [Search Image by Image](/en/tutorials/thanosql_search/search_image_by_image/).
​

```sql
%%thanosql
BUILD MODEL my_image_search_model
USING SimCLR
OPTIONS (
    image_col='image_path',
    max_epochs=1,
    overwrite=True
    )
AS
SELECT *
FROM mnist_train
```

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
    [result_col=column_name],
    [batch_size=VALUE]
    )
```

The "__OPTIONS__" clause allows you to change the value of a parameter. The definition of each parameter is as follows.
​
- "table_name": the table name to be stored in the ThanoSQL workspace database. If a previously used table is specified, the existing table will be replaced by the new table with a 'convert_result' column. If not specified, the result dataframe will not be saved as a data table (str, optional)
- "image_col": the name of the column containing the image path (str, default: 'image_path')
- "result_col": defines the column name that contains the vectorized results (str, optional, default: 'convert_result')
- "batch_size": the size of dataset bundle utilized in a single cycle of training (int, optional, default: 256)


__CONVERT Example__

An example "__CONVERT__" query can be found in [Search Image by Image](/en/tutorials/thanosql_search/search_image_by_image/).
​

```sql
%%thanosql
CONVERT USING my_image_search_model
OPTIONS (
    table_name='mnist_test',
    image_col='image_path',
    result_col='convert_result'
    )
AS
SELECT *
FROM mnist_test
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
    [result_col=column_name]
    )
```

The "__OPTIONS__" clause allows you to change the value of a parameter. The definition of each parameter is as follows.

- "search_by": defines the image|text|audio|video type to be used for the search (str)
- "search_input": defines the input to be used for the search (str)
- "emb_col": the column that contains the vectorized results (str)
- "result_col": defines the name of the column that contains the search results (str, optional, default: 'search_result')


__SEARCH IMAGE Example__

An example "__SEARCH IMAGE__" query can be found in [Search Image by Image](/en/tutorials/thanosql_search/search_image_by_image/).

```sql
%%thanosql
SEARCH IMAGE 
USING my_image_search_model
OPTIONS (
    search_by='image',
    search_input='tutorial_data/mnist_data/test/923.jpg',
    emb_col='convert_result',
    result_col='search_result'
)
AS
SELECT *
FROM mnist_test
```
