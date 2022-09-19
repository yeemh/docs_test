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
    __literal__ : A fixed or unchangeable value, also known as a Constant.
    > Each literal has a special data type such as column, in the table.

## __CREATE TABLE Syntax__

You can use the "__CREATE TABLE__" statement to generate a table containing the vectors of the image data.

```sql
CREATE TABLE (table_name_expression)
USING clip_en
OPTIONS (
    expression [ , ...]
    )
FROM
(query_expresison)
```

__OPTIONS Clause__

```sql
OPTIONS(
    (path_type = column_name),
    (data_type = column_name),
    [file_type = VALUE],
    [batch_size = VALUE],
    [overwrite = {True | False}]
    )
```

The "__OPTIONS__" clause allows you to change the value of a parameter in the model. The definition of each parameter is as follows.

- "path_type" : Sets the name of the column containing the audio files path (DEFAULT: "audio_path")
- "data_type" : Type of data.
- "file_type" : The extension type of the image.
- "batch_size" : The size of the dataset bundle read during a single train. (DEFAULT : 16)
- "overwrite" : Overwrite if a model with the same name exists. If True, the existing model is overwritten with the new model. (DEFAULT: False)

## __CONVERT Syntax__

The "__CONVERT__" statement converts image data from an existing table into a vector and adds it to the table.

```sql
CONVERT USING clip_en
OPTIONS(
    (table_name = expression),
    (image_col = column_name),
    [batch_size = VALUE]
    )
AS
(query_expr)
```

__OPTIONS Clause__

```sql
OPTIONS(
    (table_name=expression),
    (image_col=column_name),
    [batch_size=VALUE]
)
```

The "__OPTIONS__" clause allows you to change the value of a parameter in the model. The definition of each parameter is as follows.

- "table_name" : The name of the new table to be created.
- "image_col" : The name of the column containing the image path. (DEFAULT : 'image_path')
- "batch_size" : The size of the dataset bundle read during a single train. (DEFAULT : 16)

__CONVERT Example__

Examples of CONVERT queries can be found in [Image search with text](/en/tutorials/thanosql_search/search_image_by_text/).

```sql
%%thanosql
CONVERT USING tutorial_search_clip
OPTIONS (
    image_col="image_path",
    table_name="unsplash_data",
    batch_size=128
    )
AS
SELECT *
FROM unsplash_data
```

## __SEARCH Syntax__

You can use the "__SEARCH__" statement to retrieve the desired image from the table that generated the vectors.

```sql
SEARCH IMAGE ({text|texts|image|images} = expression)
USING clip_en
AS
(query_expr)
```

!!! note ""
    You must choose one of (text | texts) or (image | images) as an input. The input must be a string (e.g., 'a black cat', 'data/image/image01.jpg') or a list of strings (e.g., ['a black cat', 'a orange cat'], ['data/image/image01.jpg', 'data/image/image02.jpg']).

__SEARCH Example__

Examples of SEARCH queries can be found in [Image search with text](/en/tutorials/thanosql_search/search_image_by_text/).

```sql
%%thanosql
SEARCH IMAGE text="a black cat"
USING tutorial_search_clip
AS
SELECT *
FROM unsplash_data
```
