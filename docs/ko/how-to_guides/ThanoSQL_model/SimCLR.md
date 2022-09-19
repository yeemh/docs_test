---
title: SimCLR
---

# __SimCLR__

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


## __BUILD MODEL 구문__ 
​
이 "__BUILD MODEL__" 구문을 사용하여 인공지능 모델을 개발할 수 있습니다. 
"__BUILD MODEL__" 표현식은 "__AS__" 뒤에 나오는 query_expr을 통해 정의된 데이터 세트를 학습할 수 있습니다. 
​
``` sql
BUILD MODEL (model_name_expression)
USING SimCLR
OPTIONS (
    expression [ , ...]
)
AS 
(query_expr)
``` 

!!!faq ""
    본 쿼리를 통해서 USING 뒤에 나오는 베이스 인공지능 모델을 model_name_expression 이름으로 저장합니다.

​
__OPTIONS 절__
​
```sql
OPTIONS(
    (image_col = VALUE),
    (filename_col = VALUE),
    [label_col = VALUE],
    [max_epochs = VALUE],    
    [batch_size = VALUE],
    [overwrite = {True | False}]    
)
```
​
"__OPTIONS__" 절은 SimCLR 수치화 모델의 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.  

- "image_col" : 데이터 테이블에서 이미지의 경로를 설정합니다. (DEFAULT : "path")
- "filename_col" : 데이터 테이블에서 이미지 파일 이름을 담은 컬럼을 설정합니다. (DEFAULT : "file_name")
- "label_col" : 이미지 라벨을 담은 컬럼입니다. (DEFAULT : "label")
- "max_epochs" : 모델 학습 횟수를 설정합니다. (DEFAULT : 5)  
- "batch_size" : 학습 때 사용되어지는 데이터 묶음 속의 데이터 수를 설정합니다. (DEFAULT : 256)
- "overwrite" : 동일 이름의 모델이 존재하는 경우 덮어쓰기 가능 유무를 설정합니다. True일 경우 기존 모델은 새로운 모델로 변경됩니다. (DEFAULT : False)


__BUILD MODEL 예시__  

[이미지로 이미지 검색하기](/tutorials/thanosql_search/search_image_by_image/)에서 해당 알고리즘 구문 사용 예시를 확인하실 수 있습니다. 
​
```sql
%%thanosql
BUILD MODEL my_image_search_model
USING SimCLR
OPTIONS (
    image_col="image_path",
    max_epochs=1,
    overwrite=True
    )
AS 
SELECT * 
FROM mnist_train
```

## __CREATE TABLE 구문__

이 "__CREATE TABLE__" 구문을 사용하여 이미지별 폴더 경로 정보가 포함되어 있는 테이블 없이도 이미지 폴더 경로를 사용하여 수치화 변환이 가능합니다. 
"__CREATE TABLE__" 표현식은 "__FROM__" 뒤에 나오는 이미지 폴더 경로의 이미지 파일들을 수치화하여 테이블로 저장합니다. 
​
``` sql
CREATE TABLE (table_name_expression) 
USING (model_name_expression)
OPTIONS (
    expression [ , ...]
    )
FROM
(query_expr)
``` 
!!!faq ""
    본 쿼리를 통해서 USING 뒤에 나온 SimCLR 모델을 사용하여 도출된 수치화 벡터를 CREATE TABLE 뒤에 나온 table_name_expression 이름으로 저장합니다.

​
__OPTIONS 절__
​
```sql
OPTIONS(
    (path_type = {'folder'|'file'}),    
    (data_type = {'image'|'audio'|'video'}),
    (file_type = LIST),
    [overwrite = {True | False}]
    )
```
​
"__OPTIONS__" 는 이미지 수치화를 위한 이미지 파일의 속성값들을 정의합니다. 각 매개변수의 의미는 아래와 같습니다.  

- "path_type" : 데이터가 저장되어 있는 파일 경로의 타입을 설정합니다.(folder|file)

- "data_type" : 입력하는 비정형 데이터의  종류를 설정합니다. (image|audio|video)

- "file_type" : 대상 파일의 확장자를 리스트로 정의하여 줍니다. (ex. ['.jpg'], ['.png'])
​
- "overwrite" : 동일 이름의 모델이 존재하는 경우 덮어쓰기 가능 유무를 설정합니다. True일 경우 기존 모델은 새로운 모델로 변경됩니다. (DEFAULT : False)

## __CONVERT 구문__

이 "__CONVERT__" 구문을 사용하여 기존 이미지들의 경로가 포함되어 있는 데이터 세트를 사용하여 수치화 된 결과를 기존의 데이터셋에 새로운 컬럼으로 저장합니다. 새로운 수치화 모델을 사용할때마다 새로운 수치화 컬럼이 추가되어 수치화 결과 별 비교가 용이합니다.  
​
```sql
CONVERT USING (model_name_expression)
OPTIONS(
    expression [ , ...] 
    )
AS 
(query_expr)
``` 

!!! faq ""
    본 쿼리를 통해서 USING 뒤에 나온 모델인 model_name_expression을 사용합니다.

__OPTIONS 절__

```sql
OPTIONS(
    (table_name = VALUE)  
    )
```
"__OPTIONS__" 절은 SimCLR 수치화 모델의 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다. 
​

- "table_name" : 새로운 수치화 결과를 저장할 테이블 이름을 설정합니다.

__CONVERT 예시__

[이미지로 이미지 검색하기](/tutorials/thanosql_search/search_image_by_image/)에서 해당 알고리즘 구문 사용 예시를 확인하실 수 있습니다. 
​
```sql
%%thanosql
CONVERT USING my_image_search_model
OPTIONS (
    table_name= "mnist_test",
    image_col="image_path"
    )
AS 
SELECT * 
FROM mnist_test
```

## __SEARCH 구문__

"__SEARCH__" 구문을 사용하여 수치화을 생성한 테이블에서 원하는 이미지를 검색할 수 있습니다.

``` sql
SEARCH IMAGE (images = expression)
USING (model_name_expression)
AS 
(query_expr)
```
!!! note ""
    사용자로부터 images를 입력으로 받아야 합니다. 입력은 string (예: 'a black cat', 'data/image/image01.jpg')이어야 합니다.

!!! faq ""
    본 쿼리를 통해서 USING 뒤에 나온 모델인 model_name_expression을 사용합니다.


__SEARCH 예시__

[이미지로 이미지 검색하기](/tutorials/thanosql_search/search_image_by_image/)에서 해당 알고리즘 구문 사용 예시를 확인하실 수 있습니다.

```sql
%%thanosql
SEARCH IMAGE images='tutorial_data/mnist_data/test/923.jpg' 
USING my_image_search_model 
AS 
SELECT * 
FROM mnist_test
```