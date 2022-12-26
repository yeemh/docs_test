---
title: UPLOAD MODEL
---

# __UPLOAD MODEL__

## __1. UPLOAD MODEL 문__

사용자는 "__UPLOAD MODEL__" 구문을 사용하여 직접 만든 모델을 업로드하여 ThanoSQL 상에서 이용할 수 있습니다.

## __2. UPLOAD MODEL 구문__

```sql
UPLOAD MODEL (model_name_expression)
OPTIONS (
    framework=[model_framework],
    overwrite=True
    )
FROM [model_path_expression]
```

!!! note "쿼리 세부 정보"
    - "OPTIONS" 절은 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.
        - "framework": 모델의 프레임워크를 설정합니다. (str, default: 'pytorch')
        - "overwrite": 동일 이름의 모델이 존재하는 경우 덮어쓰기 가능 유무를 설정합니다. True일 경우 기존 모델은 새로운 모델로 변경됩니다. (bool, optional, True|False, default: False)

!!! Failure "__Caution__"
    - 현재 "__UPLOAD MODEL__" 구문은 `Pytorch` 기반의 모델들만 사용 가능합니다.

## __3. UPLOAD MODEL 예시__

```sql
%%thanosql
UPLOAD MODEL beans_mobilevit
OPTIONS (
    framework="pytorch",
    overwrite=True
    )
FROM "trained_model.pth"
```