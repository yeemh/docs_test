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
    [method={'split'}],
    [interval=VALUE],
    (result_col=expression),
    (result_dir=expression)
    )
```

The "__OPTIONS__" clause allows you to change the value of a parameter. The definition of each parameter is as follows.

- "method": video preprocessing method, currently only supports 'split' method (str)
- "interval": setting of the interval in seconds (int, optional, default: 10)
- "result_col": the name of the column containing paths of the preprocessed video (str)
- "result_dir": the name of the folder where the split video will be stored after preprocessing (str)


__video_to_df Example__

```sql
%%thanosql
FUNCTION preprocess.video_to_df 
OPTIONS (
   method='split', 
   interval=1, 
   result_col='video_split_path', 
   result_dir='video_split_folder'
    )
FROM 'thanosql-dataset/kinetics700_data/video/1ejgHKw8E3Y.mp4'
```