---
title: LIST
---

# __LIST__

## __1. LIST Statement__

The "__LIST__" statement allows users to view the lastest pre-built models("THANOSQL MODEL"), provided datasets("THANOSQL DATASET"), data tables("TABLE") and the models("MODEL") created by the users.

## __2. LIST Syntax__

The "__LIST MODEL__" statement outputs the models you have created.

```sql
LIST MODEL
```

[![IMAGE](/img/thanosql_syntax/query/LIST/img1.png)](/img/thanosql_syntax/query/LIST/img1.png)

The "__LIST THANOSQL MODEL__" statement outputs the latest pre-built models.

```sql
LIST THANOSQL MODEL
```

[![IMAGE](/img/thanosql_syntax/query/LIST/img2.png)](/img/thanosql_syntax/query/LIST/img2.png)


The "__LIST THANOSQL DATASET__" statement outputs the latest datasets used by the tutorials.

```sql
LIST THANOSQL DATASET
```

[![IMAGE](/img/thanosql_syntax/query/LIST/img3.png)](/img/thanosql_syntax/query/LIST/img3.png)

The "__LIST TABLE__" statement outputs the data tables you have created.

```sql
LIST TABLE
```

[![IMAGE](/img/thanosql_syntax/query/LIST/img4.png)](/img/thanosql_syntax/query/LIST/img4.png)

!!! note "LIST as a Subquery" 
    - "**LIST**" clause can be used as "**SELECT**" clause's subquery. 
    - By using "**LIST**" clause as a subquery like "**SELECT * FROM (LIST THANOSQL MODEL)**", you can use SQL clauses like "**LIMIT**" and "**WHERE**" with "**LIST**" clause as well. 
