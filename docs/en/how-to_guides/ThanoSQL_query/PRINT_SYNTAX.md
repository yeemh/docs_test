---
title: Print the result
---

# **Print the result (PRINT)**

## Preface

- Updated Date : {{ git_revision_date_localized }}

## **1. PRINT Syntax Overview**

Users can use the "**PRINT**" syntax to output images, audio, and video files. You can also use subqueries to immediately output results from the "**SEARCH**" syntax.

## **2. PRINT Syntax**

"**PRINT**" Syntax

```sql
%%thanosql
PRINT IMAGE | AUDIO | VIDEO
AS
[data_set_to_output]
```

"**PRINT**" syntax using OPTIONS

```sql
%%thanosql
PRINT IMAGE | AUDIO | VIDEO
OPTIONS(
    image_col | audio_col | video_col = [image_path_column_name]
    )
AS
[data_set_to_output]
```

## **3. PRINT Syntax example**

### **3.1 Image Print**

Outputs image files in the data table using the "**PRINT**" query statement.

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
    - `junyong_img` : Data table containing image file paths

### **3.2 Audio Print**

Outputs audio files in the data table using the "**PRINT**" query statement.

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
    - `junyong_aud` : Data table containing audio file paths

### **3.3 Video Print**

Outputs video files in the data table using the "**PRINT**" query statement.

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
    - `junyong_vid` : Data table containing video file paths

### **3.4 Printing using subqueries**

The following query immediately outputs the result table of "**SEARCH**" using the "**SEARCH**" query statement created in the previous [SEARCH query](/en/how-to_guides/ThanoSQL_query/SEARCH_SYNTAX) as a subquery of the "**PRINT**" syntax.

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
