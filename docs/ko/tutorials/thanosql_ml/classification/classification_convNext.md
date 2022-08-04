---
title: 이미지 분류 모델 만들기
---

# __이미지 분류 모델 만들기__

## 시작 전 사전 정보

- 튜토리얼 난이도: ★☆☆☆☆
- 읽는데 걸리는 시간 : 10분
- 사용 언어 : [SQL](https://ko.wikipedia.org/wiki/SQL) (100%)
- 실행 파일 위치 : tutorial/ml/분류 모델 만들기/이미지 분류 모델 만들기.ipynb
- 참고 문서 : [(AI-Hub) 상품이미지 데이터](https://aihub.or.kr/aihubdata/data/view.do?currMenu=115&topMenu=100&aihubDataSe=realm&dataSetSn=64), [A ConvNet for the 2020s](https://arxiv.org/abs/2201.03545)
- 마지막 수정날짜 : {{ git_revision_date_localized }}


## 튜토리얼 소개

!!! note "분류 작업 이해하기"
    분류 작업은 목푯값(Target)이 속한 범주(Category 또는 Class)를 예측하기 위해 사용하는 [머신러닝(기계학습/Machine Learning)](https://ko.wikipedia.org/wiki/%EA%B8%B0%EA%B3%84_%ED%95%99%EC%8A%B5)의 한 형태입니다. 예를 들어, 남성 또는 여성을 분류하는 이진 분류와 동물의 종(개, 고양이, 토끼 등)을 예측하는 다중 분류 모두 분류 작업에 포함됩니다.

2010년부터 인공지능 모델을 사용해서 이미지를 분류하는 대회([ImageNet](https://en.wikipedia.org/wiki/ImageNet))가 열려 왔습니다. 대회 초기 우승한 모델의 분류 성능은 약 72%였으나 2015년 우승한 [ResNet](https://arxiv.org/abs/1512.03385) 모델은 약 96%의 성능을 기록하며 특정 영역에서는 인간의 분류 능력을 뛰어넘기 시작했습니다.

!!! tip ""
    같은 데이터의 인간의 분류 능력은 약 95%라고 합니다.

정확한 이미지 분류를 위해서는 대량의 데이터 세트에 대한 [데이터 라벨링](https://en.wikipedia.org/wiki/Labeled_data) 작업이 필요하지만, 사전 학습된 인공지능 모델의 가중치를 사용하여 소량의 라벨링 된 데이터 세트에 맞게 재보정해서 사용하는 방법들이 널리 적용되고 있습니다. 결과적으로 비교적 적은 수의 데이터를 가지고도, 딥러닝 모델의 훈련을 가능하게 합니다.

ThanoSQL에서는 다양한 사전 학습된 인공지능 모델을 제공하고 있으며, 간단한 쿼리 구문을 통해 모델을 만들 수 있게 제공합니다. 이를 통해, 사용자는 적절하게 학습된 이미지 분류 모델로부터 특징을 정량화하기 힘든 이미지로부터 잠재적인 인사이트를 추출하고 다양한 서비스에 활용할 수 있습니다.

__아래는 ThanoSQL 이미지 분류 모델의 활용 및 예시입니다.__

-  ThanoSQL 이미지 분류 모델은 온라인 상품 판매 서비스에서 상품 등록을 위한 적절한 범주를 찾는 과정의 반복을 줄여줍니다. 간단한 쿼리 구문만으로 제품 사진의 범주를 분류할 수 있습니다. 사용자는 일부 잘못 분류 된 데이터를 수정하는 작업만을 진행함으로써 기존 이미지 분류에 사용하던 시간을 절약할 수 있습니다.

- 미술 작품의 대여 또는 판매 서비스를 하는 경우 각 작품의 느낌, 기법, 어울리는 장소 등 기준이 모호하여 분류하기 어려운 작업들을 이미지 분류 모델을 사용해 대략적으로 분류할 수 있습니다.

- 제조 공장에서 육안으로 확인하던 스크래치나 파손과 같은 불량품을 감지하고 분류할 수 있습니다. 레이저 스펙트럼과 같은 신호 정보도 시각화 변환과 같은 처리를 통해서 이미지 분류 모델에 적용할 수 있습니다.

!!! tip ""
    미술 작품을 좋아하는 사람들의 행동 이력(구매 또는 대여 이력, 선호도 등)을 데이터 베이스에 저장하고 활용해서 특정 작품을 좋아할 것으로 예상되는 그룹을 찾는 분류 모델을 만들 수도 있습니다. 즉, 미술 작품의 이미지만을 이용해서 연령대(20대, 30대, 40대 등), 성별(남, 여), 전시 장소(집, 카페, 회사 등) 등에서의 선호도를 예측하는 모델을 만들 수 있습니다.


!!! note "본 튜토리얼에서는"
    :point_right: 대표적인 AI 오픈데이터 공유 플랫폼인 [AI-Hub](https://aihub.or.kr/)의 `상품 이미지` 데이터 세트를 사용하여 10,000종 이상의 상품을 분류하는 모델을 구축합니다. 구축된 모델은 스마트물류창고, 무인 스토어등에서 탐지, 식별 솔루션으로 활용이 가능합니다. 데이터 세트는 일반적으로 이미지 분류 기술의 학습에 활용하는 이미지 및 라벨(정답)쌍의 약 10,000 종 이상의 상품 데이터 세트로 구성되어 있고 총 1,440,000 장의 이미지가 포함되어 있습니다. 본 튜토리얼에서는 ThanoSQL의 사용방법을 익히고 빠른 결과 확인을 위해, 훈련용 데이터 1,800장과 테스트 데이터 200장만을 사용합니다. <br>

![상품 이미지 예시](/img/thanosql_ml/classification/classification_convNext/classification_convNext_data_intro.png)



!!! warning "튜토리얼 주의 사항"
    - 이미지 분류 모델은 하나의 이미지에서 하나의 목푯값(Target, 범주/레이블/라벨)를 예측하는 용도로 사용할 수 있습니다.
    - 이미지의 경로를 나타내는 컬럼(Column)과, 이미지의 목푯값을 나타내는 컬럼이 존재해야 합니다.
    - 해당 이미지 분류 모델의 베이스 모델(`CONVNEXT`)은 GPU를 사용합니다. 사용한 모델의 크기와 배치 사이즈에 따라 GPU 메모리가 부족할 수 있습니다. 이 경우, 더 작은 모델을 사용하시거나 배치 사이즈를 줄여보십시오.

## __0. 데이터 세트 준비__

ThanoSQL의 쿼리 구문을 사용하기 위해서는 [ThanoSQL 워크스페이스 사용](/getting_started/how_to_use_ThanoSQL/#5-thanosql)
에서 언급된 것처럼 API 토큰을 생성하고 아래의 쿼리를 실행해야 합니다.

```sql
%load_ext thanosql
```
```sql
%thanosql API_TOKEN=<발급받은_API_TOKEN>
```
```sql
%%thanosql
COPY product_image_train
OPTIONS(overwrite = True)
FROM "tutorial_data/product_image_data/product_image_train.csv"
```
```sql
%%thanosql
COPY product_image_test
OPTIONS(overwrite = True)
FROM "tutorial_data/product_image_data/product_image_test.csv"
```

!!! note "__쿼리 세부 정보__"
    - "__COPY__" 쿼리 구문을 사용하여 DB에 저장 할 데이터 세트명을 지정합니다. 
    - "__OPTIONS__" 쿼리 구문을 통해 __COPY__ 에 사용할 옵션을 지정합니다.
        - "overwrite" : 동일 이름의 데이터 세트가 DB상에 존재하는 경우 덮어쓰기 가능 유무 설정. True일 경우 기존 데이터 세트는 새로운 데이터 세트로 변경됨 (True|False, DEFAULT : False) 



## __1. 데이터 세트 확인__

본 튜토리얼을 진행하기 위해 우리는 ThanoSQL DB에 저장되어 있는  <mark style="background-color:#FFEC92 ">product_image_train</mark> 테이블을 사용합니다. 아래의 쿼리문을 실행하여 테이블 내용을 확인합니다.

```sql
%%thanosql
SELECT *
FROM product_image_train
LIMIT 5
```
<a href ="/img/thanosql_ml/classification/classification_convNext/train_data_limit_5.png">
    <img src = "/img/thanosql_ml/classification/classification_convNext/train_data_limit_5.png"></img>
</a>

!!! note "__데이터 이해하기__"
    -  <mark style="background-color:#D7D0FF ">image_path</mark>: 각 이미지의 파일의 위치 정보
    -  <mark style="background-color:#D7D0FF ">div_l</mark> : 상품의 대분류
    -  <mark style="background-color:#D7D0FF ">div_m</mark> : 상품의 중분류
    -  <mark style="background-color:#D7D0FF ">div_s</mark> : 상품의 소분류
    -  <mark style="background-color:#D7D0FF ">div_n</mark> : 상품의 세분류
    -  <mark style="background-color:#D7D0FF ">comp_nm</mark> : 제조사
    -  <mark style="background-color:#D7D0FF ">img_prod_nm</mark> : 상품명(이미지상)
    -  <mark style="background-color:#D7D0FF ">multi</mark> : 복수 상품 이미지인지 여부

```sql
%%thanosql
PRINT IMAGE
AS
SELECT image_path
FROM product_image_train
LIMIT 5
```
<a href ="/img/thanosql_ml/classification/classification_convNext/print_image_train_data.png">
    <img src = "/img/thanosql_ml/classification/classification_convNext/print_image_train_data.png"></img>
</a>

## __2. 사전 학습된 모델을 사용하여 상품 이미지 분류 결과 예측__

다음 쿼리문을 실행하면, 사전에 학습을 해 둔 상품 이미지 분류모델, <mark style="background-color:#E9D7FD ">tutorial_product_classifier</mark>모델을 사용하여 결과를 빠르게 예측해볼 수 있습니다.

```sql
%%thanosql
PREDICT USING tutorial_product_classifier
AS
SELECT *
FROM product_image_test
```
<a href ="/img/thanosql_ml/classification/classification_convNext/predict_on_test_data_1.png">
    <img src = "/img/thanosql_ml/classification/classification_convNext/predict_on_test_data_1.png"></img>
</a>

## __3. 이미지 분류 모델 생성__

이전 단계에서 확인한  <mark style="background-color:#FFEC92 ">product_image_train</mark> 데이터 세트를 사용하여 이미지 분류 모델을 만듭니다. 아래의 쿼리 구문을 실행하여 <mark style="background-color:#E9D7FD ">my_product_classifier</mark>이라는 이름의 모델을 만듭니다.
(쿼리 실행 시 예상 소요 시간: 5 min)

```sql
%%thanosql
BUILD MODEL my_product_classifier
USING ConvNeXt_Tiny
OPTIONS (
  image_col='image_path',
  label_col='div_l',
  epochs=1,
  overwrite=True
  )
AS
SELECT *
FROM product_image_train
```

!!! note "쿼리 세부 정보"
    - "__BUILD MODEL__" 쿼리 구문을 사용하여 <mark style="background-color:#E9D7FD ">my_product_classifier</mark> 모델을 만들고 학습시킵니다.
    - "__USING__" 쿼리 구문을 통해 베이스 모델로 `ConvNeXt_Tiny`를 사용할 것을 명시합니다.
    - "__OPTIONS__" 쿼리 구문을 통해 모델 생성에 사용할 옵션을 지정합니다.
        - "image_col" : 이미지 경로를 담은 컬럼의 이름
        - "label_col" : 목푯값의 정보를 담은 컬럼의 이름
        - "epochs : 모든 학습 데이터 세트를 학습하는 횟수

!!! tip ""
    여기서는 빠르게 학습하기 위해 "epochs"를 1로 지정했습니다. 일반적으로 숫자가 클수록 많은 계산 시간이 소요되지만 학습이 진행됨에 따라 예측 성능이 올라갑니다.

!!! note ""
    __overwrite가 True일 때__, 사용자는 이전 생성했던 데이터 테이블과 같은 이름의 데이터 테이블을 생성할 수 있습니다.  
    반면, __overwrite가 False일 때__, 사용자는 이전에 생성했던 데이터 테이블과 같은 이름의 데이터 테이블을 생성할 수 없습니다.

## __4. 생성된 모델을 사용하여 상품 이미지 분류 결과 예측__

이전 단계에서 만든 상품 이미지 분류 모델(<mark style="background-color:#FFEC92 ">my_product_classifier</mark>)을 사용해서 특정 이미지(학습에 이용되지 않은 데이터 테이블, <mark style="background-color:#D7D0FF">product_image_test</mark>)의 목푯값을 예측해 봅니다.  아래 쿼리를 수행하고 나면, 예측 결과는 <mark style="background-color:#D7D0FF">predicted</mark> 컬럼에 저장되어 반환됩니다.

```sql
%%thanosql
PREDICT USING my_product_classifier
OPTIONS (
    image_col='image_path'
    )
AS
SELECT *
FROM product_image_test
```
<a href ="/img/thanosql_ml/classification/classification_convNext/predict_on_test_data_2.png">
    <img src = "/img/thanosql_ml/classification/classification_convNext/predict_on_test_data_2.png"></img>
</a>

!!! note "__쿼리 세부 정보__"
    - "__PREDICT USING__" 쿼리 구문을 통해 이전 단계에서 만든 <mark style="background-color:#E9D7FD ">my_product_classifier</mark> 모델을 예측에 사용합니다.
    - "__OPTIONS__" 쿼리 구문을 통해 예측에 사용할 옵션을 지정합니다.
        - "image_col" : 예측에 사용할 이미지의 경로가 기록되어 있는 컬럼의 이름

<br>

## __5. 튜토리얼을 마치며__

이번 튜토리얼에서는 <mark style="background-color:#FFD79C">상품 이미지</mark> 데이터 세트를 사용하여 이미지 분류 모델을 만들어 보았습니다. 초급 단계 튜토리얼인만큼 정확도 향상을 위한 과정 설명보다는 작동 위주의 설명으로 진행했습니다. 이미지 분류 모델은 각 플랫폼이나 서비스에 맞는 정밀한 튜닝을 통해 정확도를 향상 시킬 수 있고 적은 양의 데이터 라벨링만으로도 대부분 만족스러운 결과를 얻을 수 있습니다. 나만의 데이터를 이용해서 베이스 모델을 학습하거나, 자가학습(Self-supervised) 모델 등을 이용해 나의 데이터를 수치화하여 변환한 후 자동화 된 머신러닝(Auto-ML) 기법을 이용한 배포도 가능합니다. 다양한 비정형 데이터(오디오, 비디오, 텍스트 등)와 수치형 데이터들을 결합하여 나만의 모델을 만들고 경쟁력있는 서비스를 제공해 보세요.

다음 단계인  [중급 이미지 분류 모델 만들기] 튜토리얼에서는 이미지 분류 모델을 더욱 심도있게 다뤄봅니다. 내 서비스를 위한 나만의 이미지 분류 모델 구축방법에 대해 더욱 자세히 알고 싶다면 다음 튜토리얼들을 진행해보세요.

* [나만의 데이터 업로드하기](/how-to_guides/ThanoSQL_connecting/data_upload/)
* [중급 이미지 분류 모델 만들기]
* [이미지 변환과 Auto-ML을 이용한 나만의 모델 만들기]
* [나만의 이미지 분류 모델 배포하기](/how-to_guides/thanosql_api/rest_api_thanosql_query/)


!!! tip "__나만의 서비스를 위한 모델 배포 관련 문의__"
    ThanoSQL을 활용해 나만의 모델을 만들거나, 나의 서비스에 적용하는데 어려움이 있다면 언제든 아래로 문의주세요😊

    이미지 분류 모델 구축 관련 문의: contact@smartmind.team


