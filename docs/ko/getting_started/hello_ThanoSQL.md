---
title: ThanoSQL 워크스페이스 사용법
---

# __ThanoSQL 워크스페이스 사용법__ 

ThanoSQL의 워크스페이스는 [Jupyter Lab](https://github.com/jupyterlab/jupyterlab)을 기반으로 하는 웹 기반 컴퓨팅 환경입니다.

이 환경에서 **ThanoSQL**을 본격적으로 사용하기 위해서는 먼저 **thanosql** cell magic을 불러와야 합니다.

!!! tip ""
    상단의 실행 버튼을 누르거나, **Ctrl + Enter** 혹은 **Shift + Enter** 단축키로도 실행할 수 있습니다.

## __1. ThanoSQL cell magic 불러오기__

```sql
%load_ext thanosql
```
## __2. API_TOKEN 설정하기__

다음으로, 각 사용자의 워크스페이스 API_TOKEN 설정을 위해 브라우저 상단의 **GET API_TOKEN** 버튼을 누른 후 붙여넣기하여 설정해줍니다. 

!!! danger  
    API 토큰은 새롭게 발급 받을수 있지만 새롭게 발급 받으면 이전에 발급 받은 토큰은 더 이상 사용 할수 없는 점 유의하시기 바랍니다. 

!!! tip "생성된 API 토큰을 이용하여 ThanoSQL의 모든 REST API를 사용하실수 있습니다"
    ThanoSQL에서 REST API 사용에 대한 자세한 내용은 [참조 페이지의 __ThanoSQL REST API Reference__](/how-to_guides/reference/#thanosql-rest-api-reference)에서 확인하세요.

```sql
%thanosql API_TOKEN=<발급받은_API_TOKEN>
```

ex)

```sql
%thanosql API_TOKEN=eyGVFDdfafddvczs
```
[![IMAGE](/img/thanosql_api/restapi_token_img2.jpg)](/img/thanosql_api/restapi_token_img2.jpg) 

!!! notice "ThanoSQL의 쿼리문 작성 방법"
    ThanoSQL에서 쿼리문을 작성하는 방법에는 one-line 작성 방법과 multi-line 작성 방법이 있습니다.  

    - one-line 문법의 경우, 쿼리를 테이블 형식으로 반환하며 변수에 테이블을 할당할 때, 주로 사용됩니다. 또한 워크스페이스 사용법 1번과 2번처럼 ThanoSQL cell magic과 Token을 할당할 때 사용합니다.  

    - multi-line 문법의 경우, 다른 DBMS를 사용할 때와 같은 사용자 경험을 제공하며 테이블을 조회하거나 ThanoSQL 확장 문법을 실행할 때 사용합니다.



## __3. LIST 쿼리 구문으로 ThanoSQL 모델/튜토리얼 목록 확인하기__

**ThanoSQL**을 사용할 모든 준비가 끝났습니다.

아래 ThanoSQL문을 실행시키면 Pre-built된 ThanoSQL 모델 목록을 확인할 수 있습니다.

```sql
%%thanosql
LIST THANOSQL MODEL
```

[![IMAGE](/img/getting_started/img8.png)](/img/getting_started/img8.png)

아래 ThanoSQL문을 실행시키면 [ThanoSQL 기술 문서에 있는 튜토리얼](/tutorials/algorithm_list/)에 있는 튜토리얼 목록을 확인할 수 있습니다.

```sql
%%thanosql
LIST THANOSQL TUTORIAL
```

[![IMAGE](/img/getting_started/img9.png)](/img/getting_started/img9.png)


아래 ThanoSQL문을 실행시키면 [ThanoSQL 기술 문서에 있는 튜토리얼](/tutorials/algorithm_list/)에서 사용된 데이터 테이블 리스트를 확인할 수 있습니다.

```sql
%%thanosql
LIST THANOSQL DATASET
```

[![IMAGE](/img/getting_started/img10.png)](/img/getting_started/img10.png)


## __4. 튜토리얼 가져오기__

아래 문을 실행시키면 ThanoSQL의 전체 튜토리얼들을 자신의 워크스페이스에 가지고 올 수 있습니다. 

```sql
!git clone https://github.com/smartmind-team/thanosql-tutorial.git
```

특정 튜토리얼만 자신의 워크스페이스로 가지고 오고 싶다면 아래와 같이 wget 메서드를 사용합니다.

!!!note "___튜토리얼 Github URL 리스트___"

```python
!wget [가져올 tutorial의 Github URL]
# 예시 
## wget https://raw.githubusercontent.com/smartmind-team/thanosql-tutorial/main/tutorial/thanosql_search/search_image_by_keyword.ipynb
```

| 튜토리얼      | URL                          |
| :---------: | :----------------------------------: |
| `키워드로 이미지 검색하기`       | https://raw.githubusercontent.com/smartmind-team/thanosql-tutorial/main/tutorial/thanosql_search/search_image_by_keyword.ipynb |
| `이미지로 이미지 검색하기`       | https://raw.githubusercontent.com/smartmind-team/thanosql-tutorial/main/tutorial/thanosql_search/search_image_by_image.ipynb  |
| `텍스트로 이미지 검색하기`    | https://raw.githubusercontent.com/smartmind-team/thanosql-tutorial/main/tutorial/thanosql_search/search_image_by_text.ipynb |
| `Auto-ML을 사용하여 분류 모델 만들기`    | https://raw.githubusercontent.com/smartmind-team/thanosql-tutorial/main/tutorial/thanosql_ml/classification/automl_classification.ipynb |
| `이미지 분류 모델 만들기`    | https://raw.githubusercontent.com/smartmind-team/thanosql-tutorial/main/tutorial/thanosql_ml/classification/image_classification.ipynb |
| `텍스트 분류 모델 만들기`    | https://raw.githubusercontent.com/smartmind-team/thanosql-tutorial/main/tutorial/thanosql_ml/classification/text_classification.ipynb |
| `Auto-ML을 사용하여 예측 모델 만들기`    | https://raw.githubusercontent.com/smartmind-team/thanosql-tutorial/main/tutorial/thanosql_ml/regression/automl_regression.ipynb |
| `오디오 파일을 받아쓰는 음성 인식 모델 만들기`    | https://raw.githubusercontent.com/smartmind-team/thanosql-tutorial/main/tutorial/thanosql_ml/audio_recognition/speech_recognition.ipynb |
|`사용자의 모델을 ThanoSQL에서 사용하기`| https://raw.githubusercontent.com/smartmind-team/thanosql-tutorial/main/tutorial/thanosql_ml/udm_tutorial.ipynb |

