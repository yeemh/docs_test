---
title: Wav2Vec2
---

# __Wav2Vec2__

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

이 "__BUILD MODEL__" 구문을 사용하여 인공지능 모델을 개발할 수 있습니다.
"__BUILD MODEL__" 표현식은 "__AS__" 뒤에 나오는 query_expr을 통해 정의된 데이터 세트를 학습할 수 있습니다.

``` sql
query_statement:
    query_expr

BUILD MODEL (model_name_expression)
USING { Wav2Vec2Ko | Wav2Vec2En }
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```
!!!faq ""
    본 쿼리를 통해서 USING 뒤에 나오는 베이스 인공지능 모델을 model_name_expression 이름으로 저장합니다.

 __OPTIONS 절__

```sql
OPTIONS(
    (audio_col = column_name),
    (text_col = column_name),
    [batch_size = VALUE],
    [epochs = VALUE],
    [learning_rate = VALUE],
    [overwrite = {True | False}]
    )
```

"__OPTIONS__" 절은 오디오 모델에서 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.

- "audio_col" : 데이터 테이블에서 오디오 파일들의 경로를 담은 컬럼을 설정합니다. (DEFAULT : "audio_path")
- "text_col" : 데이터 테이블에서 오디오의 스크립트를 담은 컬럼을 설정합니다. (DEFAULT : "text")
- "batch_size" : 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (DEFAULT : 16)
- "epochs" : 총 몇 번 데이터 세트를 반복할 지를 설정합니다. (DEFAULT : 5)
- "learning_rate" : 모델의 학습률입니다. (DEFAULT : 0.0001)
- "overwrite" : 동일 이름의 모델이 존재하는 경우 덮어쓰기 가능 유무를 설정합니다. True일 경우 기존 모델은 새로운 모델로 변경됩니다. (DEFAULT : False)


 __BUILD MODEL 예시__

[오디오 파일을 받아쓰는 음성 인식 모델 만들기](/tutorials/thanosql_ml/audio_recognition/speech_recognition/)에서 해당 알고리즘 구문 사용 예시를 확인하실 수 있습니다.

```sql
%%thanosql
BUILD MODEL my_speech_recognition_model
USING Wav2Vec2En
OPTIONS (
  audio_col='audio_path',  
  text_col='text',  
  epochs=1,  
  batch_size=4 ,
  overwrite=True 
  )
AS
SELECT *
FROM librispeech_train
```

## __FIT MODEL 구문__

이 "__FIT MODEL__" 구문을 사용하여 인공지능 모델을 재학습할 수 있습니다. "__FIT MODEL__" 표현식은 "__AS__" 뒤에 나오는 query_expr을 통해 정의된 데이터 세트를 재학습할 수 있습니다.

``` sql
query_statement:
    query_expr

FIT MODEL (model_name_expression)
USING (model_name_expression)
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```
!!!faq ""
    본 쿼리를 통해서 USING 뒤에 나온 인공지능 모델을 model_name_expression 이름으로 저장하고 같은 이름에 새로운 모델을 저장합니다.

 __OPTIONS 절__

```sql
OPTIONS(
    (audio_col = column_name),
    (text_col = column_name),
    [batch_size = VALUE],
    [epochs = VALUE],
    [learning_rate = VALUE],
    [overwrite = {True | False}]
    )
```

"__OPTIONS__" 절은 오디오 모델에서 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.

- "audio_col" : 데이터 테이블에서 오디오 파일들의 경로를 담은 컬럼을 설정합니다. (DEFAULT : "audio_path")
- "text_col" : 데이터 테이블에서 오디오의 스크립트를 담은 컬럼을 설정합니다. (DEFAULT : "text")
- "batch_size" : 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (DEFAULT : 16)
- "epochs" : 총 몇 번 데이터 세트를 반복할 지를 설정합니다. (DEFAULT : 5)
- "learning_rate" : 모델의 학습률입니다. (DEFAULT : 0.0001)
- "overwrite" : 동일 이름의 모델이 존재하는 경우 덮어쓰기 가능 유무를 설정합니다. True일 경우 기존 모델은 새로운 모델로 변경됩니다. (DEFAULT : False)


## __PREDICT 구문__

이 "__PREDICT__" 구문을 사용하여 테스트 데이터 세트에 인공지능 모델을 적용하여 예측, 분류, 추천 등의 작업을 수행할 수 있습니다. "__PREDICT__" 표현식은 "__AS__" 뒤에 나오는 query_expr을 통해 정의한 데이터 세트를 전처리할 수 있습니다.

``` sql
query_statement:
    query_expr

PREDICT USING (model_name_expression)
OPTIONS (
    expression [ , ...]
    )
AS
(query_expr)
```
!!!faq ""
    본 쿼리를 통해서 USING 뒤에 나오는 인공지능 모델을 사용합니다.

 __OPTIONS 절__

```sql
OPTIONS(
    (audio_col = column_name),
    [batch_size = VALUE]
    )
```

"__OPTIONS__" 절은 오디오 모델에서 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.

- "audio_col" : 데이터 테이블에서 오디오 파일들의 경로를 담은 컬럼을 설정합니다. (DEFAULT : "audio")
- "batch_size" : 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다. (DEFAULT : 16)


 __PREDICT 예시__

[오디오 파일을 받아쓰는 음성 인식 모델 만들기](/tutorials/thanosql_ml/audio_recognition/speech_recognition/)에서 해당 알고리즘 구문 사용 예시를 확인하실 수 있습니다.

```sql
%%thanosql
PREDICT USING my_speech_recognition_model
OPTIONS (
  audio_col='audio_path'
  )
AS
SELECT *
FROM librispeech_test
```
