---
title: UPLOAD MODEL
---

# __UPLOAD MODEL__

## __1. UPLOAD MODEL Statement__
The "__UPLOAD MODEL__" statement allows users to transfer their custom models to be used within the ThanoSQL environment. 

## __2. UPLOAD MODEL Syntax__
```sql
%%thanosql
UPLOAD MODEL [user_defined_model_name] 
OPTIONS (
    overwrite=True, 
    framework=[model_framework]
    ) 
FROM [model_path_to_upload]
```

!!! note "__Note__"     
    The "__OPTIONS__" clause in the `UPLOAD MODEL` statement allows users to specify the overwrite status and model framework. If the overwrite status is not specified, the default value will be set as False. The model framework must be specified directly by the user when using the `UPLOAD MODEL` statement every time. Currently, the only available framework is `Pytorch`.
    
!!! Failure "__Caution__"
    The current `UPLOAD MODEL` statement is only available for `Pytorch` based models.
