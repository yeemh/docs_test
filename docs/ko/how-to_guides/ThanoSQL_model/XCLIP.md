---
title: XCLIP
---

# __XCLIP__

__표기법 규칙__

- 괄호 `()`는 ^^리터럴^^ 괄호를 나타냅니다.
- 중괄호 {}는 옵션 조합을 묶는 데 사용됩니다.
- 대괄호 `[]`는 선택적 절을 나타냅니다.
- 대괄호 [ , ... ] 안에 있는 쉼표 다음에 오는 줄임표는 앞의 항목이 쉼표로 구분된 
목록으로 반복될 수 있음을 의미합니다.
- 세로 막대 `|`는 논리 `OR`를 나타냅니다.
- VALUE는 값을 의미합니다.

!!! note ""
    - __리터럴__: 고정되거나 변경할 수 없는 값을 의미하며 상수(Constant)라고도 불립니다.
    > 각 리터럴은 테이블에서 컬럼과 같은 특별한 자료형을 가지고 있습니다.

## __CONVERT 구문__

"__CONVERT__" 구문은 데이터를 수치화한 벡터로 변환하고 이를 사용할 데이터 테이블에 추가합니다.

```sql
query_statement:
    query_expr

CONVERT USING (model_name_expression)
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```

__OPTIONS 절__

```sql
OPTIONS (
    [table_name=expression],
    (video_col=column_name),
    (text_col=column_name),
    (convert_type={'image'|'text'}),
    [batch_size=VALUE],
    [result_col=column_name]
    )
```

"__OPTIONS__" 절은 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.

- "table_name": ThanoSQL 워크스페이스 데이터베이스 내에 저장될 테이블 이름입니다. 기존에 사용한 테이블 이름으로 지정할 경우, 기존 테이블은 'convert_result' 컬럼을 추가한 테이블로 대체됩니다. 지정하지 않을 시 테이블을 저장하지 않습니다. (str, optional)
- "video_col": 데이터 테이블에서 비디오의 경로를 담은 컬럼의 이름입니다. (str, default: 'video_path')
- "text_col": 데이터 테이블에서 텍스트를 담은 컬럼의 이름입니다. (str, default: 'text')
- "convert_type": 수치화할 파일의 종류를 설정합니다. (str, 'video'|'text', default: 'video')
- "batch_size": 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (int, optional, default: 16)
- "result_col": 데이터 테이블에서 수치화 결과를 담을 컬럼 이름을 설정합니다. (str, optional, default: 'convert_result')

__CONVERT 예시__

[텍스트로 비디오 검색하기](/ko/tutorials/thanosql_search/search_video_by_text/)에서 "__CONVERT__" 구문 사용 예시를 확인하실 수 있습니다.

```sql
%%thanosql
CONVERT USING tutorial_search_xclip
OPTIONS (
    video_col='video_path',
    table_name='kinetics700',
    result_col='convert_result'
    )
AS
SELECT *
FROM kinetics700
```

## __SEARCH VIDEO 구문__

"__SEARCH VIDEO__" 구문을 사용하여 원하는 비디오를 검색할 수 있습니다.

```sql
query_statement:
    query_expr
    
SEARCH VIDEO 
USING (model_name_expression)
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```

__OPTIONS 절__

```sql
OPTIONS (
    (search_by={image|text|audio|video}),
    (search_input=expression),
    (emb_col=column_name),
    [column_name=expression]
    )
```

"__OPTIONS__" 절은 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.

- "search_by": 검색할 때 사용할 이미지|텍스트|오디오|비디오 타입을 설정합니다. (str)
- "search_input": 검색할 때 사용할 입력값입니다. (str)
- "emb_col": 데이터 테이블에서 수치화된 결과를 담은 컬럼의 이름입니다. (str)
- "result_col": 데이터 테이블에서 검색 결과를 담을 컬럼 이름을 설정합니다. (str, optional, default: 'search_result')

__SEARCH VIDEO 예시__

[텍스트로 비디오 검색하기](/ko/tutorials/thanosql_search/search_video_by_text/)에서 "__SEARCH VIDEO__" 구문 사용 예시를 확인하실 수 있습니다.

```sql
%%thanosql
SEARCH VIDEO
USING tutorial_search_xclip
OPTIONS (
    search_by='text',
    search_input='bench press',
    emb_col='convert_result',
    result_col='score'
    )
AS
SELECT *
FROM kinetics700
```