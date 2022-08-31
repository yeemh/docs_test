---
title: Delete the model
---

# **Delete the model (DELETE MODEL)**

## Preface

- Updated Date : {{ git_revision_date_localized }}

## **1. DELETE MODEL Syntax Overview**

The "**DELETE MODEL**" syntax allows the user to delete models created in the database.

## **2. DELETE MODEL Syntax**

```sql
%%thanosql
DELETE MODEL [Model_name_to_delete]
```

## **3. DELETE MODEL Syntax example**

The example below uses the syntax "**DELETE MODEL**" to delete the <mark style="background-color:#E9D7FD ">titanic_classification</mark> classification model from the database that you created in [BUILD MODEL](/en/how-to_guides/ThanoSQL_ml/BUILD_MODEL_SYNTAX/).

```sql
%%thanosql
DELETE MODEL titanic_classification
```
