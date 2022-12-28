---
title: ViLT
---

# __ViLT__

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

## __PREDICT Syntax__

Use the "__PREDICT__" statement to apply AI models to perform prediction, classification, recommendation, and more. The "__PREDICT__" statement can preprocess the dataset defined by the query_expr that comes after the "__AS__" clause.

```sql
query_statement:
    query_expr

PREDICT USING (model_name_expression)
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```

__OPTIONS Clause__

```sql
OPTIONS (
    (image_col=column_name),
    (question=expression),
    [result_col=column_name],
    [table_name=expression]
    )
```

The "__OPTIONS__" clause allows you to change the value of a parameter. The definition of each parameter is as follows.

- "image_col": the column containing the image path to be used for prediction (str, default: 'image_path')
- "question": the question text to be used for prediction (str)
- "result_col": defines the name of the column to contain the result (str, optional, default: "predict_result")
- "table_name": the table name to be stored in the ThanoSQL workspace database. If a previously used table is specified, the existing table will be replaced by the new table with a 'predict_result' column. If not specified, the result dataframe will not be saved as a table (str, optional)


__PREDICT Example__

An example "__PREDICT__" query can be found in [Use the Visual Question Answering Model](/en/tutorials/thanosql_ml/question_answering/visual_question_answering/).

```sql
%%thanosql
PREDICT USING tutorial_vilt
OPTIONS (
    image_col='image_path',
    question='How many people are there?',
    result_col='predict_result',
    table_name='coco_person_data'
    )
AS
SELECT image_path
FROM coco_person_data
```
