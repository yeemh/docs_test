---
title: CONVERT
---

# __CONVERT__

## __1. CONVERT 문__

사용자는 "__CONVERT__" 구문을 사용하여 이미지, 비디오, 음성, 텍스트 등 비정형 데이터를 수치화 알고리즘을 통해 벡터 형식으로 변환합니다.

## __2. CONVERT 구문__

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

!!! note "쿼리 세부 정보"
    - "__OPTIONS__" 절에서 매개변수의 값을 기본값으로부터 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.
        - "table_name": ThanoSQL 워크스페이스 데이터베이스 내에 저장될 테이블 이름입니다. 기존에 사용한 테이블 이름으로 지정할 경우, 기존 테이블은 'convert_result' 컬럼을 추가한 테이블로 대체됩니다. 지정하지 않을 시 테이블을 저장하지 않습니다. (str, optional)

## __3. CONVERT 예시__

!!! note
    - 예시는 한 모델에 특정된 것으로 필요한 옵션 값이나 사용되는 데이터 세트는 모델별로 다를 수 있습니다. 각 모델에 대한 자세한 설명은 [ThanoSQL Model Statement Reference](/ko/how-to_guides/reference/#thanosql-model-statement-reference)를 참고해 주세요.
    - 예시는 특정 모델과 데이터 세트가 존재해야만 작동하므로 그대로 복사하여 사용할 시 정상적으로 실행되지 않을 수 있습니다.

```sql
%%thanosql
CONVERT USING tutorial_search_clip
OPTIONS (
    table_name='unsplash_data',
    image_col='image_path',
    convert_type='image',
    batch_size=128,
    result_col='convert_result'
    )
AS
SELECT *
FROM unsplash_data
```

!!! note "'__CONVERT__ 문'을 사용할 수 있는 인공지능 모델"
    - SimCLR 모델 - SimCLR
    - CLIP 모델 - CLIP
    - XCLIP 모델 - XCLIP
    - SBERT 모델 - SBERTKo, SBERTEn