---
title: CLIP
---

# __CLIP__

__표기법 규칙__ 

- 괄호 `()`는 ^^리터럴^^ 괄호를 나타냅니다.  
- 중괄호 {}는 옵션 조합을 묶는 데 사용됩니다.  
- 대괄호 `[]`는 선택적 절을 나타냅니다.   
- 대괄호 [ , ... ] 안에 있는 쉼표 다음에 오는 줄임표는 앞의 항목이 쉼표로 구분된 
목록으로 반복될 수 있음을 의미합니다.
- 세로 막대 `|`는 논리 `OR`를 나타냅니다.  
- VALUE는 값을 의미합니다. 

!!! note "" 
    __리터럴__ : 고정되거나 변경할 수 없는 값을 의미하며 상수(Constant)라고도 불립니다. 
    > 각 리터럴은 테이블에서 컬럼과 같은 특별한 자료형을 가지고 있습니다.


## __CREATE TABLE 구문__

"__CREATE TABLE__" 구문을 사용하여 이미지 데이터의 수치화 벡터를 포함한 데이터 테이블을 생성할 수 있습니다.

```sql
CREATE TABLE (table_name_expression)
USING clip_en
OPTIONS (
    expression [ , ...]
    )
FROM
(query_expresison)
```
!!!faq ""
    본 쿼리를 통해서 USING 뒤에 나온 clip_en 모델을 사용하여 도출된 수치화 벡터를 CREATE TABLE 뒤에 나온 table_name_expression 이름으로 저장합니다.



__OPTIONS 절__

```sql
OPTIONS(
    (path_type = column_name),
    (data_type = column_name),
    [file_type = VALUE],
    [batch_size = VALUE],
    [overwrite = {True | False}]
    )
```

"__OPTIONS__" 절은 CLIP의 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.

- "path_type" : 데이터 테이블에서 오디오 파일들의 경로를 담은 컬럼을 설정합니다. (DEFAULT: "audio_path")
- "data_type" : 데이터의 형식입니다.
- "file_type" : 이미지의 확장자 형식입니다.
- "batch_size" : 한 번의 예측에서 읽는 데이터 세트 묶음의 크기입니다. (DEFAULT : 16)
- "overwrite" : 동일 이름의 모델이 존재하는 경우 덮어쓰기 가능 유무를 설정합니다. True일 경우 기존 모델은 새로운 모델로 변경됩니다. (DEFAULT : False)

## __CONVERT 구문__

"__CONVERT__" 구문은 기존에 존재하던 테이블에서 이미지 데이터를 수치화한 벡터로 변환하고 이를 사용할 데이터 테이블에 추가합니다.

```sql
CONVERT USING clip_en
OPTIONS(
    (table_name = expression),
    (image_col = column_name),
    [batch_size = VALUE]
    )
AS
(query_expr)
```

!!!faq ""
    본 쿼리를 통해서 USING 뒤에 나온 모델인 clip_en을 사용합니다. clip의 경우 현재 Build를 제공하지 않기 때문에 베이스 모델인 clip_en을 사용합니다.

__OPTIONS 절__

```sql
OPTIONS(
    (table_name=expression),
    (image_col=column_name),
    [batch_size=VALUE]
)
```

"__OPTIONS__" 절은 모델에서 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.

- "table_name" : 새로 만들어질 테이블의 이름입니다.
- "image_col" : 테이블에서 이미지의 경로를 담고 있는 컬럼의 이름입니다. (DEFAULT : 'image_path')
- "batch_size" : 한 번의 예측에서 읽는 데이터 세트 묶음의 크기입니다. (DEFAULT : 16)


__CONVERT 예시__

[텍스트로 이미지 검색하기](/tutorials/thanosql_search/search_image_by_text/)에서 해당 알고리즘 구문 사용 예시를 확인하실 수 있습니다.


```sql
%%thanosql
CONVERT USING tutorial_search_clip
OPTIONS (
    image_col="image_path", 
    table_name="unsplash_data", 
    batch_size=128
    )
AS 
SELECT * 
FROM unsplash_data
```

## __SEARCH 구문__

"__SEARCH__" 구문을 사용하여 수치화을 생성한 테이블에서 원하는 이미지를 검색할 수 있습니다.

``` sql
SEARCH IMAGE ({text|texts|image|images} = expression)
USING clip_en
AS 
(query_expr)
```
!!! note ""
    text, texts, image, images 중 하나를 입력으로 받아야 합니다. text와 texts, image와 images는 각각 동일합니다. 입력은 string (예: 'a black cat', 'data/image/image01.jpg'), 또는 list of string (예: ['a black cat', 'a orange cat'], ['data/image/image01.jpg', 'data/image/image02.jpg']) 이어야 합니다.

!!! faq ""
    본 쿼리를 통해서 USING 뒤에 나온 모델인 clip_en을 사용합니다. clip의 경우 현재 Build를 제공하지 않기 때문에 베이스 모델인 clip_en을 사용합니다.

__SEARCH 예시__

[텍스트로 이미지 검색하기](/tutorials/thanosql_search/search_image_by_text/)에서 해당 알고리즘 구문 사용 예시를 확인하실 수 있습니다.

```sql
%%thanosql
SEARCH IMAGE text="a black cat"
USING tutorial_search_clip
AS 
SELECT * 
FROM unsplash_data
```