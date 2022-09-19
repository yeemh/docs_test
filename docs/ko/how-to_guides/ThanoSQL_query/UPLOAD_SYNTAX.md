---
title: UPLOAD MODEL
---

# __UPLOAD MODEL__

## __1. UPLOAD MODEL 문__

사용자는 "__UPLOAD MODEL__" 구문을 사용하여 직접 만든 모델들을 업로드하여 ThanoSQL 상에서 이용할 수 있습니다. 

## __2. UPLOAD MODEL 구문__
```sql
%%thanosql
UPLOAD MODEL [사용자 지정 모델 이름] 
OPTIONS (
    overwrite=True, 
    framework=[모델 프레임워크]
    ) 
FROM [업로드할 모델 경로]
```

!!! note "__Note__"     
    UPLOAD MODEL 구문의 OPTIONS로는 overwrite 상태와 모델 framework를 지정할 수 있습니다. Overwrite 상태는 지정을 하지 않을 경우에는 deafult 값으로 False 로 지정됩니다. Framework는 사용자가 UPLOAD 구문 사용 시 직접 지정을 해주어야 합니다. 현재 지정 가능한 framework는 pytorch 입니다.
    
!!! Failure "__Caution__"
    현제 UPLOAD MODEL 구문은 Pytorch 기반의 모델들만 사용 가능합니다. 
