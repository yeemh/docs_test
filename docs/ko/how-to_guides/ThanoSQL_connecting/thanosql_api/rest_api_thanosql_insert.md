---
title: ThanoSQL DB 업로드하기
---

# __ThanoSQL DB 업로드하기__

## 시작 전 사전 정보

- 마지막 수정날짜 : {{ git_revision_date_localized }}

REST API를 사용하여 자신의 ThanoSQL 저장 공간에 원격으로 이미지를 보내고 원하는 DB 테이블에 이미지를 넣을수 있습니다. 

=== "Python"

    ``` python
    import requests
    import json

    api_token = "발급받은_API_TOKEN"
    base_url="http://engine.thanosql.ai/api/v1/insert"
    table_name = "테이블 명"
    column_name = "컬럼 명"

    api_url = f"{base_url}?table_name={table_name}&column_name={column_name}"
    header = {
        "Authorization" : f"Bearer {api_token}"
    }
    files = {'file' : open('이미지 파일 경로', 'rb')}

    r = requests.post(api_url, files=files, headers=header)

    r.raise_for_status()
    return_json = r.json()
    ```

=== "cURL"

    ``` shell 
    curl -X 'POST' \
      'https://engine.thanosql.ai/api/v1/insert/?table_name=테이블 명&column_name=컬럼 명' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer 발급받은_API_TOKEN' \
      -H 'Content-Type: multipart/form-data' \
      -F 'file=@이미지 파일 경로;type=image/이미지 파일 타입'
    ```
