---
title: ThanoSQL 워크스페이스 데이터베이스 업로드
---

# __ThanoSQL 워크스페이스 데이터베이스 업로드__

REST API를 사용하여 자신의 ThanoSQL 저장 공간에 원격으로 파일을 보내고 원하는 데이터베이스 테이블에 파일을 넣을수 있습니다.

## 업로드

"table_name"과 "column_name"이 지정되지 않은 경우 지정된 파일은 ThanoSQL 저장소로만 전송됩니다.

=== "Python"

    ```python
    import requests
    import json
    api_token = "발급받은_API_TOKEN"
    api_url="https://engine.thanosql.ai/api/v1/upload/"
    header = {
        "Authorization": f"Bearer {api_token}"
    }
    files = {'file': open('데이터 파일 경로', 'rb')}
    r = requests.post(api_url, files=files, headers=header)
    r.raise_for_status()
    return_json = r.json()
    ```

=== "cURL"

    ```shell
    curl -X 'POST' \
      'https://engine.thanosql.ai/api/v1/upload/' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer 발급받은_API_TOKEN' \
      -H 'Content-Type: multipart/form-data' \
      -F 'file=@데이터 파일 경로;type=파일_타입/데이터 파일 타입'
    ```

## 업로드 & 인서트

"table_name"과 "column_name"이 지정되면 주어진 파일이 ThanoSQL 저장소로 전송되고 파일 경로가 지정된 테이블의 열에 삽입됩니다.

=== "Python"

    ```python
    import requests
    import json

    api_token = "발급받은_API_TOKEN"
    base_url="https://engine.thanosql.ai/api/v1/upload/"
    table_name = "테이블 명"
    column_name = "컬럼 명"

    api_url = f"{base_url}?table_name={table_name}&column_name={column_name}"
    header = {
        "Authorization": f"Bearer {api_token}"
    }
    files = {'file': open('데이터 파일 경로', 'rb')}

    r = requests.post(api_url, files=files, headers=header)

    r.raise_for_status()
    return_json = r.json()
    ```

=== "cURL"

    ```shell 
    curl -X 'POST' \
      'https://engine.thanosql.ai/api/v1/upload/?table_name=테이블 명&column_name=컬럼 명' \
      -H 'accept: application/json' \
      -H 'Authorization: Bearer 발급받은_API_TOKEN' \
      -H 'Content-Type: multipart/form-data' \
      -F 'file=@데이터 파일 경로;type=파일_타입/데이터 파일 타입'
    ```

!!! faq "FAQ"
    - Jupyter 내부의 path를 조회하기 위해서는 앞에 /home/jovyan 을 붙여야 합니다.