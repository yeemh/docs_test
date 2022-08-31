---
title: Evaluate the model
---

# **Evaluate the model (EVALUATE USING)**

## Preface

- Updated Date : {{ git_revision_date_localized }}

## **1. EVALUATE USING Syntax Overview**

The "**EVALUATE USING**" syntax allows users to evaluate performance on AI models.

## **2. EVALUATE USING Syntax**

```sql
%%thanosql
EVALUATE USING [Existing_trained_model_name]
OPTIONS ([Option_value_required_for_evaluation_by_model])
AS
[data_set_to_use]
```

!!! warning
    If the data set you want to use does not have a target, you cannot evaluate the performance of the model.

## **3. EVALUATE USING Syntax example**

The example below uses the syntax "**EVALUATE USING**" to evaluate the <mark style="background-color:#E9D7FD ">test_classifier</mark> classification model that you created in [BUILD MODEL](/en/how-to_guides/ThanoSQL_ml/BUILD_MODEL_SYNTAX/).

```sql
%%thanosql
EVALUATE USING test_classifier
OPTIONS (
    target='survived'
    )
AS
SELECT *
FROM titanic_train
```

[![IMAGE](/img/thanosql_ml/classification/automl/img2.png)](/img/thanosql_ml/classification/automl/img2.png)

!!! note "**Query Details**"  
    Evaluate a model called <mark style="background-color:#E9D7FD ">titanic_classification</mark> built using the "**EVALUATE USING**" query syntax.
    For "target" in "**OPTIONS**", write down the name of the column(<mark style="background-color:#D7D0FF">survived</mark>) being the target value in the classification prediction model.
