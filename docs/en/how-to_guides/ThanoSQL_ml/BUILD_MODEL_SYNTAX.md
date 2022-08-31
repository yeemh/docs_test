---
title: Train the model
---

# **Train the model (BUILD MODEL)**

## Preface

- Updated Date : {{ git_revision_date_localized }}

## **1. BUILD MODEL Syntax Overview**

Users can simply use the "**BUILD MODEL**" syntax to develop the desired AI model without the need for data science expertise.

## **2. BUILD MODEL Syntax**

```sql
%%thanosql
BUILD MODEL [Custom_Model_Name]
USING [AI_model_to_use]
OPTIONS ([Option_values_​​required_when_creating_an_AI_model])
AS
[data_set_to_use]

```

!!! NOTE
    "The option values used by **OPTIONS**" apply differently depending on the AI model you are using. The "**OPTIONS**" for each AI model is available [in the options documentation page.](/en/how-to_guides/OPTIONS/)

## **3. BUILD MODEL Syntax example**

### **3-1. Using Auto_ML Models to Create Classification Models**

The example below uses the syntax "**BUILD MODEL**" to create a classification model using a user-defined <mark style="background-color:#E9D7FD ">Titanic_classification</mark> model provided by ThanoSQL ["AutomlClassifier"](https://www.automl.org/automl/).

If you're curious about the whole process, try [Creating a Titanic Survivor Classification Model using Auto-ML](/en/tutorials/thanosql_ml/classification/automl_classification/).

```sql
%%thanosql
BUILD MODEL titanic_classification
USING AutomlClassifier
OPTIONS (
    target='survived',
    impute_type='iterative',
    features_to_drop=["name", 'ticket', 'passengerid', 'cabin'],
    overwrite = True
    )
AS
SELECT *
FROM titanic_train
```

!!! note "Artificial intelligence models available with '**BUILD MODEL**'"
    - Auto-ML Classification model - AutomlClassifier
    - Auto-ML Regression model - AutomlRegressor
    - ConvNeXT Model - ConvNeXt_Tiny , ConvNeXt_Base
    - EfficientNet Model - EfficientNetV2S , EfficientV2M
    - Albert Model - AlbertKo, AlbertEn
    - Electra Model - ElectraKo , ElectraEn
    - Wav2Vec2 Model - Wav2Vec2Ko , Wav2Vec2En
