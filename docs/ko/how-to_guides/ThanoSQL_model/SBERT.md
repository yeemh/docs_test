---
title: SBERT
---

# __SBERT__

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

## __BUILD MODEL 구문__

"__BUILD MODEL__" 구문을 사용하여 인공지능 모델을 개발할 수 있습니다. "__BUILD MODEL__" 구문은 "__AS__" 뒤에 나오는 query_expr을 통해 정의된 데이터 세트를 사용하여 모델을 학습할 수 있습니다.

```sql
query_statement:
    query_expr

BUILD MODEL (model_name_expression)
USING { SBERTKo | SBERTEn }
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```

__OPTIONS 절__

```sql
OPTIONS (
    (text_col=column_name),
    [max_epochs=VALUE],
    [batch_size=VALUE],
    [learning_rate=VALUE],
    [overwrite={True|False}]
    )
```

"__OPTIONS__" 절은 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.

- "text_col": 데이터 테이블에서 학습의 대상이 될 텍스트를 담은 컬럼의 이름입니다. (str, default: 'text')
- "batch_size": 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (int, optional, default: 16)
- "max_epochs": 모든 학습 데이터 세트를 학습하는 횟수를 설정합니다. (int, optional, default: 1)
- "learning_rate": 모델의 학습률입니다. (float, optional, default: 3e-5)
- "overwrite": 동일 이름의 모델이 존재하는 경우 덮어쓰기 가능 유무를 설정합니다. True일 경우 기존 모델은 새로운 모델로 변경됩니다. (bool, optional, True|False, default: False)

__BUILD MODEL 예시__

[텍스트로 텍스트 검색하기](/ko/tutorials/thanosql_search/search_text_by_text/)에서 "__BUILD MODEL__" 구문 사용 예시를 확인하실 수 있습니다.

```sql
%%thanosql
BUILD MODEL nsmc_text_search_model
USING SBERTKo
OPTIONS (
    text_col='document',
    overwrite=True
)
AS
SELECT *
FROM nsmc_train
```

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
    (text_col=column_name),
    [table_name=expression],
    [batch_size=VALUE],
    [result_col=column_name]
    )
```

"__OPTIONS__" 절은 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.

- "text_col": 데이터 테이블에서 수치화의 대상이 될 텍스트를 담은 컬럼의 이름입니다. (str, default: 'text')
- "table_name": ThanoSQL 워크스페이스 데이터베이스 내에 저장될 테이블 이름입니다. 기존에 사용한 테이블 이름으로 지정할 경우, 기존 테이블은 'convert_result' 컬럼을 추가한 테이블로 대체됩니다. 지정하지 않을 시 테이블을 저장하지 않습니다. (str, optional)
- "batch_size": 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (int, optional, default: 16)
- "result_col": 데이터 테이블에서 수치화 결과를 담을 컬럼 이름을 설정합니다. (str, optional, default: 'convert_result')

__CONVERT 예시__

[텍스트로 텍스트 검색하기](/ko/tutorials/thanosql_search/search_text_by_text/)에서 "__CONVERT__" 구문 사용 예시를 확인하실 수 있습니다.

```sql
%%thanosql
CONVERT USING nsmc_text_search_model
OPTIONS (
    text_col='document',
    table_name='nsmc_test',
    batch_size=32,
    result_col='convert_result'
    )
AS
SELECT *
FROM nsmc_test
```

## __SEARCH TEXT 구문__

"__SEARCH TEXT__" 구문을 사용하여 원하는 문서를 검색할 수 있습니다.

```sql
query_statement:
    query_expr

SEARCH TEXT
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
    [result_col=column_name]
    )
```

"__OPTIONS__" 절은 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.

- "search_by": 검색할 때 사용할 이미지|텍스트|오디오|비디오 타입을 설정합니다. (str)
- "search_input": 검색할 때 사용할 입력값입니다. (str)
- "emb_col": 데이터 테이블에서 수치화된 결과를 담은 컬럼의 이름입니다. (str)
- "result_col": 데이터 테이블에서 검색 결과를 담을 컬럼 이름을 설정합니다. (str, optional, default: 'search_result')

__SEARCH TEXT 예시__

[텍스트로 텍스트 검색하기](/ko/tutorials/thanosql_search/search_text_by_text/)에서 "__SEARCH TEXT__" 구문 사용 예시를 확인하실 수 있습니다.

```sql
%%thanosql
SELECT document, label, score
FROM (
    SEARCH TEXT
    USING nsmc_text_search_model
    OPTIONS (
        search_by='text',
        search_input='기분이 좋아지는 작품',
        emb_col='convert_result',
        result_col='score'
        )
    AS
    SELECT *
    FROM nsmc_test
    )
ORDER BY score DESC
LIMIT 10
```

## __SEARCH KEYWORD 구문__

"__SEARCH KEYWORD__" 구문을 사용하여 키워드를 추출할 수 있습니다.

```sql
query_statement:
    query_expr

SEARCH KEYWORD 
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
    [lang={en|ko}],
    (text_col=column_name),
    [ngram_range=[VALUE,VALUE]],
    [top_n=VALUE],
    [diversity=VALUE],
    [use_stopwords={True|False}],
    [threshold=VALUE]
    )
```

"__OPTIONS__" 절은 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.

- "lang": 사용할 언어를 설정합니다. (str, optional, 'ko'|'en' default: 'ko')
- "text_col": 데이터 테이블에서 키워드 추출의 대상이 될 텍스트를 담은 컬럼의 이름입니다. (str, default: 'text')
- "ngram_range": 키워드의 최소 단어 수와 최대 단어 수를 정합니다. (list[int, int], optional, default: [1, 2])
- "top_n": 추출할 키워드의 수입니다. 유사도가 높은 순서대로 출력합니다. (int, optional, default: 5)
- "diversity": 추출될 키워드의 다양성입니다. 높을 수록 기존에 추출된 키워드와 유사한 키워드는 다시 추출되지 않습니다. 0 <= diversity <= 1 (float, optional, default: 0.5)
- "use_stopwords": 큰 의미가 없는 단어를 제외할지 정합니다. (bool, optional, True|False, default: True)
- "threshold": 추출할 키워드의 유사도 수치의 최소값입니다. (float, optional, default: 0.0)

__SEARCH KEYWORD 예시__

[텍스트로 텍스트 검색하기](/ko/tutorials/thanosql_search/search_text_by_text/)에서 "__SEARCH KEYWORD__" 구문 사용 예시를 확인하실 수 있습니다.

```sql
%%thanosql
SELECT * FROM (
    SELECT document, label, json_array_elements(keyword -> 'keyword') AS keywords, json_array_elements(keyword -> 'score') AS score
        FROM (
            SEARCH KEYWORD
            USING nsmc_text_search_model
            OPTIONS (
                text_col='document',
                use_stopwords=True
                )
            AS
            SELECT *
            FROM nsmc_test
            LIMIT 10
        )
    )
WHERE score > 0.5
```