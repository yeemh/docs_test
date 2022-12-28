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
    - __literal__: a fixed or unchangeable value, also known as a Constant.
    > Each literal has a special data type such as column, in the table.

## __BUILD MODEL Syntax__

Use the "__BUILD MODEL__" statement to develop an AI model. The "__BUILD MODEL__" statement allows you to train a model using datasets defined with the query_expr that comes after the "__AS__" clause.

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
OPTIONS (
    (image_col=column_name),
    (label_col=column_name),
    [batch_size = VALUE],
    [max_epochs=VALUE],
    [learning_rate=VALUE],
    [input_size=VALUE],
    [color={'RGB'|'GRAY'}],
    [overwrite={True|False}]
    )
```

The "__OPTIONS__" clause allows you to change the value of a parameter. The definition of each parameter is as follows.

- "image_col": name of column containing the image path to be used for the training (str, default: 'image_path')
- "label_col": name of the column containing information about the target value (str, default: 'label')
- "batch_size": the size of dataset bundle utilized in a single cycle of training (int, optional, default: 16)
- "max_epochs": number of times to train with the training dataset (int, optional, default: 3)
- "learning_rate": the learning rate of the model (float, optional default: 1e-3)
- "input_size": size of the image to be used for training (int, optional)
- "color": color of the image to be used for training (str, optional, 'RGB'|'GRAY', default: 'RGB')
- "overwrite": overwrite if a model with the same name exists. If True, the existing model is overwritten with the new model (bool, optional, True|False, default: False)

__BUILD MODEL Example__

An example "__BUILD MODEL__" query can be found in [Create an Image Classification Model](/en/tutorials/thanosql_ml/classification/image_classification/).

```sql
%%thanosql
BUILD MODEL my_product_classifier
USING ConvNeXt_Tiny
OPTIONS (
  image_col='image_path',
  label_col='div_l',
  max_epochs=1,
  overwrite=True
  )
AS
SELECT *
FROM product_image_train
```

## __FIT MODEL Syntax__

Use the "__FIT MODEL__" statement to retrain an AI models. The "__FIT MODEL__" statement allows you to retrain a model using datasets defined with the query_expr that comes after the "__AS__" clause. In this case, the label of the data used for retraining should be the same as the label used for the previous training.

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

```sql
OPTIONS (
    (image_col=column_name),
    (label_col=column_name),
    [batch_size = VALUE],
    [max_epochs=VALUE],
    [learning_rate=VALUE],
    [input_size=VALUE],
    [color={'RGB'|'GRAY'}],
    [overwrite={True|False}]
    )
```

The "__OPTIONS__" clause allows you to change the value of a parameter. The definition of each parameter is as follows.

- "image_col": name of column containing the image path to be used for the training (str, default: 'image_path')
- "label_col": name of the column containing information about the target value (str, default: 'label')
- "batch_size": the size of dataset bundle utilized in a single cycle of training (int, optional, default: 16)
- "max_epochs": number of times to train with the training dataset (int, optional, default: 3)
- "learning_rate": the learning rate of the model (float, optional default: 1e-3)
- "input_size": size of the image to be used for training (int, optional)
- "color": color of the image to be used for training (str, optional, 'RGB'|'GRAY', default: 'RGB')
- "overwrite": overwrite if a model with the same name exists. If True, the existing model is overwritten with the new model (bool, optional, True|False, default: False)

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
    [result_col=column_name],
    [batch_size=VALUE],
    [table_name=expression],
    [input_size=VALUE]
    )
```

The "__OPTIONS__" clause allows you to change the value of a parameter. The definition of each parameter is as follows.

- "image_col": the column containing the image path to be used for prediction (str, default: 'image_path')
- "result_col": the column that contains the predicted results (str, optional, default: 'predict_result')
- "batch_size": the size of the dataset bundle utilized in a single cycle of prediction (int, optional, default: 16)
- "table_name": the table name to be stored in the ThanoSQL workspace database. If a previously used table is specified, the existing table will be replaced by the new table with a 'predict_result' column. If not specified, the result dataframe will not be saved as a table (str, optional)
- "input_size": size of the image to be used for prediction (int, optional)


__PREDICT Example__

An example "__PREDICT__" query can be found in [Create an Image Classification Model](/en/tutorials/thanosql_ml/classification/image_classification/).

```sql
%%thanosql
PREDICT USING my_product_classifier
OPTIONS (
    image_col='image_path',
    result_col='predict_result',
    table_name='product_image_test'
    )
AS
SELECT *
FROM product_image_test
```



## __EVALUATE Syntax__

Use the "__EVALUATE__" statement to evaluate the AI model. The "__EVALUATE__" statement evaluates a model using the dataset defined by the query_expr that comes after the "__AS__" clause.

```sql
query_statement:
    query_expr

EVALUATE USING (model_name_expression)
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
    (label_col=column_name),
    [batch_size=VALUE],
    [input_size=VALUE]
    )
```

The "__OPTIONS__" clause allows you to change the value of a parameter. The definition of each parameter is as follows.

- "image_col": the column containing the image path to be used for evaluation (str, default: 'image_path')
- "label_col": the name of the column containing information about the target (str, default: 'label')
- "batch_size": the size of dataset bundle utilized in a single cycle of evaluation (int, optional, default: 16)
- "input_size": size of the image to be used for evaluation (int, optional)