---
title: Whisper
---

# __Whisper__

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

## __PREDICT 구문__

"__PREDICT__" 구문을 사용하여 인공지능 모델을 적용하여 예측, 분류, 추천 등의 작업을 수행할 수 있습니다. "__PREDICT__" 구문은 "__AS__" 뒤에 나오는 query_expr을 통해 정의한 데이터 세트를 전처리할 수 있습니다.

```sql
query_statement:
    query_expr

PREDICT USING (model_name_expression)
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```

__OPTIONS 절__

```sql
OPTIONS (
    (audio_col=column_name),
    [batch_size=VALUE],
    (language=expression),
    (task={'transcribe'|'translate'}),
    [result_col=column_name],
    [table_name=expression]
    )
```

"__OPTIONS__" 절은 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.

- "audio_col": 데이터 테이블에서 예측의 대상이 될 오디오 파일들의 경로를 담은 컬럼의 이름입니다. (str, default: 'audio_path')
- "batch_size": 한 번의 예측에서 읽는 데이터 세트 묶음의 크기입니다. (int, optional, default: 16)
- "language": 오디오 파일의 주요 사용 언어를 설정합니다. ‘auto’일 경우 모델이 인식할 수 있는 99가지 언어 중 하나로 인식합니다. (str, default: 'auto')
- "task": 실행할 작업의 종류를 설정합니다. (str, 'transcribe'|'translate', default: 'transcribe')
> "transcribe": 음성 인식을 합니다.  
> "translate": 인식된 음성을 영어로 출력합니다. 이 과정은 ‘한국어 음성‘을 바로 ‘영어 텍스트‘로 번역하는 것으로, 중간에 ‘한국어 텍스트’를 거치지 않는 다는 점이 일반적인 번역 태스크와 다릅니다.
- "result_col": 데이터 테이블에서 예측 결과를 담을 컬럼 이름을 설정합니다. (str, optional, default: 'predict_result')
- "table_name": ThanoSQL 워크스페이스 데이터베이스 내에 저장될 테이블 이름입니다. 기존에 사용한 테이블 이름으로 지정할 경우, 기존 테이블은 'predict_result' 컬럼을 추가한 테이블로 대체됩니다. 지정하지 않을 시 테이블을 저장하지 않습니다. (str, optional)

__PREDICT 예시__

[오디오 파일을 받아쓰는 음성 인식 모델 사용하기](/ko/tutorials/thanosql_ml/audio_recognition/speech_recognition2/)에서 "__PREDICT__" 구문 사용 예시를 확인하실 수 있습니다.

```sql
%%thanosql
PREDICT USING tutorial_whisper_small
OPTIONS (
    audio_col='audio_path',
    language='auto',
    task='transcribe',
    result_col='predict_result',
    table_name='korean_voice'
    )
AS
SELECT *
FROM korean_voice
```

## __EVALUATE 구문__

"__EVALUATE__" 구문을 사용하여 인공지능 모델에 대한 평가 작업을 수행할 수 있습니다. "__EVALUATE__" 구문은 "__AS__" 뒤에 나오는 query_expr을 통해 정의한 데이터 세트를 사용하여 모델을 평가합니다.

```sql
query_statement:
    query_expr

EVALUATE USING (model_name_expression)
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```

__OPTIONS 절__

```sql
OPTIONS (
    (audio_col=column_name),
    [batch_size=VALUE],
    (language=expression),
    (task={'transcribe'|'translate'}),
    (text_col=column_name)
    )
```

"__OPTIONS__" 절은 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.

- "audio_col": 데이터 테이블에서 평가의 대상이 될 오디오 파일들의 경로를 담은 컬럼의 이름입니다. (str, default: 'audio_path')
- "batch_size": 한 번의 평가에서 읽는 데이터 세트 묶음의 크기입니다. (int, optional, default: 16)
- "language": 오디오 파일의 주요 사용 언어를 설정합니다. ‘auto’일 경우 모델이 인식할 수 있는 99가지 언어 중 하나로 인식합니다. (str, default: 'auto')
- "text_col": 데이터 테이블에서 목푯값의 정보를 담은 컬럼의 이름입니다. (str, default: 'text')