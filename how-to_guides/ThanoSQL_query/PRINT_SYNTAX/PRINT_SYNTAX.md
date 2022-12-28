---
title: PRINT
---

# __PRINT__

## __1. PRINT Statement__
The "__PRINT__" statement allows users to to output images, audio, and video files.

## __2. PRINT Syntax__

The "__PRINT__" syntax.
```sql
query_statement:
    query_expr

PRINT { IMAGE | AUDIO | VIDEO }
AS
(query_expr)
```

The "__PRINT__" syntax with an "__OPTIONS__" clause.

```sql
query_statement:
    query_expr

PRINT IMAGE | AUDIO | VIDEO
OPTIONS (
    image_col | audio_col | video_col = (column_name)
    )
AS
(query_expr)
```

!!! note "__Query Details__"
    - The "__OPTIONS__" clause allows you to change the value of a parameter. The definition of each parameter is as follows:
        - "image_col | audio_col | video_col": the name of a column to be printed (str, default: 'image_path'|'audio_path'|'video_path')

## __3. PRINT Example__

### __3.1 Image Print__

Outputs image files from the table.

```sql
%%thanosql
PRINT IMAGE
OPTIONS (
    image_col='image'
    )
AS
SELECT *
FROM image_table
```

!!! note ""
    - "image_table": table containing paths of the image files

### __3.2 Audio Print__

Outputs audio files from the table.

```sql
%%thanosql
PRINT AUDIO
OPTIONS (
    audio_col='audio'
    )
AS
SELECT *
FROM audio_table
```

[![IMAGE](/img/thanosql_syntax/query/PRINT/PRINT_img1.png)](/img/thanosql_syntax/query/PRINT/PRINT_img1.png)

!!! note ""
    - "audio_table": table containing paths of the audio files

### __3.3 Video Print__

Outputs video files from the table.

```sql
%%thanosql
PRINT VIDEO
OPTIONS (
    video_col='video'
    )
AS
SELECT *
FROM video_table
```

!!! note ""
    - "video_table": table containing paths of the video files

### __3.4 Print with a subquery__

The following statement outputs the results of "__SEARCH__" statement created in the nested [SEARCH](/en/how-to_guides/ThanoSQL_query/SEARCH_SYNTAX). 

```sql
%%thanosql
PRINT IMAGE 
AS (
    SELECT image_path, search_result 
    FROM (
        SEARCH IMAGE 
        USING my_image_search_model 
        OPTIONS (
            search_by='image',
            search_input='thanosql-dataset/mnist_data/test/923.jpg',
            emb_col='convert_result',
            result_col='search_result'
            )
        AS 
        SELECT * 
        FROM mnist_test
        )
    ORDER BY search_result DESC 
    LIMIT 4
    )
```