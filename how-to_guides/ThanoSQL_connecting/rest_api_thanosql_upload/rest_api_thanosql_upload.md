---
title: How to Upload to the ThanoSQL Workspace Database
---

# **How to Upload to the ThanoSQL Workspace Database**

You can use ThanoSQL's REST API to remotely send and upload files to your ThanoSQL storage and insert them into any of your table within the database.

## Upload Only 

If "table_name" and "column_name" are not specified, the given file would only be sent to the ThanoSQL storage. 

=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    api_url="https://engine.thanosql.ai/api/v1/upload/"

    header = {
        "Authorization": f"Bearer {api_token}"
    }
    files = {'file': open('Data File Path', 'rb')}

    r = requests.post(api_url, files=files, headers=header)

    r.raise_for_status()
    return_json = r.json()
    ```

=== "cURL"

    ```shell
    curl -X 'POST' \
      'https://engine.thanosql.ai/api/v1/upload/' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer Issued_API_TOKEN' \
      -H 'Content-Type: multipart/form-data' \
      -F 'file=@Data File Path;type=file_type/Data File Type'
    ```

## Upload & Insert 

If "table_name" and "column_name" are specified, the given file would be sent to the ThanoSQL storage, and the file path would be inserted into a column of the specified table.

=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    base_url="https://engine.thanosql.ai/api/v1/upload/"
    table_name = "Table Name"
    column_name = "Column Name"

    api_url = f"{base_url}?table_name={table_name}&column_name={column_name}"
    header = {
        "Authorization": f"Bearer {api_token}"
    }
    files = {'file': open('Data File Path', 'rb')}

    r = requests.post(api_url, files=files, headers=header)

    r.raise_for_status()
    return_json = r.json()
    ```

=== "cURL"

    ```shell
    curl -X 'POST' \
      'https://engine.thanosql.ai/api/v1/upload/?table_name=Table name&column_name=Table Name' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer Issued_API_TOKEN' \
      -H 'Content-Type: multipart/form-data' \
      -F 'file=@Data File Path;type=file_type/Data File Type'
    ```

!!! faq "FAQ"
    - In order to look up the path within the Jupiter workspace, you must put /home/jovyan in front.