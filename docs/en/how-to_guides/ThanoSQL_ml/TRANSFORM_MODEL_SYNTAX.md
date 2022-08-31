---
title: Preprocessing data for model application
---

# **Preprocessing data for model application (TRANSFORM USING)**

## Preface

- Updated Date : {{ git_revision_date_localized }}

## **1. TRANSFORM USING Syntax overview**

The "**TRANSFORM USING**" syntax allows the user to apply the same preprocessing method used to create an AI model to the test dataset.

## **2. TRANSFORM USING Syntax**

```sql
%%thanosql
TRANSFORM USING [Existing_trained_model_name]
AS
[test data set to use]
```

## **3. TRANSFORM USING Syntax eample**

The example below uses the syntax "**TRANSFORM USING**" to apply the same processing to the <mark style="background-color:#FFEC92 ">titanic_test</mark> dataset as the <mark style="background-color:#E9D7FD ">test_classifier</mark> classification model that you created in [Learning Models](/en/how-to_guides/ThanoSQL_ml/BUILD_MODEL_SYNTAX/).

```sql
%%thanosql
TRANSFORM USING test_classifier
AS
SELECT *
FROM titanic_test
```
