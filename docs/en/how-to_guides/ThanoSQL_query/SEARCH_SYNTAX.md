---
title: Searching for unstructured data
---

# **Searching for unstructured data (SEARCH)**

## Preface

- Updated Date : {{ git_revision_date_localized }}

## **1. SEARCH Syntax Overview**

The "**SEARCH**" query syntax searches for content, meaning, or similarity in unstructured data.

## **2. SEARCH Syntax**

```sql
%%thanosql

SEARCH [custom_data_table_name]
USING [AI_model_to_use]
AS [data_set_to_use]
```

## **3. SEARCH Query Syntax example**

The query below uses `Color_Descriptor`, an AI model for quantifying images, to search for similar images.

```sql
%%thanosql
SEARCH IMAGE images='tutorial/image_search/images/20150617_132435.jpg'
USING Color_Descriptor
AS
SELECT *
FROM color_descriptor_table_test
```

[![IMAGE](/img/thanosql_syntax/query/SEARCH/SEARCH_img1.png)](/img/thanosql_syntax/query/SEARCH/SEARCH_img1.png)
