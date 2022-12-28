---
title: GET
---

# __GET__

## __1. GET Statement__
The "__GET__" statement allows users to download the lastest ThanoSQL pre-built models and tutorial datasets. 

## __2. GET Syntax__

The "__GET THANOSQL MODEL__" statement downloads the ThanoSQL pre-built models to the user's workspace. 

```sql
GET THANOSQL MODEL (ThanoSQL_model_name_expression)
OPTIONS (
    model_name=(model_name_expression),
    overwrite=True
    ) 
```

!!! note "__Note__"    
    - You can use the __LIST THANOSQL MODEL__ statement to view a list of the ThanoSQL pre-built models.   
    - If you do not include the "model_name" and provide a "model_name_expression", pre-built models will be saved with their default names. 

!!! note "__Query Details__"
    - The "__OPTIONS__" clause allows you to change the value of a parameter. The definition of each parameter is as follows:
        - "model_name": the model name to store a given model in the ThanoSQL workspace (str, optional)
        - "overwrite": determines whether to overwrite a model if it already exists. If set as True, the old model is replaced with the new model (bool, optional, True|False, default: False)

The "__GET THANOSQL DATASET__" statement downloads the tutorial datasets to the user's workspace. 

```sql
GET THANOSQL DATASET [ThanoSQL_dataset_name_expression]
OPTIONS (overwrite=True)
```

!!! note "__Note__"    
    - You can use the __LIST THANOSQL DATASET__ statement to view a list of the ThanoSQL datasets.  
    - Datasets cannot be renamed.

!!! note "__Query Details__"
    - The "__OPTIONS__" clause allows you to change the value of a parameter. The definition of each parameter is as follows:
        - "overwrite": determines whether to overwrite a dataset if it already exists. If set as True, the old dataset is replaced with the new dataset (bool, optional, True|False, default: False)

## __3. GET Example__ 

### __3.1 GET THANOSQL MODEL Example__

```sql
%%thanosql
GET THANOSQL MODEL clip
OPTIONS (
    model_name='tutorial_search_clip',
    overwrite=True
    )
```

### __3.2 GET THANOSQL DATASET Example__

```sql
%%thanosql
GET THANOSQL DATASET unsplash_data
OPTIONS (overwrite=True)
```
