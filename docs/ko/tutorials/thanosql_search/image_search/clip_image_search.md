---
title: 텍스트로 이미지 검색하기
---

# __텍스트로 이미지 검색하기__

## 시작 전 사전 정보

- 튜토리얼 난이도 : ★★☆☆☆
- 읽는데 걸리는 시간 : 7분
- 사용 언어 : [SQL](https://ko.wikipedia.org/wiki/SQL) (100%)
- 실행 파일 위치 : tutorial/query/텍스트로 이미지 검색하기.ipynb  
- 참고 문서 : [Unsplash Dataset - Lite](https://unsplash.com/data), [Learning Transferable Visual Models From Natural Language Supervision](https://arxiv.org/abs/2103.00020)
- 마지막 수정날짜 : {{ git_revision_date_localized }}

## 튜토리얼 소개

!!! note "텍스트 수치화 기술 이해하기"
    자연어를 컴퓨터가 이해하려면 자연어를 수치화 해야 합니다. 최근 [BERT](https://en.wikipedia.org/wiki/BERT_(language_model))나 [GPT-3](https://en.wikipedia.org/wiki/GPT-3)와 같은 사전학습 모델에 대한 연구가 활발히 이루어지고 있으며, 주목할 만한 성과를 보여주고 있습니다. 이러한 모델들은 [자가 학습(Self-Supervised Learning)](https://en.wikipedia.org/wiki/Self-supervised_learning)을 기반으로 각 문장들의 의미를 파악하고 유사한 의미를 갖는 각 문장들을 가깝게 위치하도록 저차원 공간에 수치화하여 표현합니다. 문장 간의 순서를 무작위로 섞거나 일부 단어를 마스킹하는 방식 등을 이용해 각 문장/문맥의 참/거짓 여부를 판단함으로써 라벨링 작업이 없어도 학습이 가능하도록 지원합니다.

텍스트와 이미지 같이 다른 형태의 입력 자료를 함께 다루는 문제를 멀티 모달(Multi-modal)이라고 합니다. **"CLIP: Connecting Text and Image"**은 대표적인 멀티 모달 모델로 수치화 된 저차원 공간에 대한 이해를 다루고 있습니다. 기존 모델이 이미지 자체의 [특징(Feature)](https://ko.wikipedia.org/wiki/%ED%8A%B9%EC%A7%95_(%EA%B8%B0%EA%B3%84_%ED%95%99%EC%8A%B5))만을 학습 했다면, 멀티 모달 모델에서는 이미지와 텍스트를 모두 입력 자료로 사용하면서 해당 이미지를 설명하는 텍스트에 대한 특징까지 동시에 학습할 수 있습니다. 또한, 텍스트와 이미지가 저차원 공간에 함께 위치함으로써 텍스트와 이미지 사이의 유사도를 판단할 수 있게 되며, 이를 응용하면 검색 알고리즘으로 사용할 수 있습니다.

ThanoSQL은 인공지능 알고리즘을 이용해서 데이터 세트를 수치화 합니다. 이렇게 수치화 된 데이터는 DB의 컬럼 내에 저장되고, 입력받은 텍스트의 수치화 결과와 유사도 계산을 통해 비슷한 이미지를 검색하는데 사용됩니다.

__아래는 ThanoSQL 텍스트-이미지 검색 알고리즘의 활용 및 예시 입니다.__

- 사용자가 보유하고 있는 이미지나 동영상에서 원하는 장면을 텍스트로 묘사하고 이와 가장 유사한 이미지를 검색합니다. 사용자가 검색하는 상품에 대한 키워드가 아닌 텍스트 기반의 설명을 듣고 가장 유사한 상품 이미지를 노출합니다.
- 유튜브 영상 등에서 내가 원하는 광고를 넣고 싶은 시간을 검색합니다. 여행 광고를 넣기 위해서 산이나 캠핑 장면 등이 나오는 장면을 손쉽게 검색하고 광고를 삽입합니다. 
<br>

!!! note "본 튜토리얼에서는"
    :point_right: Unsplash는 20만 명 이상의 사진가들이 참여한 이미지들을 AI를 위한 데이터 세트로 무료로 공개했습니다. `Unsplash Dataset - Lite`는 25,000 장의 자연을 테마로한 이미지로 구성되어 있으며, 25,000 개의 키워드를 함께 제공합니다. 
    
이번 튜토리얼에서는 텍스트-이미지 검색 모델을 사용하여, ThanoSQL DB의 `Unsplash Dataset - Lite` 데이터 세트의 25,000 장의 이미지 중에서 텍스트로 원하는 이미지를 검색해 봅니다. 

## __0. 데이터 세트 준비__
ThanoSQL의 쿼리 구문을 사용하기 위해서는 [ThanoSQL 워크스페이스](/getting_started/how_to_use_ThanoSQL/#5-thanosql)
에서 언급된 것처럼 API 토큰을 생성하고 아래의 쿼리를 실행해야 합니다.
```sql
%load_ext thanosql
```
```sql
%thanosql API_TOKEN=<발급받은_API_TOKEN>
```
```sql
%%thanosql
COPY unsplash_data 
OPTIONS(overwrite=True)
FROM "tutorial_data/unsplash_data/unsplash.csv"
```

!!! note "__쿼리 세부 정보__"
    - "__COPY__" 쿼리 구문을 사용하여 DB에 저장 할 데이터 세트명을 지정합니다. 
    - "__OPTIONS__" 쿼리 구문을 통해 __COPY__ 에 사용할 옵션을 지정합니다.
        - "overwrite" : 동일 이름의 데이터 세트가 DB상에 존재하는 경우 덮어쓰기 가능 유무 설정. True일 경우 기존 데이터 세트는 새로운 데이터 세트로 변경됨 (True|False, DEFAULT : False) 

## __1. 데이터 세트 확인__

텍스트-이미지 검색 모델을 만들기 위해 우리는 ThanoSQL DB에 저장되어 있는 `unsplash_data` 테이블을 사용합니다. 아래의 쿼리문을 실행하고 테이블의 내용을 확인합니다.

<br>

```sql
%%thanosql
SELECT photo_id, image_path, photo_image_url, photo_description, ai_description
FROM unsplash_data
LIMIT 5
```
[![IMAGE](/img/thanosql_search/clip_search/select_data.png)](/img/thanosql_search/clip_search/select_data.png)

!!! note "데이터 이해하기"
    - `photo_id` 이미지의 고유 id 컬럼 명
    - `image_path` 이미지가 위치한 경로의 컬럼 명
    - `photo_image_url` 웹사이트 unsplash에서의 원본 이미지 주소를 나타내는 컬럼 명
    - `photo_description` 해당 이미지에 대해 사람이 작성한 짧은 설명을 나타내는 컬럼 명
    - `ai_description` AI가 생성해낸 해당 이미지에 대한 설명을 나타내는 컬럼 명

```sql
%%thanosql
PRINT IMAGE 
AS
SELECT image_path 
FROM unsplash_data 
LIMIT 5
```
[![IMAGE](/img/thanosql_search/clip_search/print_dataset_image.png)](/img/thanosql_search/clip_search/print_dataset_image.png)

## __2. 텍스트 검색을 위한 이미지 수치화 모델 생성하기__

!!! danger "참고 사항"
    텍스트-이미지 검색 알고리즘은 학습에 오랜 시간이 걸리고 총 4억 개의 데이터 세트로 사전 학습된 모델을 사용하기 때문에 "__BUILD MODEL__" 쿼리 구문을 이용한 학습 과정을 본 튜토리얼에서는 생략합니다. `tutorial_search_clip` 모델은 베이스 알고리즘으로 `clipen`을 사용한 사전학습 된 모델을 가져와서 사용하게 됩니다. "__CONVERT USING__" 쿼리 구문을 실행하게 되면 "모델명(`tutorial_search_clip`)_베이스 알고리즘명(`clipen`)"으로 이미지가 수치화 된 컬럼이 자동으로 생성이 되며, "__SEARCH IMAGE__" 쿼리 구문을 실행하게 되면 "모델명(`tutorial_search_clip`)_베이스 알고리즘 명(`clipen`)_similarity수(1)"로 이미지 유사도 컬럼이 자동으로 생성 됩니다. 여기수 "수"는 검색에 사용한 텍스트의 갯수를 의미합니다. 2개 이상의 텍스트로 검색이 이루어 질 경우 순서에 따라 컬럼의 수가 순차적으로 증가되어 생성 됩니다. 자세한 사항은 아래 내용을 참고하세요.
(쿼리 실행 시 예상 소요 시간: 3 min)  

다음 "__CONVERT USING__" 쿼리 구문을 실행하여 `unsplash_data` 이미지들을 수치화 합니다. 수치화된 결과값은 새로 생긴 <mark style="background-color:#D7D0FF ">tutorial_search_clip_clipen</mark> 컬럼에 저장됩니다. (결과 컬럼명은 {model_name}_{base_model_name}으로 추가됩니다) 

```sql
%%thanosql
CONVERT USING tutorial_search_clip
OPTIONS (
    image_col="image_path", 
    table_name="unsplash_data", 
    batch_size=128
    )
AS 
SELECT * 
FROM unsplash_data
```

!!! note "쿼리 세부 정보"
    - "__CONVERT USING__" 쿼리 구문은 `tutorial_search_clip` 모델을 이미지 수치화를 위한 알고리즘으로 사용합니다.  
    - "__OPTIONS__" 쿼리 구문은 이미지 수치화 시 필요한 변수들을 정의합니다. 
        - "table_name" : ThanoSQL DB 내에 저장될 테이블 이름
        - "image_col" : 이미지 경로를 담고 있는 컬럼 명
        - "batch_size" : 한번의 학습에서 읽는 데이터 세트 묶음의 크기. 논문에 따르면 클 수록 학습 성능이 증가하지만 메모리의 크기를 고려하여 128을 사용합니다. (DEFAULT : 16)  
           
[![IMAGE](/img/thanosql_search/clip_search/select_data_with_embedding.png)](/img/thanosql_search/clip_search/select_data_with_embedding.png)

<br>

## __3. 텍스트로 이미지 검색하기__


"__SEARCH IMAGE__"  쿼리 구문과 생성한 `tutorial_search_clip` 모델을 사용하여 텍스트 기반 이미지 검색을
 할 수 있습니다. 다음 쿼리 구문을 실행하여 "a black cat" 이라는 텍스트와 임베딩 된 `unsplash_data` 
이미지들의 유사도를 계산합니다. 결괏값은 새로 추가된 <mark style="background-color:#D7D0FF ">tutorial_search_clip_clipen_similarity1</mark> 컬럼에 
저장됩니다.

```sql
%%thanosql
SEARCH IMAGE text="a black cat"
USING tutorial_search_clip
AS 
SELECT * 
FROM unsplash_data
```
[![IMAGE](/img/thanosql_search/clip_search/search_result_raw.png)](/img/thanosql_search/clip_search/search_result_raw.png)

!!! note "쿼리 세부 정보"
    - "__SEARCH IMAGE__" 쿼리 구문을 사용하여 이미지를 찾을 것임을 명시합니다. "text" 변수를 이용해서 찾고자 하는 이미지의 텍스트 내용을 입력합니다. 
    - "__USING__" 쿼리 구문을 통해 검색에 사용할 모델로 `tutorial_search_clip`을 사용할 것을 명시합니다.

아래 쿼리 구문을 실행하여 'a black cat' 텍스트와 가장 유사한 이미지 5개의 유사도를 확인합니다.

```sql
%%thanosql
SELECT image_path, tutorial_search_clip_clipen_similarity1 
FROM (
    SEARCH IMAGE text="a black cat"
    USING tutorial_search_clip
    AS 
    SELECT * 
    FROM unsplash_data
    )
ORDER BY tutorial_search_clip_clipen_similarity1 DESC 
LIMIT 5
```
[![IMAGE](/img/thanosql_search/clip_search/search_result_sorted.png)](/img/thanosql_search/clip_search/search_result_sorted.png)

!!! note "쿼리 세부 정보"    
    - "__SEARCH IMAGE__" 쿼리 구문은 입력한 텍스트와 이미지 사이의 유사도를 계산하여 반환합니다.
    - 첫 번째 "__SELECT__" 쿼리 구문은 괄호 안의 쿼리 결과에서 <mark style="background-color:#D7D0FF ">image_path</mark> 컬럼과 <mark style="background-color:#D7D0FF ">tutorial_search_clip_clipen_similarity1</mark> 컬럼을 선택합니다.<br>
    - "__ORDER BY__" 쿼리 구문은 결과를 <mark style="background-color:#D7D0FF ">tutorial_search_clip_clipen_similarity1</mark> 컬럼의 값을 기준으로 정렬하는데, 정렬은 내림차순("__DESC__")이며, 그 중 상위 5개("__LIMIT__" 5)의 결과를 출력합니다.


이전 쿼리 구문을 "__PRINT__"문과 함께 응용하여, 결과 이미지를 바로 확인할 수 있습니다.

```sql
%%thanosql
PRINT IMAGE 
AS (
    SELECT image_path, tutorial_search_clip_clipen_similarity1 
    FROM (
        SEARCH IMAGE text="a black cat"
        USING tutorial_search_clip
        AS 
        SELECT * 
        FROM unsplash_data
        )
    ORDER BY tutorial_search_clip_clipen_similarity1 DESC 
    LIMIT 5
    )
```

[![IMAGE](/img/thanosql_search/clip_search/result_black_cat.png)](/img/thanosql_search/clip_search/result_black_cat.png)


!!! note "쿼리 세부 정보"
    이 쿼리는 위의 쿼리와 합쳐 세 단계로 구성됩니다.

    - 첫 번째 괄호 안의 "__SELECT__" 쿼리 구문을 통해 바로 위 단계의 결과를 생성합니다.    
    - "__PRINT IMAGE__" 쿼리 구문을 사용하여 해당 이미지를 출력합니다.

```sql
%%thanosql
PRINT IMAGE 
AS (
    SELECT image_path, tutorial_search_clip_clipen_similarity1 
    FROM (
        SEARCH IMAGE text="a dog on a chair"
        USING tutorial_search_clip
        AS 
        SELECT * 
        FROM unsplash_data
        )
    ORDER BY tutorial_search_clip_clipen_similarity1 DESC 
    LIMIT 5
    )
```

[![IMAGE](/img/thanosql_search/clip_search/result_dog_on_chair.png)](/img/thanosql_search/clip_search/result_dog_on_chair.png)

```sql
%%thanosql
PRINT IMAGE 
AS (
    SELECT image_path, tutorial_search_clip_clipen_similarity1 
    FROM (
        SEARCH IMAGE text="gloomy photos"
        USING tutorial_search_clip
        AS 
        SELECT * 
        FROM unsplash_data
        )
    ORDER BY tutorial_search_clip_clipen_similarity1 DESC 
    LIMIT 5
    )
```

[![IMAGE](/img/thanosql_search/clip_search/result_gloomy.png)](/img/thanosql_search/clip_search/result_gloomy.png)


```sql
%%thanosql
PRINT IMAGE 
AS (
    SELECT image_path, tutorial_search_clip_clipen_similarity1 
    FROM (
        SEARCH IMAGE text="the feeling when your program finally works"
        USING tutorial_search_clip
        AS 
        SELECT * 
        FROM unsplash_data
        )
    ORDER BY tutorial_search_clip_clipen_similarity1 DESC 
    LIMIT 5
    )
```

[![IMAGE](/img/thanosql_search/clip_search/result_happy.png)](/img/thanosql_search/clip_search/result_happy.png)

## __4. 튜토리얼을 마치며__

이번 튜토리얼에서는 멀티 모달 텍스트/이미지 수치화 모델을 사용하여 `unsplash 데이터 세트`에서 텍스트를 통한 이미지 검색을 해보았습니다. 초급 단계의 튜토리얼인 만큼 간단한 쿼리를 통해 눈에 보이는 결과를 얻는 것 위주로 진행했습니다. 이미지 검색을 조금 더 다채로운 쿼리와 함께 사용한다면, 보다 원하는 결과에 가까운 값을 얻을 수 있을 것입니다.


!!! tip "__나만의 서비스를 위한 모델 배포 관련 문의__"
    ThanoSQL을 활용해 나만의 모델을 만들거나, 나의 서비스에 적용하는데 어려움이 있다면 언제든 아래로 문의주세요😊

    텍스트-이미지 검색 모델 구축 관련 문의: contact@smartmind.team
