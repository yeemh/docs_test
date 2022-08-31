---
title: Retrain the model
---

# **Retrain the model (FIT MODEL)**

## Preface

- Updated Date : {{ git_revision_date_localized }}

## **1. FIT MODEL Syntax Overview**

Users can simply use the "**FIT MODEL**" syntax to learn using a new set of data that has been added to the model without the need for data science expertise.

## **2. FIT MODEL Syntax**

```sql
%%thanosql
FIT MODEL [Custom_Model_Name]
USING [Existing_trained_model_name | Pre-trained_AI_model_name ]
OPTIONS ([, Option_values_required_when_creating_an_AI_model])
AS
[data_set_to_use]
```

!!! NOTE
    The option values used by "**OPTIONS**" apply differently depending on the AI model to be used. The "**OPTIONS**" for each AI model is available [in the options documentation page.](/en/how-to_guides/OPTIONS/).

!!! warning
    However, in the case of the Auto-ML model, the model is made according to the option value used in **OPTIONS** by changing only the dataset, not using the parameter value that the existing AI model had.

## **3. FIT MODEL Syntax example**

The example below uses the syntax "**FIT MODEL**" to create a <mark style="background-color:#E9D7FD ">fit_test_classifier</mark> model that you learned using a newly added dataset to the <mark style="background-color:#E9D7FD ">test_classifier</mark> model that you previously created.

```sql
%%thanosql
FIT MODEL fit_test_classifier
USING test_classifier
OPTIONS (
    target = 'survived',
    impute_type='simple',
    features_to_drop=["name", 'ticket', 'passengerid', 'cabin'],
    overwrite = True
    )
AS
SELECT *
FROM titanic_train
```
