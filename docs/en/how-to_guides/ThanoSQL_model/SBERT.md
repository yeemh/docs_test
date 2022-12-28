---
title: SBERT
---

# __SBERT__

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
USING { SBERTKo | SBERTEn }
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```

__OPTIONS Clause__

```sql
OPTIONS (
    (text_col=column_name),
    [batch_size=VALUE],
    [max_epochs=VALUE],
    [learning_rate=VALUE],
    [overwrite={True|False}]
    )
```

The "__OPTIONS__" clause allows you to change the value of a parameter. The definition of each parameter is as follows.

- "text_col": the name of the column containing the text to be used for the training (str, default: 'text')
- "batch_size": the size of dataset bundle utilized in a single cycle of training (int, optional, default: 16)
- "max_epochs": number of times to train with the training dataset (int, optional, default: 1)
- "learning_rate": the learning rate of the model (float, optional, default: 3e-5) 
- "overwrite": determines whether to overwrite a model if it already exists. If set as True, the old model is replaced with the new model (bool, optional, True|False, default: False) 

__BUILD MODEL Example__

An example "__BUILD MODEL__" query can be found in [Search Text by Text](/en/tutorials/thanosql_search/search_text_by_text/).

```sql
%%thanosql
BUILD MODEL movie_text_search_model
USING SBERTEn
OPTIONS (
    text_col='review',
    overwrite=True
    )
AS
SELECT *
FROM movie_review_train
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
    (text_col=column_name),
    [table_name=expression],
    [batch_size=VALUE],
    [result_col=column_name]
    )
```

The "__OPTIONS__" clause allows you to change the value of a parameter. The definition of each parameter is as follows.

- "text_col": the name of the column containing the text to be used for the vectorization (str, default: 'text')
- "table_name": the table name to be stored in the ThanoSQL workspace database. If a previously used table is specified, the existing table will be replaced by the new table with a 'convert_result' column. If not specified, the result dataframe will not be saved as a table (str, optional)
- "batch_size": the size of dataset bundle utilized in a single cycle of training (int, optional, default: 16)
- "result_col": defines the column name that contains the vectorized results (str, optional, default: 'convert_result')


__CONVERT Example__

An example "__CONVERT__" query can be found in [Search Text by Text](/en/tutorials/thanosql_search/search_text_by_text/).

```sql
%%thanosql
CONVERT USING movie_text_search_model
OPTIONS (
    text_col='review',
    table_name='movie_review_test',
    batch_size=32,
    result_col="convert_result"
    )
AS 
SELECT *
FROM movie_review_test
```

## __SEARCH TEXT Syntax__

Use the "__SEARCH TEXT__" statement to retrieve the desired text data.

```sql
query_statement:
    query_expr

SEARCH TEXT 
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
- "result_col": defines the name of the column that contains the search results (str, optional. default: 'search_result')

__SEARCH TEXT Example__

An example "__SEARCH TEXT__" query can be found in [Search Text by Text](/en/tutorials/thanosql_search/search_text_by_text/).

```sql
SELECT review, sentiment, score
FROM (
    SEARCH TEXT text="This movie was my favorite movie of all time"
    USING movie_text_search_model
    OPTIONS (
        emb_col="convert_result",
        column_name="score"
        )
    AS 
    SELECT * 
    FROM movie_review_test
    )
ORDER BY score DESC 
LIMIT 10
```

## __SEARCH KEYWORD Syntax__

Use the "__SEARCH KEYWORD__" statement to retrieve the desired keyword data.

```sql
query_statement:
    query_expr

SEARCH KEYWORD 
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
    [lang={en|ko}],
    (text_col=column_name),
    [ngram_range=[VALUE,VALUE]],
    [top_n=VALUE],
    [diversity=VALUE],
    [use_stopwords={True|False}],
    [threshold=VALUE]
    )
```

The "__OPTIONS__" clause allows you to change the value of a parameter. The definition of each parameter is as follows.

- "lang": language to use (str, optional, 'ko'|'en' default: 'ko')
- "text_col": the name of the column containing the text to be used for th keyword extraction (str, default: 'text')
- "ngram_range": minimum and maximum number of words for each keyword ex) [1, 3]. In most situations, keywords are extracted according to the maximum number of words (list[int, int], optional, default: [1, 2])
- "top_n": number of keywords to be extracted, in order of highest similarity (int, optional, default: 5)
- "diversity": variety of keywords to be extracted. The higher the value, the more diverse the keywords will be 0 <= diversity <= 1 (float, optional, default: 0.5)
- "use_stopwords": whether to exclude words that do not have a significant meaning (bool, optional, True|False, default: True)
- "threshold": minimum value of similarity value of keywords to be extracted (float, optional, default: 0.0)


__SEARCH KEYWORD Example__

An example "__SEARCH KEYWORD__" query can be found in [Search Text by Text](/en/tutorials/thanosql_search/search_text_by_text/).

```sql
%%thanosql 
SELECT * FROM (
    SELECT review, sentiment, json_array_elements(keyword -> 'keyword') AS keywords, json_array_elements(keyword -> 'score') AS score
        FROM (
            SEARCH KEYWORD 
            USING movie_text_search_model
            OPTIONS (
                text_col='review',
                use_stopwords=True
                )
            AS 
            SELECT * 
            FROM movie_review_test
            LIMIT 10
        )
    ) 
WHERE score > 0.5
```
