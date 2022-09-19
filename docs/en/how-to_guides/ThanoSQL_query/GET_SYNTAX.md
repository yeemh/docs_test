---
title: GET
---

# __GET__

## __1. GET Statement__
The "__GET__" statement allows users to download the lastest ThanoSQL pre-built models and tutorial datasets. 
## __2. GET Syntax__

The "__GET THANOSQL MODEL__" statement downloads the ThanoSQL pre-built models to the user's workspace. 

```sql
%%thanosql
GET THANOSQL MODEL [ThanoSQL_pre-built_model_name] 
OPTIONS (
    overwrite=True
) 
AS 
[custom_model_name]
```

!!! note "__Note__"    
    You can use the `LIST THANOSQL MODEL` statement to view a list of the ThanoSQL pre-built models. There is only overwrite option in the `GET` statement and if not specified, the default value of the overwrite status will be set as False. If you do not include the `AS` clause and `custom_model_name`, pre-built models will be saved with default names. 


The "__GET THANOSQL DATASET__" statement downloads the tutorial datasets to the user's workspace. 

```sql
%%thanosql
GET THANOSQL DATASET [ThanoSQL_dataset_name]
OPTIONS (
    overwrite=True 
)
```

!!! note "__Note__"    
    You can use the `LIST THANOSQL DATASET` statement to view a list of the ThanoSQL datasets. There is only overwrite option in the `GET` statement and if not specified, the default value of the overwrite status will be set as False. Datasets cannot be renamed. 

