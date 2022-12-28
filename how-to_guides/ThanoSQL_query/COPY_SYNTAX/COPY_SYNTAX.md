---
title: COPY
---

# __COPY__

## __1. COPY Statement__

The "__COPY__" statement allows users to create data tables in the ThanoSQL workspace database with their data files, data folders, and Pandas DataFrame within their workspace.

!!! note "__Supported file types__"
    - csv
    - pkl, pickle
    - xls, xlsx, xlsm, xlsb

## __2. COPY Syntax__

```sql
COPY (table_name_expression)
OPTIONS (overwrite=True)
FROM (file_path | dir_path)
```

!!! note "__Query Details__"
    - The "__OPTIONS__" clause allows you to change the value of a parameter. The definition of each parameter is as follows.
        - "overwrite": determines whether to overwrite a table if it already exists. If set as True, the old table is replaced with the new table (bool, optional, True|False, default: False)

## __3. COPY Example__

### __3-1. Using the Path of the Data File__

The example below demonstrates how to use a data file for the COPY clause. A specified file with a path as an input would be read by the ThanoSQL Engine and recreated as a table within a database. 

```sql
%%thanosql
COPY mytable
OPTIONS (overwrite=True)
FROM 'data/example.csv'
```

### __3-2. Using the Path of the Data Folder__

The example below demonstrates how to use a data directory for the COPY clause. A specified folder with a path as an input would be read by the ThanoSQL Engine and recreated as a table within a database. 

!!! note "__Using COPY with Data Folders__"
    - If the path to the folder containing the images, audios, or videos is given as an input, the “__COPY__” clause will translate each file as a row and recreate it as a data table.

```sql
%%thanosql
COPY mytable
OPTIONS (overwrite=True)
FROM 'diet_image_data/'
```

### __3-3. Using a Pandas DataFrame__
The example below demonstrates how to use a Pandas DataFrame for the COPY clause. A specified dataframe as an input would be read by the ThanoSQL Engine and recreated as a table within a database. 

#### Prepare a Pandas DataFrame 
```python
# create a Pandas DataFrame
df = pd.read_csv("./diet_image_data/sample.csv")
# df must to be converted to JSON and 'orient' must be specified as 'records' 
df_in_json = df.to_json(orient="records")

# use a f-string to wrap 'df_in_json' within the COPY clause 
copy_pandas_df = f'''
COPY mytable
OPTIONS (overwrite=True)
FROM '{df_in_json}'
'''
```

#### COPY a Pandas DataFrame 

```sql
%thanosql $copy_pandas_df
```

!!! warning "__Warning__"
    - A Pandas DataFrame must be converted to JSON before being wrapped in the __COPY__ clause. 
    - __${variable_name}__ should be followed by the __%thanosql__. 
