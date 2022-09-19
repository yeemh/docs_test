---
title: ConvNeXt & EfficientNet
---

# __ConvNeXt & EfficientNet__

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

## __BUILD MODEL Syntax__

Use the "__BUILD MODEL__" query to develop an AI model.
The "__BUILD MODEL__" statement allows you to train datasets defined with the query_expr that comes after the "__AS__" clause.

```sql
query_statement:
    query_expr

BUILD MODEL (model_name_expression)
USING { ConvNeXt_Tiny | ConvNeXt_Base | EfficientNetV2S | EfficientNetV2M }
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```

__OPTIONS Clause__

```sql
OPTIONS(
    (image_col = column_name),
    (label_col = column_name),
    [batch_size = VALUE],
    [epochs = VALUE],
    [learning_rate = VALUE],
    [overwrite = {True | False}]
    )
```

The "__OPTIONS__" clause allows you to change the value of a parameter in the model. The definition of each parameter is as follows.

- "image_col" : Sets the name of the column containing the image path. (DEFAULT: "image")
- "label_col" : Sets the name of the column containing the path of the label. (DEFAULT : "label")
- "batch_size" : The size of the dataset bundle read during a single train. (DEFAULT : 16)
- "epochs" : Sets how many times the dataset is trained in total. (DEFAULT : 3)
- "learning_rate" : The learning rate of the model. (DEFAULT : 0.0001)
- "overwrite" : Overwrite if a model with the same name exists. If True, the existing model is overwritten with the new model (DEFAULT: False)

__BUILD MODEL Example__

A sample BUILD MODEL query can be found in [Create an image classification model](/en/tutorials/thanosql_ml/classification/image_classification/).

```sql
%%thanosql
BUILD MODEL my_product_classifier
USING ConvNeXt_Tiny
OPTIONS (
  image_col='image_path',
  label_col='div_l',
  epochs=1,
  overwrite=True
  )
AS
SELECT *
FROM product_image_train
```

## __FIT MODEL Syntax__

The "**FIT MODEL**" statement lets you retrain artificial intelligence models. The "**FIT MODEL**" expression allows you to retrain datasets defined with the query_expr that comes after the "__AS__" clause. In this case, the label of the data used for retraining should be the same as the label used for the previous train.

```sql
query_statement:
    query_expr

FIT MODEL (model_name_expression)
USING (model_name_expression)
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```

__OPTIONS Clause__

```sql
OPTIONS(
    (image_col = column_name),
    (label_col = column_name),
    [batch_size = VALUE],
    [epochs = VALUE],
    [learning_rate = VALUE],
    [overwrite = {True | False}]
    )
```

The "__OPTIONS__" clause allows you to change the value of a parameter in the model. The definition of each parameter is as follows.

- "image_col" : Sets the name of the column containing the image path. (DEFAULT: "image")
- "label_col" : Sets the name of the column containing the path of the label (DEFAULT : "label")
- "batch_size" : The size of the dataset bundle read during a single train. (DEFAULT : 16)
- "epochs" : Sets how many times the dataset is trained in total. (DEFAULT : 3)
- "learning_rate" : The learning rate of the model. (DEFAULT : 0.0001)
- "overwrite" : Overwrite if a model with the same name exists. If True, the existing model is overwritten with the new model (DEFAULT: False)

## __PREDICT Syntax__

Use the "__PREDICT__" statement to apply artificial intelligence models to test datasets to perform prediction, classification, recommendation, and more. The "__PREDICT__" expression can preprocess the dataset defined by the query_expr that comes after the "__AS__" clause.

```sql
query_statement:
    query_expr

PREDICT USING (model_name_expression)
OPTIONS (
    expression
    )
AS
(query_expr)
```

__OPTIONS Clause__

```sql
OPTIONS(
    (image_col = column_name),
    [batch_size = VALUE]
    )
```

The "__OPTIONS__" clause allows you to change the value of a parameter in the model. The definition of each parameter is as follows.

- "image_col" : Sets the name of the column containing the image path. (DEFAULT: "image")
- "batch_size" : The size of the dataset bundle read during a single train. (DEFAULT : 16)

__PREDICT Example__

A sample PREDICT query can be found in [Create an image classification model](/en/tutorials/thanosql_ml/classification/image_classification/).

```sql
%%thanosql
PREDICT USING my_product_classifier
OPTIONS (
    image_col='image_path'
    )
AS
SELECT *
FROM product_image_test
```

## __EVALUATE Syntax__

You can use the "__EVALUATE__" statement to evaluate the AI model. The "__EVALUATE__" expression evaluates the dataset defined by the query_expr that comes after the "__AS__" clause.

```sql
query_statement:
    query_expr

EVALUATE USING (model_name_expression)
OPTIONS (
    expression
    )
AS
(query_expr)
```

__OPTIONS Clause__

```sql
OPTIONS(
    (image_col = column_name),
    [batch_size = VALUE]
    )
```

The "__OPTIONS__" clause allows you to change the value of a parameter in the model. The definition of each parameter is as follows.

- "image_col" : Sets the name of the column containing the image path. (DEFAULT: "image")
- "batch_size" : The size of the dataset bundle read during a single train. (DEFAULT : 16)