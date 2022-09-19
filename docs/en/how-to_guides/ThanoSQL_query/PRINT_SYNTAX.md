---
title: PRINT
---

# __PRINT__

## __1. PRINT Statement__
The "__PRINT__" statement allows users to to output images, audio, and video files.

## __2. PRINT Syntax__

The "__PRINT__" syntax
```sql
%%thanosql
PRINT IMAGE | AUDIO | VIDEO
AS
[dataset_to_output]
```

The "__PRINT__" syntax with an "__OPTIONS__" clause.

```sql
%%thanosql
PRINT IMAGE | AUDIO | VIDEO
OPTIONS (
    image_col | audio_col | video_col = [image_path_column_name]
    )
AS
[dataset_to_output]
```

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
FROM junyong_img
```

!!! note ""
    - `junyong_img` : Table containing paths of the image files

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
FROM junyong_aud
```

[![IMAGE](/img/thanosql_syntax/query/PRINT/PRINT_img1.png)](/img/thanosql_syntax/query/PRINT/PRINT_img1.png)

!!! note ""
    - `junyong_aud` : Table containing paths of the audio files

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
FROM junyong_vid
```

!!! note ""
    - `junyong_vid` : Table containing paths of the video files

### __3.4 Print with a subquery__

The following statement outputs the results of "__SEARCH__" statement created in the nested [SEARCH](/en/how-to_guides/ThanoSQL_query/SEARCH_SYNTAX). 

```sql
%%thanosql
PRINT IMAGE AS(
    SELECT image_path as image, query1_score
    FROM (
        SEARCH IMAGE text='12345'
        USING clip_en
        AS
        SELECT *
        FROM mnist_dataset)
    ORDER BY query1_score DESC
    LIMIT 10
    )
```
