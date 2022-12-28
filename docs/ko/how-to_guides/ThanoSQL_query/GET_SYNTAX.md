---
title: GET
---

# __GET__

## __1. GET 문__

사용자는 "__GET__" 구문을 사용하여 가장 최신의 ThanoSQL의 Pre-built 모델들과 튜토리얼 데이트 세트들을 받아올 수 있습니다.

## __2. GET 구문__

"__GET THANOSQL MODEL__" 구문을 사용하여 ThanoSQL의 Pre-built 모델을 사용자의 워크스페이스로 가지고 올 수 있습니다.

```sql
GET THANOSQL MODEL (ThanoSQL_model_name_expression) 
OPTIONS (
    model_name=(model_name_expression),
    overwrite=True
    )
```

!!! note "__Note__"
    - "__LIST THANOSQL MODEL__" 구문을 사용하여 ThanoSQL pre-built 모델의 리스트를 확인할 수 있습니다.
    - "__GET THANOSQL MODEL__" 구문 사용 시 `model_name`과 `model_name_expression`을 표기하지 않는다면 ThanoSQL pre-built 모델의 이름으로 저장됩니다.

!!! note "쿼리 세부 정보"
    - "__OPTIONS__" 절에서 매개변수의 값을 기본값으로부터 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.
        - "model_name": 저장할 모델의 이름을 설정합니다. (str, optional)
        - "overwrite": 동일 이름의 모델이 존재하는 경우 덮어쓰기 가능 유무를 설정합니다. True일 경우 기존 모델은 새로운 모델로 변경됩니다. (bool, optional, True|False, default: False)

"__GET THANOSQL DATASET__" 구문을 사용하여 ThanoSQL 튜토리얼에 사용되는 테이터 세트를 사용자의 워크스페이스로 가지고 올 수 있습니다.

```sql
GET THANOSQL DATASET [ThanoSQL_dataset_name_expression]
OPTIONS (overwrite=True)
```

!!! note "__Note__"
    - "__LIST THANOSQL DATASET__" 구문을 사용하여 ThanoSQL 데이터 세트의 리스트를 확인할 수 있습니다.
    - "__GET THANOSQL DATASET__" 구문은 지정된 데이터 세트의 이름을 바꿀 수 없습니다.

!!! note "쿼리 세부 정보"
    - "__OPTIONS__" 절에서 매개변수의 값을 기본값으로부터 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.
        - "overwrite": 동일 이름의 데이터 세트가 존재하는 경우 덮어쓰기 가능 유무를 설정합니다. True일 경우 기존 데이터 세트는 새로운 데이터 세트로 변경됩니다. (bool, optional, True|False, default: False)

## __3. GET 예시__

### __3.1 GET THANOSQL MODEL 예시__

```sql
%%thanosql
GET THANOSQL MODEL clip
OPTIONS (
    model_name='tutorial_search_clip',
    overwrite=True
    )
```

### __3.2 GET THANOSQL DATASET 예시__

```sql
%%thanosql
GET THANOSQL DATASET unsplash_data
OPTIONS (overwrite=True)
```