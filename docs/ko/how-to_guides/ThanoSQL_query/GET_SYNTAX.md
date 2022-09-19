---
title: GET
---

# __GET__

## __1. GET 문__

사용자는 "__GET__" 구문을 사용하여 현재 ThanoSQL의 Pre-built 모델들("THANOSQL MODEL")과 튜토리얼 데이트 세트들을 받아올 수 있습니다. 

## __2. GET 구문__

"__GET THANOSQL MODEL__" 구문은 ThanoSQL의 Pre-built 모델들을 자신의 워크스페이스로 가지고 올 수 있습니다.

```sql
%%thanosql
GET THANOSQL MODEL [ThanoSQL Pre-built 모델 이름] 
OPTIONS (
    overwrite=True
) 
AS 
[사용자 지정 모델 이름]
```

!!! note "__Note__"    
    LIST THANOSQL MODEL 구문을 사용하여 ThanoSQ의 Pre-built 모델들의 리스트를 확인할 수 있습니다. GET 구문의 OPTIONS로는 overwrite 상태만 지정할 수 있으며 overwrite 상태의 default 값은 False 입니다. GET 구문 샤용 시 AS 와 사용자 지정 모델 이름을 표기하지 않는다면 ThanoSQL Pre-built 모델의 이름으로 저장됩니다. 



"__GET THANOSQL DATASET__" 구문은 ThanoSQL Tutorial에 사용되는 테이터 세트를 자신의 워크스페이스 Dataset 폴더로 가지고 올 수 있습니다. 

```sql
%%thanosql
GET THANOSQL DATASET [ThanoSQL 데이터 세트 이름]
OPTIONS (
    overwrite=True 
)
```

!!! note "__Note__"    
    LIST THANOSQL DATASET 구문을 사용하여 ThanoSQ의 데이터 세트들의 리스트를 확인할 수 있습니다. GET 구문의 OPTIONS로는 overwrite 상태만 지정할 수 있으며 overwrite 상태의 default 값은 False 입니다. GET THANOSQL DATASET 구문은 지정된 데이터 세트의 이름을 바꿀 수 없습니다.  




