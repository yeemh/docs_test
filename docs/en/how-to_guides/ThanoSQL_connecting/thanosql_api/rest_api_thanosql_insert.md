---
title: How to Upload to ThanoSQL DB
---

# **How to Upload to ThanoSQL DB**

## Preface

- Updated Date : {{ git_revision_date_localized }}

You can use the REST API to remotely send images to your ThanoSQL storage and place them in any DB table.

=== "Python"

    ``` python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    base_url="https://engine.thanosql.ai/api/v1/insert/"
    table_name = "Table Name"
    column_name = "Column Name"

    api_url = f"{base_url}?table_name={table_name}&column_name={column_name}"
    header = {
        "Authorization" : f"Bearer {api_token}"
    }
    files = {'file' : open('Image File Path', 'rb')}

    r = requests.post(api_url, files=files, headers=header)

    r.raise_for_status()
    return_json = r.json()
    ```

=== "cURL"

    ``` shell
    curl -X 'POST' \
      'https://engine.thanosql.ai/api/v1/insert/?table_name=Table name&column_name=Table Name' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer Issued_API_TOKEN' \
      -H 'Content-Type: multipart/form-data' \
      -F 'file=@Image File Path;type=image/Image File Type'
    ```
