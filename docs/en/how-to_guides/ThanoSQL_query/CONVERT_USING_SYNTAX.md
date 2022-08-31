---
title: Add unstructured properties
---

# **Add unstructured properties (CONVERT USING)**

## Preface

- Updated Date : {{ git_revision_date_localized }}

## **1. CONVERT USING Query Syntax Overview**

The "**CONVERT USING**" syntax uses information from unstructured data, such as images, videos, and voice, to convert to vector format using a quantization algorithm and add this value to the dataset you want to use.

## **2. CONVERT USING Query Syntax**

```sql
CONVERT USING [AI_model_to_use]
OPTIONS(
    table_name=[Table_name_to_be_saved]
    )
AS
[data_set_to_use]
```

## **3. CONVERT USING Query Syntax example**

### **3.1 `Color_descriptor` Image quantification using algorithms**

The example below uses the color feature extraction model created in [Transform Unstructured Data(CREATE TABLE)](/en/how-to_guides/ThanoSQL_query/CREATE_TABLE_SYNTAX/) to store the quantized results by adding a new column to the "color_descriptor_table_test" table stored in the ThanoSQL DB.

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

### **3.2 `clip_en` Image digitization using algorithms**

The example below stores the quantized results using the `clip_en` algorithm by adding a new column to the "mnist_dataset" table stored on the ThanoSQL DB.

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
