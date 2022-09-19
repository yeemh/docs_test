---
title: How to Use ThanoSQL Search
---

# **How to Use ThanoSQL Search**

## Preface

- Updated Date : {{ git_revision_date_localized }}

Using ThanoSQL's REST API, you can query and search for similar images within the ThanoSQL DB using images, text, and built models.

## **To search for an image using images**

=== "Python"

    ``` python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    base_url="https://engine.thanosql.ai/api/v1/search/file/"
    table_name = "Table Name"
    model_name = "Model Name"
    column_name = "Column Name"

    api_url = f"{base_url}?table_name={table_name}&model_name={model_name}&column_name={column_name}"

    header = {
        "Authorization" : f"Bearer {api_token}"
    }

    files = {'file' : open('Image File Path', 'rb')}

    ## SEARCH WITH IMAGE
    with requests.post(api_url, headers = header, files=files, stream=True) as r:
        r.raise_for_status()
        with open("Path to the zip file to save", 'wb') as f:
        for chunk in r.iter_content(chunk_size=8192):
            f.write(chunk)
    ```

=== "cURL"

    ``` shell
    curl -X 'POST' \
      'https://engine.thanosql.ai/api/v1/search/file/?table_name=Table Name&model_name=Model Name&column_name=Column Name' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer Issued_API_TOKEN' \
      -H 'Content-Type: multipart/form-data' \
      -F 'file=@Image File Path;type=image/Image File Type'
    ```

## **To search for an image using text**

=== "Python"

    ``` python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    base_url="https://engine.thanosql.ai/api/v1/search/text/"
    table_name = "Table Name"
    model_name = "Model Name"
    column_name = "Column Name"
    text = 'Text to search'


    ## WHEN SEARCHING WITH IMAGE
    api_url = f"{base_url}?table_name={table_name}&model_name={model_name}&column_name={column_name}&text={text}"

    header = {
        "Authorization" : f"Bearer {api_token}"
    }

    ## SEARCH WITH IMAGE
    with requests.post(api_url, headers = header, stream=True) as r:
        r.raise_for_status()
        with open("Path to the zip file to save", 'wb') as f:
        for chunk in r.iter_content(chunk_size=8192):
            f.write(chunk)
    ```

=== "cURL"

    ``` shell
    curl -X 'POST' \
      'https://engine.thanosql.ai/api/v1/search/text/?table_name=Table Name&model_name=Model Name&column_name=Column Name&text=Text to search' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer Issued_API_TOKEN' \
      -d ''
    ```
