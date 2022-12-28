---
title: PRINT
---

# __PRINT__

## __1. PRINT 문__

사용자는 "__PRINT__" 구문을 사용하여 이미지, 오디오 그리고 비디오 파일을 출력할 수 있습니다.

## __2. PRINT 구문__

"__PRINT__" 구문

```sql
query_statement:
    query_expr

PRINT { IMAGE | AUDIO | VIDEO }
AS
(query_expr)
```

"__OPTIONS__"를 사용한 "__PRINT__" 구문

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

!!! note "쿼리 세부 정보"
    - "__OPTIONS__" 절에서 매개변수의 값을 기본값으로부터 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.
        - "image_col | audio_col | video_col": 프린트 할 컬럼명을 지정합니다. (str, default: 'image_path'|'audio_path'|'video_path')

## __3. PRINT 예시__

### __3.1 이미지 출력__

"__PRINT__" 쿼리문을 사용하여 테이블에 있는 이미지 파일들을 출력합니다.

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
    - "image_table": 이미지 파일 경로가 저장되어 있는 데이터 테이블

### __3.2 오디오 출력__

"__PRINT__" 쿼리문을 사용하여 데이터 테이블에 있는 오디오 파일들을 출력합니다.

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
    - "audio_table": 오디오 파일 경로가 저장되어 있는 데이터 테이블


### __3.3 비디오 출력__

"__PRINT__" 쿼리문을 사용하여 데이터 테이블에 있는 비디오 파일들을 출력합니다.

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
    - "video_table": 비디오 파일 경로가 저장되어 있는 데이터 테이블

### __3.4 서브 쿼리를 사용하여 출력하기__

다음 쿼리는 [SEARCH](/ko/how-to_guides/ThanoSQL_query/SEARCH_SYNTAX)에서 만들었던 "__SEARCH__" 쿼리문을 "__PRINT__" 구문의 서브 쿼리로 사용하여 "__SEARCH__"의 결과 테이블을 바로 출력합니다.

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