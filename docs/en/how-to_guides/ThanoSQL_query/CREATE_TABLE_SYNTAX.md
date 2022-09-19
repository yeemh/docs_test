---
title: CREATE TABLE
---

# __CREATE TABLE__

## __1. CREATE TABLE Statement__
The "__CREATE TABLE__" statement allows users to create a data table with unstructured data such as images, audio, video, and more by converting them into vector formats with a vectorization algorithm.

## __2. CREATE TABLE Syntax__

```sql
CREATE TABLE [custom_data_table_name]
USING [AI_model_to_use]
OPTIONS (
    overwrite=True -- default:False
) 
FROM [dataset_path_to_use]
```

!!! note "__Query Details__"
    - Specify the options to use for the __CREATE TABLE__ statement with an "__OPTIONS__" clause.
        - "overwrite" : Overwrite if a dataset with the same name exists. If True, the existing dataset is overwritten with the new dataset. (True|False, DEFAULT: False)

## __3. CREATE TABLE Example__
The example below uses an attribute extraction model called `Color_descriptor` to create a user-defined table named `color_descriptor_table_test` and store vectorized image files from the specified path.

```sql
%%thanosql
CREATE TABLE color_descriptor_table_test
USING Color_Descriptor
OPTIONS (
    data_type='image',
    file_type=['.jpg'],
    overwrite = True
    )
FROM 'data/thanosAlgo/image_search/junyoung_test/'
```
