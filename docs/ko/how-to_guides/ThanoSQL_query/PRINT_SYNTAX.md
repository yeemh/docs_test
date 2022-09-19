---
title: PRINT
---

# __PRINT__

## __1. PRINT 문__

사용자는 "__PRINT__" 구문을 사용하여 이미지, 오디오 그리고 비디오 파일을 출력할 수 있습니다. 

## __2. PRINT 구문__
"__PRINT__" 구문
```sql
%%thanosql
PRINT IMAGE | AUDIO | VIDEO
AS 
[출력할 데이터 세트]
```
OPTIONS 사용한 "__PRINT__" 구문
```sql
%%thanosql
PRINT IMAGE | AUDIO | VIDEO
OPTIONS( 
    image_col | audio_col | video_col = [이미지 경로 열 이름]
    ) 
AS 
[출력할 데이터 세트]
```
## __3. PRINT 예시__

### __3.1 이미지 출력__ 

"__PRINT__" 쿼리문을 사용하여 데이터 테이블에 있는 이미지 파일들을 출력합니다.
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
    - `junyong_img` : 이미지 파일 경로가 저장되어 있는 데이터 테이블

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
FROM junyong_aud
```
[![IMAGE](/img/thanosql_syntax/query/PRINT/PRINT_img1.png)](/img/thanosql_syntax/query/PRINT/PRINT_img1.png)

!!! note ""
    - `junyong_aud` : 오디오 파일 경로가 저장되어 있는 데이터 테이블


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
FROM junyong_vid
```
!!! note ""
    - `junyong_vid` : 비디오 파일 경로가 저장되어 있는 데이터 테이블

### __3.4 서브 쿼리를 사용하여 출력하기__

다음 쿼리는 이전 [SEARCH](/how-to_guides/ThanoSQL_query/SEARCH_SYNTAX)에서 만들었던 "__SEARCH__" 쿼리문을 "__PRINT__" 구문의 서브 쿼리로 사용하여 "__SEARCH__"의 결과 테이블을 바로 출력합니다.

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