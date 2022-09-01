---
title: 이미지로 이미지 검색하기
---

# __이미지로 이미지 검색하기__ 

## 시작 전 사전정보

- 튜토리얼 난이도 : ★☆☆☆☆
- 읽는데 걸리는 시간 : 7분
- 사용 언어 : [SQL](https://ko.wikipedia.org/wiki/SQL) (100%)
- 실행 파일 위치 : tutorial/query/이미지로 이미지 검색하기.ipynb   
- 참고 문서 : [MNIST 데이터 세트](http://yann.lecun.com/exdb/mnist/), [A Simple Framework for Contrastive Learning of Visual Representations](https://arxiv.org/abs/2002.05709)
- 마지막 수정날짜 : {{ git_revision_date_localized }}

## 튜토리얼 소개

!!! note "이미지 수치화 기술 이해하기"
    이미지는 고차원 데이터(높이x너비x채널[RGB]x색의 강도)로써 각 픽셀의 정보를 무작위로 생성한다면 아무 의미를 갖지 않는 이미지가 생성됩니다. 즉, 각 픽셀은 주변 픽셀과 연관된 특정 패턴을 갖는 경우에만 사람이 이미지로 인식을 할 수 있게 됩니다. 이는 이미지를 실제보다 저차원적 특성 벡터에 표현할 수 있다는 의미입니다. 최근에는 인공지능을 이용해서 각 이미지가 갖는 의미의 유사도에 따라 저차원 공간에 수치화하여 표현하는 연구들이 다방면으로 이루어지고 있으며 이는 이미지 수치화, 벡터화(Vectorization), 임베딩(Embedding) 등의 이름으로  다양하게 활용되고 있습니다. 

이미지의 유사도를 정의하는 방법은 여러가지가 있습니다. 색상이 비슷하거나, 이미지 내의 사물이 비슷하거나, 손글씨처럼 의미가 동일할 수도 있습니다. 유사한 이미지에 대한 정확한 정의를 내리기 어렵지만 이미지가 보유하고 있는 일반적인 특징을 인공지능은 학습하고 수치화합니다.

ThanoSQL에서는 이미지를 입력하고 DB에서 유사한 이미지를 검색하기 위해 [자가학습모델(Self-Supervised Learning Model)](https://en.wikipedia.org/wiki/Self-supervised_learning)을 사용합니다. 사용자가 보유하고 있는 이미지들을 ThanoSQL의 DB에 올리면 인공지능 알고리즘을 통해 비슷한 이미지는 가깝게 다른 이미지들은 멀리 배치하며 스스로 학습을 진행합니다. 정답이 없는 데이터셋에서 이미지의 일반적인 표현을 학습하고 소량의 목푯값(Target)이 있는 이미지로 미세 조정하여 분류나 회귀 작업에 활용할 수 있습니다. 

또한, ThanoSQL은 인공지능 알고리즘을 이용해서 데이터 세트를 수치화 합니다. 이렇게 수치화 된 데이터는 DB의 컬럼 내에 저장되고, 이미지 간 유사도(거리) 계산을 통해 비슷한 이미지를 검색하는데 사용됩니다.

__아래는 ThanoSQL 유사 이미지 검색 알고리즘의 활용 및 예시 입니다.__   

- 사용자가 좋아하는 이미지를 입력받으면 DB 상에 저장되어 있는 미술 작품 중에서 비슷한 느낌의 미술 작품들을 검색하고 사용자에게 추천합니다. <br>
- 수천장의 사진이 담겨있는 앨범에서 유사 이미지를 찾아낼 수 있습니다.<br>
- 이미지의 수치화 된 값을 ThanoSQL의 DB에 저장하고, ThanoSQL Auto-ML 회귀/분류 예측 모델을 이용해서 나만의 검색 엔진이나 인공지능 모델을 만들 수 있습니다. <br>
 
!!! note "본 튜토리얼에서는" 
    :point_right: 이번 튜토리얼에서는 <mark style="background-color:#FFD79C">MNIST 손글씨 데이터 세트</mark>를 사용합니다. 각 이미지는 0~1 사이의 값을 갖는 고정 크기(28x28 = 784 픽셀) 이며, 여러 사람들이 손으로 쓴 0~9까지 숫자를 정답과 함께 제공합니다. 1,000개의 학습용 데이터 세트와 200개의 테스트용 데이터 세트로 이루어져 있습니다. <br> 
    
ThanoSQL을 사용하여 손글씨 데이터를 입력하고 DB 내에서 입력 이미지와 유사한 이미지를 검색해주는 모델을 만들어 봅니다. 

[![IMAGE](/img/thanosql_search/simclr_search/simclr_img7.png "MNIST 데이터")](/img/thanosql_search/simclr_search/simclr_img7.png)

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
COPY mnist_train
OPTIONS(overwrite=True)
FROM "tutorial_data/mnist_data/mnist_train.csv"
```
```sql
%%thanosql
COPY mnist_test
OPTIONS(overwrite=True) 
FROM "tutorial_data/mnist_data/mnist_test.csv"
```

!!! note "__쿼리 세부 정보__"
    - "__COPY__" 쿼리 구문을 사용하여 DB에 저장 할 데이터 세트명을 지정합니다. 
    - "__OPTIONS__" 쿼리 구문을 통해 __COPY__ 에 사용할 옵션을 지정합니다.
        - "overwrite" : 동일 이름의 데이터 세트가 DB상에 존재하는 경우 덮어쓰기 가능 유무 설정. True일 경우 기존 데이터 세트는 새로운 데이터 세트로 변경됨 (True|False, DEFAULT : False) 
    

## __1. 데이터 세트 확인__

손글씨 분류 모델을 만들기 위해 ThanoSQL [DB](https://ko.wikipedia.org/wiki/%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4)에 저장되어 있는 <mark style="background-color:#FFEC92">mnist_train</mark> 테이블을 사용합니다. <mark style="background-color:#FFEC92">mnist_train</mark> 테이블은 <mark style="background-color:#FFD79C">MNIST</mark> 이미지 파일들이 저장되어 있는 경로와 파일 이름 그리고 라벨 정보가 담겨 있는 테이블입니다. 아래의 쿼리문을 실행하고 테이블의 내용을 확인합니다.


```sql
%%thanosql
SELECT * 
FROM mnist_train 
LIMIT 5
```

[![IMAGE](/img/thanosql_search/simclr_search/simclr_img1.png)](/img/thanosql_search/simclr_search/simclr_img1.png)


!!! note "데이터 테이블 이해하기" 
    <mark style="background-color:#FFEC92">mnist_train</mark> 테이블은 아래와 같은 정보를 담고 있습니다. "6782.jpg" 이미지 파일은 숫자 5를 쓴 손글씨 이미지입니다.

    - <mark style="background-color:#D7D0FF">image_path</mark>: 이미지 경로
    - <mark style="background-color:#D7D0FF">filename</mark>: 파일 이름
    - <mark style="background-color:#D7D0FF">label</mark> : 이미지 라벨

## __2. 이미지 수치화 모델 생성__

이전 단계에서 확인한 <mark style="background-color:#FFEC92">mnist_train</mark> 테이블을 사용하여 이미지 수치화 모델을 만듭니다. 아래의 쿼리 구문을 실행하여 <mark style="background-color:#E9D7FD">my_image_search_model</mark>이라는 이름의 모델을 만듭니다.  
(쿼리 실행 시 예상 소요 시간 : 1 min)
```sql
%%thanosql
BUILD MODEL my_image_search_model
USING SimCLR
OPTIONS (
    image_col="image_path",
    max_epochs=1,
    overwrite=True
    )
AS 
SELECT * 
FROM mnist_train
```

!!! note "쿼리 세부 정보" 
    - "__BUILD MODEL__" 쿼리 구문을 사용하여 <mark style="background-color:#E9D7FD">mnist_model</mark> 이라는 모델을 만들고 학습시킵니다.
    - "__USING__" 쿼리 구문을 통해 베이스 모델로 <mark style="background-color:#E9D7FD">SimCLR</mark> 모델을 사용할 것을 명시합니다.
    - "__OPTIONS__" 쿼리 구문을 통해 모델 생성에 사용할 옵션을 지정합니다.  
        -  "image_col" : 데이터 테이블에서 이미지의 경로를 담은 컬럼 (Default : "<mark style="background-color:#D7D0FF ">image_path</mark>")
        -  "max_epochs" : 이미지 수치화 모델을 생성하기 위한 데이터 세트 학습 횟수
        - "overwrite" : 동일 이름의 모델이 존재하는 경우 덮어쓰기 가능 유무 설정. True일 경우 기존 모델은 새로운 모델로 변경됨 (True|False, DEFAULT : False) 



다음 "__CONVERT USING__ " 쿼리 구문을 실행하여 `mnist_test` 이미지들을 수치화 합니다. 수치화 된 결과는 `mnist_test` 테이블에 <mark style="background-color:#D7D0FF ">my_image_search_model_simclr</mark>이라는 새로운 이름의 컬럼에 저장됩니다.

```sql
%%thanosql
CONVERT USING my_image_search_model
OPTIONS (
    table_name= "mnist_test",
    image_col="image_path"
    )
AS 
SELECT * 
FROM mnist_test

```

[![IMAGE](/img/thanosql_search/simclr_search/simclr_img3.png)](/img/thanosql_search/simclr_search/simclr_img3.png)

!!! note "쿼리 세부정보" 
    - "__CONVERT USING__" 쿼리 구문은 `my_image_search_model`을 이미지 수치화를 위한 알고리즘으로 사용합니다.   
    - "__OPTIONS__" 쿼리 구문을 통해 이미지 수치화 시 필요한 변수들을 정의합니다. 
        - "table_name" : ThanoSQL DB 내에 저장될 테이블 이름을 정의합니다. 
        - "image_col" : 데이터 테이블에서 이미지의 경로를 담은 컬럼(default: "image_path")


## __3. 이미지 수치화 모델을 사용해서 유사 이미지 검색하기__

이번 단계에서는 `my_image_search_model` 이미지 수치화 모델과 테스트 테이블을 사용하여 "923.jpg" 이미지 파일(손글씨 8)과 유사한 이미지를 검색합니다. <br>

[![IMAGE](/img/thanosql_search/simclr_search/simclr_img8.png){: style="width:100px"}](/img/thanosql_search/simclr_search/simclr_img8.png)

<p style = "text-align:center">923.jpg 이미지파일</p>


```sql
%%thanosql
SEARCH IMAGE images='tutorial_data/mnist_data/test/923.jpg' 
USING my_image_search_model 
AS 
SELECT * 
FROM mnist_test
```

[![IMAGE](/img/thanosql_search/simclr_search/simclr_img4.png)](/img/thanosql_search/simclr_search/simclr_img4.png)


!!! note "쿼리 세부정보" 
    - "__SEARCH IMAGE [images|audio|videos]__" 쿼리 구문은 검색하고자 하는 이미지|오디오|비디오 파일을 정의합니다.  <br>
    - "__USING__"은 이미지 수치화에 사용할 모델을 정의합니다.<br>
    - "__AS__" 쿼리 구문은 검색에 사용할 임베딩 테이블을 정의합니다. `mnist_embds` 테이블을 사용합니다 

다음 쿼리를 실행하여 "__SEARCH__" 결과를 ThanoSQL의 "__PRINT__" 쿼리 구문을 활용하여 가장 유사한 상위 4개를 출력합니다. 학습을 조금 밖에 진행하지 않았지만 8과 비슷한 이미지를 출력하는 것을 확인할 수 있습니다.


```sql
%%thanosql
PRINT IMAGE 
AS (
    SELECT image_path, my_image_search_model_simclr_similarity1 
    FROM (
        SEARCH IMAGE images='tutorial_data/mnist_data/test/923.jpg' 
        USING my_image_search_model 
        AS 
        SELECT * 
        FROM mnist_test
        )
    ORDER BY my_image_search_model_simclr_similarity1 DESC 
    LIMIT 4
    )
```

[![IMAGE](/img/thanosql_search/simclr_search/simclr_img5.png)](/img/thanosql_search/simclr_search/simclr_img5.png)

!!! danger "참고 사항"
    이미지 유사도 검색 알고리즘의 기본 학습 옵션은 이미지의 좌우상하 반전, 색상의 변화 등에 관계없이 모두 같은 이미지로 인식하도록 학습이 진행 됩니다. 강아지의 사진은 뒤집히거나 색이 변해도 강아지로 인식되어야 하기 때문입니다. 의류 이미지 등과 같이 색의 변화가 중요하거나 숫자 처럼 상하, 좌우 반전이 중요한 경우 학습 시 옵션을 변경해 주어야 합니다. 본 튜토리얼에서는 이러한 이미지 유사도 검색의 특징을 보여주고 있습니다.

## __4. 튜토리얼을 마치며__

이번 튜토리얼에서는 `MNIST` 손글씨 데이터 세트를 사용하여 이미지 수치화와 수치화 결과를 바탕으로한 유사 이미지 검색까지 진행해 보았습니다. 이번 튜토리얼에서는 이미지 유사도의 정확도보다도 작동 위주의 설명으로 진행하였습니다. 이미지 수치화 모델은 각 이미지 데이터 세트에 맞는 정밀한 튜닝과 소량의 정답을 학습 시에 추가하여 정확도를 향상 시킬 수 있습니다. 나만의 이미지 수치화 모델을 만들어 다양한 형태의 비정형 데이터 세트에 검색 기능을 추가하고 Auto-ML 기법을 이용한 나만의 모델을 배포할 수 있습니다.
<br>
다음 단계에서 이미지 수치화 모델의 다양한 "__OPTIONS__" 쿼리 구문과 학습 방법을 더욱 심도있게 다뤄봅니다. 나만의 정확한 이미지 변환 모델 구축방법에 대해 더욱 자세히 알고 싶다면 다음 튜토리얼들을 진행 해보세요.

* [나만의 데이터 업로드하기](/how-to_guides/ThanoSQL_connecting/data_upload/)
* [중급 유사 이미지 검색 모델 만들기]

!!! tip "__나만의 서비스를 위한 모델 배포 관련 문의__"
    ThanoSQL을 활용해 나만의 모델을 만들거나, 나의 서비스에 적용하는데 어려움이 있다면 언제든 아래로 문의주세요😊

    유사 이미지 검색 모델 구축 관련 문의: contact@smartmind.team
