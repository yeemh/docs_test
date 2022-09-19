---
title: CONVERT
---

# __CONVERT__

## __1. CONVERT Statement__

The "__CONVERT__" statement allows users to convert unstructured data such as images, videos, and audio to vector format using a vectorization algorithm and append it to the given data table. 
## __2. CONVERT Syntax__

```sql
CONVERT USING [AI_model_to_use]
OPTIONS(
    table_name=[table_name_to_be_saved]
    )
AS
[dataset_to_use]
```

## __3. CONVERT Examples__

### __3.1 Image vectorization using `Color_descriptor` algorithm__
The example below uses the color feature extraction model created in [CREATE TABLE](/en/how-to_guides/ThanoSQL_query/CREATE_TABLE_SYNTAX/) to store the vectorized results as a new column to the "color_descriptor_table_test" table.

```sql
%%thanosql
CONVERT USING Color_Descriptor
OPTIONS(
    table_name= "color_descriptor_table_test"
    )
AS
SELECT *
FROM color_descriptor_table_test
```

[![IMAGE](/img/thanosql_syntax/query/CONVERT/img1.png)](/img/thanosql_syntax/query/CONVERT/img1.png)

### __3.2 Image vectorization using `clip_en` algorithm__

The example below stores the vectorized results of the images using the `clip_en` algorithm as a new column to the "mnist_dataset" table.

```sql
%%thanosql
CONVERT USING clip_en
OPTIONS(
    image_col='image_path',
    table_name='mnist_dataset',
    batch_size=128
    )
AS
SELECT *
FROM mnist_dataset
```
