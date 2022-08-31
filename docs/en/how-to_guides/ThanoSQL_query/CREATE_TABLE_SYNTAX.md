---
title: Transform Unstructured Data
---

# **Transform Unstructured Data (CREATE TABLE)**

## Preface

- Updated Date : {{ git_revision_date_localized }}

## **1. CREATE TABLE Query Syntax overview**

Using the syntax "**CREATE TABLE**", users can create a data table that converts unstructured data (image, audio, video, etc.) into vector formats using a quantization algorithm.

## **2. CREATE TABLE Syntax**

```sql
CREATE TABLE [Custom_data_table_name]
USING [AI_model_to_use]
OPTIONS (overwrite=True) -- default:False
FROM [data_set_to_use]
```

!!! note "**Query Details**"
    - Specify the options to use for **CREATE TABLE** with the query syntax "**OPTIONS**".
        - "overwrite" : Set whether or not a data set with the same name can be overwritten when it exists on the DB. If True, the existing dataset is changed to the new dataset (True|False, DEFAULT: False)

## **3. CREATE TABLE Syntax example**

The example below uses an attribute extraction AI model called 'data/thanosAlgo/image_search/junyoung_test/' `Color_descriptor` to create image files in the path in a data table named `color_descriptor_table_test` in the ThanoSQL DB.

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
