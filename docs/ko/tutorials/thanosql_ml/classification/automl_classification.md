---
title: Auto-ML을 사용하여 분류 모델 만들기
---

# __Auto-ML을 사용하여 분류 모델 만들기__ 

## 시작 전 사전정보

- 튜토리얼 난이도 : ★☆☆☆☆
- 읽는데 걸리는 시간 : 4 분
- 사용 언어 : [SQL](https://ko.wikipedia.org/wiki/SQL) (100%)
- 실행 파일 위치 : tutorial/ml/분류 모델 만들기/Auto-ML을 사용하여 분류 모델 만들기.ipynb 
- 참고 문서 : [(캐글) Titanic - Machine Learning from Disaster](https://www.kaggle.com/competitions/titanic/overview)
- 마지막 수정날짜 : {{ git_revision_date_localized }}

## 튜토리얼 소개

!!! note "분류 작업 이해하기" 
    분류 작업은 목푯값(Target)이 속한 범주(Category 또는 Class)를 예측하기 위해 사용하는 [머신러닝(기계학습/Machine Learning)](https://ko.wikipedia.org/wiki/%EA%B8%B0%EA%B3%84_%ED%95%99%EC%8A%B5)의 한 형태입니다. 예를 들어, 남성 또는 여성을 분류하는 이진 분류와 동물의 종(개, 고양이, 토끼 등)을 예측하는 다중 분류 모두 분류 작업에 포함됩니다. <br>

기업의 특정 마케팅 프로모션에 대해 잠재고객이 긍정적인 반응을 보일 것인지 아닌지를 예측하기 위해서는 고객의 [CRM(Customer Relationship Management)](https://ko.wikipedia.org/wiki/%EA%B3%A0%EA%B0%9D_%EA%B4%80%EA%B3%84_%EA%B4%80%EB%A6%AC) 데이터(인구 통계학 정보, 고객의 행동/검색 데이터 등)를 이용할 수 있습니다. 이 경우 CRM 데이터에서 표현되는 [특성(Feature)](https://ko.wikipedia.org/wiki/%ED%8A%B9%EC%A7%95_(%EA%B8%B0%EA%B3%84_%ED%95%99%EC%8A%B5))이 입력 데이터로 사용되며, 예측하고자 하는 값인 목푯값은 프로모션에 대한 대상 고객의 반응이 긍정(1 또는 True) 또는 부정(0 또는 False)일지 여부입니다. 이러한 분류 모델을 이용해서, 마케팅에 노출되지 않은 고객들의 프로모션에 대한 반응을 미리 예측하고 적절한 고객에게 마케팅을 노출함으로써 마케팅 효율을 지속적으로 높일 수 있습니다. 

__아래는 ThanoSQL 분류 모델의 활용 및 예시입니다.__  

- 분류 모델은 현재 사용자의 이탈에 대한 조기 탐지를 가능하게 하고 문제(이탈)에 대해 사전에 대응할 수 있도록 해줍니다. 과거 데이터를 통해 이탈 고객의 특성을 파악할 수 있게 되고 이탈 가능성이 높은 사용자를 사전에 발견함으로써 적절한 조치가 가능해집니다. 이를 통해 고객이탈 방지 및 매출 증대 효과를 기대할 수 있습니다.

- 온라인 플랫폼 내에서 사용자의 [세그먼트](https://ko.wikipedia.org/wiki/%EC%8B%9C%EC%9E%A5%EC%84%B8%EB%B6%84%ED%99%94)를 예측할 수 있습니다. 대부분의 서비스 사용자들은 서로 다른 특성을 가지고 다양한 행동방식(User Behaviour)과 니즈(Needs)를 가지고 있습니다. 분류 예측 모델은 서비스 사용자의 특성을 이용하여 세분화된 집단을 식별하고 그들에게 맞춤화된 전략 수립을 가능하게 합니다.  

!!! note "본 튜토리얼에서는"
    :point_right: 대표적인 머신러닝 경진대회 플랫폼인 [캐글](https://www.kaggle.com/)의 입문자를 위한 <mark style="background-color:#FFD79C"> __Titanic: Machine Learning from Disaster__</mark> 데이터 세트를 사용하여 생존자 예측 분류 모델을 만듭니다. 이 대회의 목표는 아래와 같습니다. 
    (참고로, 해당 대회의 데이터는 1912년 4월 15일 실제 타이타닉 사건 때, 탑승했었던 승객들 명단입니다.)

__타이타닉에서 살아남을 수 있는 승객을 예측하기__

ThanoSQL에서는 자동화된 머신러닝(__Auto-ML__) 도구를 제공합니다. 본 튜토리얼에서는 Auto-ML을 사용하여 타이타닉에서 살아남을 수 있는 승객을 예측합니다. ThanoSQL에서 제공하는 Auto-ML은 모델(Model)개발을 위한 프로세스를 자동화하고, 데이터 과학(Data Science)에 대한 전문지식이 없어도 데이터의 수집 및 저장, 머신러닝 모델의 개발 및 배포(엔드투엔드 머신러닝 파이프라인)를 하나의 언어(ThanoSQL)만으로 가능하도록 지원합니다.

__자동화된 ML을 사용하면 다음과 같은 장점이 있습니다__ 

1. 광범위한 프로그래밍 또는 데이터 과학에 대한 지식이 없어도 머신러닝 솔루션의 구현 및 배포가 가능  
2. 개발모델의 배포에 들어가는 시간 및 리소스를 절약   
3. 의사결정을 위해 보유하고 있는 데이터를 이용한 신속한 문제해결이 가능  

이제부터 ThanoSQL을 사용하여 간단하게 타이타닉에서 살아남을 수 있는 승객을 예측하는 분류 모델을 만들어 봅니다.

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
COPY titanic_train 
OPTIONS(overwrite=True)
FROM "tutorial_data/titanic_data/titanic_train.csv"
```
```sql
%%thanosql
COPY titanic_test
OPTIONS(overwrite=True) 
FROM "tutorial_data/titanic_data/titanic_test.csv"
```

!!! note "__쿼리 세부 정보__"
    - "__COPY__" 쿼리 구문을 사용하여 DB에 복사 할 데이터 세트명을 지정합니다. 
    - "__OPTIONS__" 쿼리 구문을 통해 __COPY__ 에 사용할 옵션을 지정합니다.
        - "overwrite" : 동일 이름의 데이터 세트가 DB상에 존재하는 경우 덮어쓰기 가능 유무 설정. True일 경우 기존 데이터 세트는 새로운 데이터 세트로 변경됨 (True|False, DEFAULT : False) 
    


## __1. 데이터 세트 확인__

생존자 예측 분류 모델을 만들기 위해 우리는 ThanoSQL DB에 저장되어 있는 <mark style="background-color:#FFEC92 ">__titanic_train__</mark> 테이블을 사용합니다. 아래의 쿼리문을 실행하면서 테이블 내용을 확인합니다.

```sql
%%thanosql
SELECT * 
FROM titanic_train 
LIMIT 5 
```
[![IMAGE](/img/thanosql_ml/classification/automl/img1.png)](/img/thanosql_ml/classification/automl/img1.png)

!!! note "__데이터 이해하기__"
    <mark style="background-color:#FFEC92 ">__tianic_train__</mark> 데이터 세트는 다음과 같은 정보를 담고 있습니다.

    - <mark style="background-color:#D7D0FF">passengerid</mark> : 탑승승객 아이디
    - <mark style="background-color:#D7D0FF">survived</mark> : 탑승승객  생존 여부
    - <mark style="background-color:#D7D0FF">pclass</mark> : 탑승승객 티켓 등급
    - <mark style="background-color:#D7D0FF">name</mark> : 탑승승객 이름
    - <mark style="background-color:#D7D0FF">sex</mark> : 탑승승객 성별
    - <mark style="background-color:#D7D0FF">age</mark> : 탑승승객 나이
    - <mark style="background-color:#D7D0FF">sibsp</mark> : 탑승한 형제 자매 또는 배우자 수
    - <mark style="background-color:#D7D0FF">parch</mark> : 탑승한 부모 또는 자녀의 수
    - <mark style="background-color:#D7D0FF">ticket</mark> : 티켓 번호
    - <mark style="background-color:#D7D0FF">fare</mark> : 요금
    - <mark style="background-color:#D7D0FF">cabin</mark> : 선실
    - <mark style="background-color:#D7D0FF">embarked</mark> : 승선지 또는 항구
 
이번 튜토리얼에서는 추가적인 쿼리문을 이용한 데이터 전처리가 필요한
<mark style="background-color:#D7D0FF">name</mark>, <mark style="background-color:#D7D0FF">ticket</mark>, <mark style="background-color:#D7D0FF">cabin</mark>컬럼을 제외하고 모델 학습을 진행하겠습니다.

## __2. 분류 모델 생성__

이전 단계에서 확인한 <mark style="background-color:#FFEC92 ">titanic_train</mark> 데이터를 사용하여 생존자 예측 분류 모델을 만듭니다. 아래의 쿼리 구문을 실행시켜 <mark style="background-color:#E9D7FD ">titanic_automl_classification</mark> 이름의 모델을 만들어 봅니다.  
(쿼리 실행 시 예상 소요 시간: 8 min)  

```sql
%%thanosql
BUILD MODEL titanic_automl_classification 
USING AutomlClassifier 
OPTIONS (
    target='survived', 
    impute_type='iterative',  
    features_to_drop=['name', 'ticket', 'passengerid', 'cabin'],
    time_left_for_this_task = 300,
    overwrite = True
    ) 
AS 
SELECT * 
FROM titanic_train
```

!!! note "__쿼리 세부 정보__" 
    - "__BUILD MODEL__" 쿼리 구문을 사용하여 <mark style="background-color:#E9D7FD ">titanic_automl_classification</mark>이라는 모델을 만들고 학습시킵니다.
    - "__OPTIONS__" 쿼리 구문을 통해 모델 생성에 사용할 옵션을 지정합니다.
        - "target" : 분류 모델의 목푯값이 담겨 있는 컬럼명
        - "impute_type" : 데이터 테이블의 빈 값(NaN)을 처리하는 방법 설정 ('simple'|'iterative' , DEFAULT : 'simple')        
        - "features_to_drop" : 데이터 테이블에서 학습에 이용하지 못하는 컬럼명 리스트
        - "time_left_for_this_task" : 적합한 분류 예측 모델을 찾는데 소요되는 시간 (DEFAULT : 300)
        - "overwrite" : 동일 이름의 모델이 존재하는 경우 덮어쓰기 가능 유무 설정. True일 경우 기존 모델은 새로운 모델로 변경됨 (True|False, DEFAULT : False) 

!!! warning
    Auto-ML 분류 모델 생성시 [OPTIONS](/how-to_guides/OPTIONS/#1-automlclassifier) 내에 명시된 파라미터 외 다른 파라미터 사용시 모델 생성은 될 수 있으나 설정한 값들은 모두 무시 됩니다. 
    


## __3. 생성된 모델 평가__ 
아래의 쿼리문을 실행하여 이전 단계에서 만든 예측 모델의 성능을 평가합니다.

```sql
%%thanosql 
EVALUATE USING titanic_automl_classification 
OPTIONS (
    target = 'survived'
    )
AS
SELECT *
FROM titanic_train
```
[![IMAGE](/img/thanosql_ml/classification/automl/img2.png)](/img/thanosql_ml/classification/automl/img2.png)

!!! note "__쿼리 세부 정보__"   
    - "__EVALUATE USING__" 쿼리 구문을 사용하여 구축한  <mark style="background-color:#E9D7FD ">titanic_automl_classification</mark>이라는 모델을 평가합니다. 
    - "__OPTIONS__" 쿼리 구문을 통해 모델 평가에 사용할 옵션을 지정합니다.
        - "target" : 분류 예측 모델에 목푯값이 되는 컬럼의 이름

!!! warning "__평가용 데이터 세트__"
    평가용 데이터 세트는 학습 데이터 세트의 일부를 분리하여 학습에 사용되지 않아야 하나 튜토리얼에서는 편의상 학습 데이터를 사용합니다
         
         
## __4. 생성된 모델을 사용하여 생존자 예측__ 

이전 단계에서 생성한 생존자 예측 모델을 사용해 탑승 승객 정보에 따른 생존 여부를 예측해 봅니다. 테스트용 데이터 세트(학습에 이용되지 않은 데이터 테이블, <mark style="background-color:#FFEC92 ">__titanic_test__</mark>)를 사용합니다.

```sql
%%thanosql 
PREDICT USING titanic_automl_classification
AS 
SELECT * 
FROM titanic_test
```

[![IMAGE](/img/thanosql_ml/classification/automl/img3.png)](/img/thanosql_ml/classification/automl/img3.png)

!!! note "__쿼리 세부 정보__" 
    "__PREDICT USING__" 쿼리 구문을 사용하여 이전 단계에서 만든 <mark style="background-color:#E9D7FD ">titanic_automl_classification</mark> 모델을 예측에 사용합니다. "__PREDICT__"의 경우 생성된 모델의 절차를 따르기 때문에 특별한 옵션이 필요없습니다. 

## __5. 튜토리얼을 마치며__ 

이번 튜토리얼에서는 [캐글](https://www.kaggle.com/)의 <mark style="background-color:#FFD79C"> __Titanic: Machine Learning from Disaster__</mark> 데이터를 사용하여 타이타닉 생존자 분류 예측 모델을 만들어 보았습니다. 초급 단계 튜토리얼인만큼 정확도 향상을 위한 과정보다는 전반적인 프로세스 위주의 설명으로 진행 하였습니다. 향상 된 분류 모델 구축에 대해 자세히 알고 싶다면 중급 튜토리얼을 진행해 볼 것을 권장드립니다.

다음 [중급 분류 예측 모델 만들기] 튜토리얼에서는 정확도 향상을 위한 "__OPTIONS__"에 대해 더욱 심도있게 다뤄보겠습니다. 중급,고급 단계를 마치고 나만의 서비스/프로덕트를 위한 분류 예측 모델을 만들어 보세요. 중급 단계에서는 ThanoSQL의 AutoML이 제공하는 다양한 "__OPTIONS__"를 활용하여 정교한 분류 예측 모델을 만들어 볼 예정입니다. 또한, 중급 단계를 마치신 이후 고급 단계에서는 비정형 데이터를 수치화시킨 후 AutoML의 학습 요소로 포함하여 분류 예측 모델을 만들 수 있습니다.

* [나만의 데이터 업로드하기](/how-to_guides/ThanoSQL_connecting/data_upload/)
* [중급 이미지 분류 모델 만들기]
* [이미지 변환과 Auto-ML을 이용한 나만의 모델 만들기]
* [나만의 이미지 분류 모델 배포하기](/how-to_guides/ThanoSQL_connecting/thanosql_api/rest_api_thanosql_query/)

!!! tip "__나만의 서비스를 위한 모델 배포 관련 문의__"
    ThanoSQL을 활용해 나만의 모델을 만들거나, 나의 서비스에 적용하는데 어려움이 있다면 언제든 아래로 문의주세요😊

    분류 모델 구축 관련 문의: contact@smartmind.team




