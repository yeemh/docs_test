---
title: How to Query using ThanoSQL
---

# **How to Query Using ThanoSQL**

You can run the same queries using ThanoSQL's REST API as you would on the ThanoSQL workspace.

=== "Python"

    ```python
    import requests
    import json

    api_token = "Issued_API_TOKEN"
    api_url="https://engine.thanosql.ai/api/v1/query/"
    query="Query to request"

    header = {
        "Authorization": f"Bearer {api_token}"
    }

    data = {
        'query_string': query
    }

    r = requests.post(api_url, data=json.dumps(data), headers=header)

    r.raise_for_status()
    return_json = r.json()

    ```

=== "cURL"

    ```shell
    curl -X 'POST' \
      'https://engine.thanosql.ai/api/v1/query/' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer Issued_API_TOKEN' \
      -H 'Content-Type: application/json' \
      -d '{"query_string": query}'
    ```
