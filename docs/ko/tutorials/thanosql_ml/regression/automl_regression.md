---
title: Auto-ML을 사용하여 예측 모델 만들기
---

# __Auto-ML을 사용하여 예측 모델 만들기__ 

## 시작 전 사전정보

- 튜토리얼 난이도 : ★☆☆☆☆
- 읽는 시간 : 5분
- 사용 언어 : [SQL](https://ko.wikipedia.org/wiki/SQL) (100%)
- 실행 파일 위치 : tutorial/ml/회귀 모델 만들기/Auto-ML을 사용하여 예측 모델 만들기.ipynb
- 참고 문서 : [(캐글) Bike Sharing Demand](https://www.kaggle.com/competitions/bike-sharing-demand/overview)
- 마지막 수정날짜 : {{ git_revision_date_localized }}

## 튜토리얼 소개

!!! note "회귀 작업 이해하기"
    회귀 작업은 목푯값(Target)이 연속성을 지닌 숫자를 예측하기 위해 사용하는 [머신러닝(기계학습/Machine Learning)](https://ko.wikipedia.org/wiki/%EA%B8%B0%EA%B3%84_%ED%95%99%EC%8A%B5)의 한 형태입니다. 예를 들어, 기상 데이터가 주어졌을 때 내일 기온을 예측하거나, 특정지역의 집값을 예측할 때 사용할 수 있습니다. 

기업이 특정 금액을 광고에 사용할 경우 과거의 유사한 사례의 판매 성과 데이터를 활용하여 광고의 성과를 예측할 수 있습니다. 광고하고자 하는 제품에 대한 특성부터 제품을 판매하는 시기, 주변 시장 정보, 경쟁사의 판매량 정보, 대상 고객군에 대한 정의, 산업군의 시장 트렌드 등 데이터화 할 수 있는 모든 [특성(Feature)](https://ko.wikipedia.org/wiki/%ED%8A%B9%EC%A7%95_(%EA%B8%B0%EA%B3%84_%ED%95%99%EC%8A%B5))이 입력자료가 될 수 있습니다. 입력 데이터에서 통제 가능한 정보를 바꿔가면서 최적의 판매성과를 예측해 볼 수 있고 예측성과에 따라 광고에 소비할 비용을 조정할 수도 있습니다. 이러한 회귀 모델을 이용해서 광고 성과를 향상시키고 판매량을 지속적으로 늘릴 수 있습니다. 

__아래는 ThanoSQL 회귀 모델의 활용 및 예시입니다.__ 

 - 주식의 시가, 종가, 고가, 저가, 관련주 주가, 종합주가지수, 관련뉴스 등을 활용한 주식가격 예측 (금융)
 - 기기/설비의 온도, 진동, 소리 등 센서데이터를 이용한 고장 확률 및 잔존 수명 예측 (제조)
 - 날씨, 기온, 운량, 일사량 등을 활용한 태양광 에너지 발전량 예측 (에너지)
 - 과거 수요량 트렌드, 유가 및 환율 변동 등을 활용한 수요예측 (원자재) <br>

!!! note "본 튜토리얼에서는"
    :point_right: 대표적인 머신러닝 경진대회 플랫폼인 [캐글](https://www.kaggle.com/)의 입문자를 위한 <mark style="background-color:#FFD79C">__Bike Sharing Demand__</mark> 데이터 세트를 사용하여 자전거 수요 예측 회귀 모델을 만듭니다. 이 대회의 목표는 아래와 같습니다. (참고로, 해당 대회의 데이터는 2011년부터 2012년까지 날짜와 시간, 기온, 습도, 풍속 등의 정보를 기반으로 자전거 대여 횟수가 기재되어 있습니다.)

__특정 날짜에서 시간대별 자전거 대여 수량 예측하기__

ThanoSQL에서는 자동화된 머신러닝(__Auto-ML__)을 도구로 제공합니다. 본 튜토리얼에서는 Auto-ML을 사용하여 자전거 대여 수량을 예측합니다. ThanoSQL에서 제공하는 Auto-ML은 모델(Model)개발을 위한 프로세스를 자동화하고, 데이터 과학(Data Science)에 대한 전문지식이 없어도 데이터의 수집 및 저장, 머신러닝 모델의 개발 및 배포(엔드투엔드 머신러닝 파이프라인)를 하나의 언어(__ThanoSQL__)만으로도 가능하도록 지원합니다.

__ThanoSQL의 자동화 된 머신러닝을 사용하면 다음과 같은 장점이 있습니다.__

1. 광범위한 프로그래밍 또는 데이터과학에 대한 지식이 없어도 머신러닝 솔루션의 구현 및 배포가 가능
2. 개발모델의 배포에 들어가는 시간 및 리소스를 절약
3. 의사결정을 위해 보유하고 있는 데이터를 이용한 신속한 문제해결이 가능

이제부터 ThanoSQL을 사용하여 간단하게 자전거 대여 수량을 예측하는 회귀 모델을 만들어 봅니다. 

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
COPY bike_sharing_train 
OPTIONS(overwrite = True)
FROM "tutorial_data/bike_sharing_data/bike_sharing_train.csv"
```
```sql
%%thanosql
COPY bike_sharing_test 
OPTIONS(overwrite = True)
FROM "tutorial_data/bike_sharing_data/bike_sharing_test.csv"
```

!!! note "__쿼리 세부 정보__"
    - "__COPY__" 쿼리 구문을 사용하여 DB에 저장 할 데이터 세트명을 지정합니다. 
    - "__OPTIONS__" 쿼리 구문을 통해 __COPY__ 에 사용할 옵션을 지정합니다.
        - "overwrite" : 동일 이름의 데이터 세트가 DB상에 존재하는 경우 덮어쓰기 가능 유무 설정. True일 경우 기존 데이터 세트는 새로운 데이터 세트로 변경됨 (True|False, DEFAULT : False) 



## __1. 데이터 세트 확인__

본 튜토리얼을 진행하기 위해 우리는 ThanoSQL DB에 저장되어 있는 <mark style="background-color:#FFEC92 ">bike_sharing_train</mark> 테이블을 사용합니다. 아래의 쿼리문을 실행하여 테이블 내용을 확인합니다.

```sql
%%thanosql
SELECT * 
FROM bike_sharing_train 
LIMIT 5
```
[![IMAGE](/img/thanosql_ml/regression/img1.png)](/img/thanosql_ml/regression/img1.png)

!!! note "__데이터 이해하기__"
    <mark style="background-color:#FFEC92 ">__bike_sharing_train__</mark> 데이터 세트에는 2011년 1월부터 2012년 12월까지 날짜와 시간, 기온, 습도, 풍속 등의 정보를 기반으로 1시간 간격 동안의 자전거 대여 횟수에 대한 정보를 담고 있습니다.

    - <mark style="background-color:#D7D0FF ">datetime</mark> : 시간별 날짜
    - <mark style="background-color:#D7D0FF ">season</mark> : 계절(1 = 봄, 2 = 여름, 3 = 가을, 4 = 겨울)
    - <mark style="background-color:#D7D0FF ">holiday</mark> : 휴일(0 = 휴일이 아닌 날, 1 = 주말을 제외한 국경일 등의 휴일)
    - <mark style="background-color:#D7D0FF ">workingday</mark> : 작업일(0 = 주말 및 휴일, 1 = 주말 및 휴일이 아닌 주중)
    - <mark style="background-color:#D7D0FF ">weather</mark> : 날씨
    - <mark style="background-color:#D7D0FF ">temp</mark> : 온도
    - <mark style="background-color:#D7D0FF ">atemp</mark> : 체감온도
    - <mark style="background-color:#D7D0FF ">humidity</mark> : 상대습도
    - <mark style="background-color:#D7D0FF ">windspeed</mark> : 풍속
    - <mark style="background-color:#D7D0FF ">count</mark> : 대여 횟수

## __2. 회귀 모델 생성__

이전 단계에서 확인한 <mark style="background-color:#FFEC92 ">__bike_sharing_train__</mark> 데이터 세트를 사용하여 자전거 수요 예측 회귀 모델을 만듭니다. 아래의 쿼리 구문을 실행하여 <mark style="background-color:#E9D7FD ">bike_regression</mark>이라는 이름의 모델을 만듭니다.  
(쿼리 실행 시 예상 소요 시간 : 8 min)

```sql
%%thanosql
BUILD MODEL bike_regression
USING AutomlRegressor
OPTIONS (
 target='count', 
 impute_type='simple', 
 datetime_attribs=['datetime'],
 time_left_for_this_task = 300,
 overwrite=True
 ) 
AS
SELECT *
FROM bike_sharing_train
```

!!! note "__쿼리 세부 정보__"
    - "__BUILD MODEL__" 쿼리 구문을 사용하여 <mark style="background-color:#E9D7FD ">bike_regression</mark>라는 모델을 만들고 학습시킵니다. 
    - "__OPTIONS__" 쿼리 구문을 통해 모델 생성에 사용할 옵션을 지정합니다. 
        - "target" : 회귀 예측 모델의 목푯값이 담겨 있는 컬럼명
        - "impute_type" : 데이터 테이블의 빈 값(NaN)을 처리하는 방법 설정 ('simple'|'iterative' , DEFAULT : 'simple')        
        - "datetime_attribs" : 날짜 형식의 데이터가 담겨 있는 컬럼명 리스트
        - "time_left_for_this_task" : 적합한 회귀 예측 모델을 찾는데 소요되는 시간 (DEFAULT : 300)
        - "overwrite" : 동일 이름의 모델이 존재하는 경우 덮어쓰기 가능 유무 설정. True일 경우 기존 모델은 새로운 모델로 변경됨 (True|False, DEFAULT : False) 

!!! warning
    Auto-ML 회귀 예측 모델 생성시 [OPTIONS](/how-to_guides/OPTIONS/#2-automlregressor) 내에 명시된 파라미터 외 다른 파라미터 사용시 모델 생성은 될 수 있으나 설정한 값들은 모두 무시 됩니다. 


## __3. 생성된 모델 평가__

아래의 쿼리문을 실행하여 이전 단계에서 만든 예측 모델의 성능을 평가합니다.

```sql
%%thanosql
EVALUATE USING bike_regression 
OPTIONS (
  target='count'
  ) 
AS
SELECT *
FROM bike_sharing_train
```

[![IMAGE](/img/thanosql_ml/regression/img2.png)](/img/thanosql_ml/regression/img2.png)

!!! note "__쿼리 세부 정보__"
    - "__EVALUATE USING__" 쿼리 구문을 사용하여 구축한 <mark style="background-color:#E9D7FD ">bike_regression</mark> 모델을 평가합니다. 
    - "__OPTIONS__" 쿼리 구문을 사용하여 평가에 사용할 옵션을 지정합니다.
        - "target" : 회귀 예측 모델의 목푯값이 담겨있는 컬럼명

!!! warning "__평가용 데이터 세트__"
    평가용 데이터 세트는 학습 데이터 세트의 일부를 분리하여 학습에 사용되지 않아야 하나 튜토리얼에서는 편의상 학습 데이터를 사용합니다

## __4. 생성된 모델을 사용하여 자전거 대여 수량 예측__

이전 단계에서 만든 수요 예측 모델로 테스트용 데이터 세트(학습에 이용되지 않은 데이터 테이블, <mark style="background-color:#FFEC92 ">bike_shaing_test</mark>)에 있는 10개의 데이터에 대한 자전거 대여 수량을 예측해 봅니다.

```sql
%%thanosql
PREDICT USING bike_regression 
AS
SELECT *
FROM bike_sharing_test
LIMIT 10
```
[![IMAGE](/img/thanosql_ml/regression/img3.png)](/img/thanosql_ml/regression/img3.png)
!!! note "__쿼리 세부 정보__"  
    - "__PREDICT USING__" 쿼리 구문을 사용하여 <mark style="background-color:#E9D7FD ">bike_regression</mark> 모델을 예측에 사용합니다. 
    - "__PREDICT__"의 경우 생성된 모델의 절차를 따르기 때문에 특별한 옵션값이 필요없습니다.

## __5. 튜토리얼을 마치며__

이번 튜토리얼에서는 [캐글](https://www.kaggle.com)의 <mark style="background-color:#FFD79C">Bike Sharing Demand</mark> 데이터 세트를 사용하여 자전거 수요 예측 회귀 모델을 만들어 보았습니다. 초급 단계 튜토리얼인만큼 정확도 향상을 위한 과정보다는 전반적인 프로세스 위주의 설명으로 진행 하였습니다. 향상 된 회귀 모델 구축에 대해 자세히 알고 싶다면 중급 튜토리얼을 진행해 볼 것을 권장드립니다.

다음 [중급 회귀 작업 모델 만들기] 튜토리얼에서는 정확도 향상을 위한 "__OPTIONS__"에 대해 더욱 심도있게 다뤄보겠습니다. 중급, 고급 단계를 마치고 나만의 서비스/프로덕트를 위한 회귀 예측 모델을 만들어 보세요. 중급 단계에서는 ThanoSQL의 Auto-ML이 제공하는 다양한 "__OPTIONS__"를 활용하여 정교한 회귀 예측 모델을 만들어 볼 예정입니다. 또한, 중급 단계를 마치신 이후 고급 단계에서는 비정형 데이터를 수치화시킨 후 Auto-ML의 학습 요소로 포함하여 회귀 예측 모델을 만들 수 있습니다.

* [나만의 데이터 업로드하기](/how-to_guides/ThanoSQL_connecting/data_upload/)
* [중급 이미지 분류 모델 만들기]
* [이미지 변환과 Auto-ML을 이용한 나만의 모델 만들기]
* [나만의 이미지 분류 모델 배포하기](/how-to_guides/ThanoSQL_connecting/thanosql_api/rest_api_thanosql_query/)

!!! tip "__나만의 서비스를 위한 모델 배포 관련 문의__"
    ThanoSQL을 활용해 나만의 모델을 만들거나, 나의 서비스에 적용하는데 어려움이 있다면 언제든 아래로 문의주세요😊

    회귀 모델 구축 관련 문의: contact@smartmind.team
