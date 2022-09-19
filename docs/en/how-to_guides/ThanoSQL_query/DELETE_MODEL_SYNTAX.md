---
title: DELETE  MODEL
---

# __DELETE MODEL__

## __1. DELETE MODEL Statement__

The "__DELETE MODEL__" statement allows users to delete their models.

## __2. DELETE MODEL Syntax__

```sql
%%thanosql
DELETE MODEL [model_name_to_delete]
```

## __3. DELETE MODEL Example__

The example below demonstrates how to use the "__DELETE MODEL__" statement in order to delete the <mark style="background-color:#E9D7FD ">titanic_classification</mark> model built with the [BUILD MODEL](/en/how-to_guides/ThanoSQL_query/BUILD_MODEL_SYNTAX/) statement.

```sql
%%thanosql
DELETE MODEL titanic_classification
```

