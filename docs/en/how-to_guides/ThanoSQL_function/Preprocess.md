---
title: Preprocess
---

# __Preprocess__

## __1. Preprocess Statement__

The "__Preprocess__" statement allows users to preprocess data using ThanoSQL's "__Preprocess__" functions.

## __2. Preprocess Functions__

### __2-1. video_to_df__

The "__video_to_df__" is a function that divide video into fixed intervals to save as files and create a data table.

__video_to_df Syntax__


```sql
query_statement:
    query_expr

FUNCTION preprocess.video_to_df
OPTIONS (
    expression [ , ...]
    )
{ AS (query_expr) | FROM {file_path_expression} } 
```


__OPTIONS Clause__

```sql
OPTIONS (
    [method_name={'split'}],
    [interval=VALUE],
    (table_name=expression),
    (folder_name=expression)
    )
```

The "__OPTIONS__" clause allows you to change the value of a parameter. The definition of each parameter is as follows.

- "method_name": video preprocessing method, currently only supports 'split' method (str)
- "interval": setting of the interval in seconds (int, optional, default: 1)
- "table_name": the name of the table to be created after preprocessing (str)
- "folder_name": the name of the folder where the split video will be stored after preprocessing (str)


__video_to_df Example__

```sql
%%thanosql
FUNCTION preprocess.video_to_df 
OPTIONS (
   method_name='split', 
   interval=1, 
   table_name='video_split_table', 
   folder_name='video_split_folder'
    )
FROM 'thanosql-dataset/kinetics700_data/video/1ejgHKw8E3Y.mp4'
```