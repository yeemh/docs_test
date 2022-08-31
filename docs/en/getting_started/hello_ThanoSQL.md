---
title: How to use the ThanoSQL workspace
---

# **How to use the ThanoSQL Workspace**

ThanoSQL's workspace is a web-based computing environment based on [Jupyter Lab](https://github.com/jupyterlab/jupyterlab).

To use **ThanoSQL** in this environment, you must first load the **thanosql** cell magic.

!!! tip ""
    You can press the run button at the top, or you can run it with **Ctrl + Enter** or **Shift + Enter** shortcuts.

## **1. Call up ThanoSQL cell magic**

```sql
%load_ext thanosql
```

## **2. Set API_TOKEN**

Next, for each user's workspace API_TOKEN setting, press and paste the **GET API_TOKEN** button at the top of the browser.

```sql
%thanosql API_TOKEN=<Issued_API_TOKEN>
```

ex)

```sql
%thanosql API_TOKEN=eyGVFDdfafddvczs
```

## **3. Check the list of ThanoSQL models/tutorials using the LIST query syntax**

All preparations are complete for using **ThanoSQL**.

To view a list of prebuilt ThanoSQL models, run the TanoSQL statement below.

```sql
%%thanosql
LIST THANOSQL MODEL
```

[![IMAGE](/img/getting_started/img6.png)](/img/getting_started/img6.png)

If you run the ThanoSQL statement below, you can view the tutorial list in the [ThanoSQL Technical Documentation](https://docs.thanosql.ai/en/).

```sql
%%thanosql
LIST THANOSQL TUTORIAL
```

[![IMAGE](/img/getting_started/img9.png)](/img/getting_started/img9.png)

If you run the ThanoSQL statement below, you can see the list of data tables used in the tutorial in the [ThanoSQL Technical Documentation](https://docs.thanosql.ai/en/).

```sql
%%thanosql
LIST THANOSQL DATASET
```

[![IMAGE](/img/getting_started/img10.png)](/img/getting_started/img10.png)
