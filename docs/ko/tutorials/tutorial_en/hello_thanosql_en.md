---
jupyter:
  jupytext:
    text_representation:
      extension: .md
      format_name: markdown
      format_version: '1.3'
      jupytext_version: 1.14.1
  kernelspec:
    display_name: Python 3.8.9 64-bit
    language: python
    name: python3
---

<!-- #region tags=[] -->
# __Hello ThanoSQL!__

ThanoSQL is an integrated platform that enables Query and AI modeling with SQL alone for both structured and unstructured data. Relational DataBase (RDB), AI, and Big Data Platform capabilities can be applied on a single ThanoSQL platform, dramatically reducing the inefficiency of AI-based digital transformation.

- ThanoSQL can query both structured and unstructured data only with SQL, enabling fast AI modeling.
- Replacing a Lambda architecture-based unstructured big data platform with one ThanoSQL enables development, deployment, and operation in one process.
- Based on big data processing and distributed parallel processing technology, data processing is more than twice as fast as before.

## __Using ThanoSQL Workspace__
ThanoSQL's workspace is a web-based computing environment based on [Jupyter Lab](https://github.com/jupyterlab/jupyterlab).

In order to use `ThanoSQL` in earnest in this environment, the `thanosql` cell magic must be loaded first.

(You can press the run button at the top, or run it with the `Ctrl + Enter` or `Shift + Enter` shortcut.)
<!-- #endregion -->

```python
%load_ext thanosql
```

Next, for each user's workspace API_TOKEN setting, press and paste the `Get API_TOKEN` button at the top of the browser.

```
%thanosql API_TOKEN=<Issued_API_TOKEN>

Example)
%thanosql API_TOKEN=eyJhbGciOiJIUzI1NiIsInR..
```

```python
%thanosql API_TOKEN=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ3b3Jrc3BhY2VfaWQiOjUsImV4cCI6MTY4NjQwODY3Mn0.ywc_hbXv1zh3gcTLgviKOvPJg1etTljMzoMYb8ZF58c
```

All preparations for using `ThanoSQL` are complete.

To view a list of prebuilt ThanoSQL models, run the TanoSQL statement below.

```python
%%thanosql
LIST THANOSQL MODEL
```

For more ThanoSQL usage and tutorial, visit the ThanoSQL Technical Documentation at [ThanoSQL Techdoc](https://docs.thanosql.ai)



