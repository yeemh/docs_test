---
title: SEARCH
---

# __SEARCH__

## __1. SEARCH Statement__

The "__SEARCH__" statement allows users to search for content, meaning, or similarity from the unstructured data table.

## __2. SEARCH Syntax__

```sql
%%thanosql
SEARCH [custom_data_table_name]
USING [AI_model_to_use]
AS [dataset_to_use]
```

## __3. SEARCH Example__
The example below uses an attribute extraction model called `Color_descriptor` to search for similar images.

```sql
%%thanosql
SEARCH IMAGE images='tutorial/image_search/images/20150617_132435.jpg'
USING Color_Descriptor
AS
SELECT *
FROM color_descriptor_table_test
```

[![IMAGE](/img/thanosql_syntax/query/SEARCH/SEARCH_img1.png)](/img/thanosql_syntax/query/SEARCH/SEARCH_img1.png)
