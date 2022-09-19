---
title: Auto-ML을 사용하여 분류 모델 만들기
---

# __Auto-ML을 사용하여 분류 모델 만들기__

## 시작 전 사전정보

- 튜토리얼 난이도 : ★☆☆☆☆
- 읽는데 걸리는 시간 : 4 분
- 사용 언어 : [SQL](https://ko.wikipedia.org/wiki/SQL) (100%)
- 실행 파일 위치 : tutorial/thanosql_ml/classification/automl_classification.ipynb 
- 참고 문서 : [(캐글) Titanic - Machine Learning from Disaster](https://www.kaggle.com/competitions/titanic/overview)

## 튜토리얼 소개

<div class="admonition note">
    <h4 class="admonition-title">분류 작업 이해하기</h4>
    <p>분류 작업은 목푯값(Target)이 속한 범주(Category 또는 Class)를 예측하기 위해 사용하는 <a href="https://ko.wikipedia.org/wiki/%EA%B8%B0%EA%B3%84_%ED%95%99%EC%8A%B5">머신러닝(기계학습/Machine Learning)</a>의 한 형태입니다. 예를 들어, 남성 또는 여성을 분류하는 이진 분류와 동물의 종(개, 고양이, 토끼 등)을 예측하는 다중 분류 모두 분류 작업에 포함됩니다. <br></p>
</div>

기업의 특정 마케팅 프로모션에 대해 잠재고객이 긍정적인 반응을 보일 것인지 아닌지를 예측하기 위해서는 고객의 [CRM(Customer Relationship Management)](https://ko.wikipedia.org/wiki/%EA%B3%A0%EA%B0%9D_%EA%B4%80%EA%B3%84_%EA%B4%80%EB%A6%AC) 데이터(인구 통계학 정보, 고객의 행동/검색 데이터 등)를 이용할 수 있습니다. 이 경우 CRM 데이터에서 표현되는 <a href="https://ko.wikipedia.org/wiki/%ED%8A%B9%EC%A7%95_(%EA%B8%B0%EA%B3%84_%ED%95%99%EC%8A%B5)">특성(Feature)</a>이 입력 데이터로 사용되며, 예측하고자 하는 값인 목푯값은 프로모션에 대한 대상 고객의 반응이 긍정(1 또는 True) 또는 부정(0 또는 False)일지 여부입니다. 이러한 분류 모델을 이용해서, 마케팅에 노출되지 않은 고객들의 프로모션에 대한 반응을 미리 예측하고 적절한 고객에게 마케팅을 노출함으로써 마케팅 효율을 지속적으로 높일 수 있습니다. 

__아래는 ThanoSQL 분류 모델의 활용 및 예시입니다.__  

- 분류 모델은 현재 사용자의 이탈에 대한 조기 탐지를 가능하게 하고 문제(이탈)에 대해 사전에 대응할 수 있도록 해줍니다. 과거 데이터를 통해 이탈 고객의 특성을 파악할 수 있게 되고 이탈 가능성이 높은 사용자를 사전에 발견함으로써 적절한 조치가 가능해집니다. 이를 통해 고객이탈 방지 및 매출 증대 효과를 기대할 수 있습니다.

- 온라인 플랫폼 내에서 사용자의 [세그먼트](https://ko.wikipedia.org/wiki/%EC%8B%9C%EC%9E%A5%EC%84%B8%EB%B6%84%ED%99%94)를 예측할 수 있습니다. 대부분의 서비스 사용자들은 서로 다른 특성을 가지고 다양한 행동방식(User Behaviour)과 니즈(Needs)를 가지고 있습니다. 분류 예측 모델은 서비스 사용자의 특성을 이용하여 세분화된 집단을 식별하고 그들에게 맞춤화된 전략 수립을 가능하게 합니다.  

<div class="admonition note">
    <h4 class="admonition-title">본 튜토리얼에서는</h4>
    <p>👉 대표적인 머신러닝 경진대회 플랫폼인 <a href="https://www.kaggle.com/">캐글</a>의 입문자를 위한 <mark style="background-color:#FFD79C"> <strong>Titanic: Machine Learning from Disaster</strong></mark> 데이터 세트를 사용하여 생존자 예측 분류 모델을 만듭니다. 이 대회의 목표는 아래와 같습니다. 
    (참고로, 해당 대회의 데이터는 1912년 4월 15일 실제 타이타닉 사건 때, 탑승했었던 승객들 명단입니다.)</p>
</div>

__타이타닉에서 살아남을 수 있는 승객을 예측하기__

ThanoSQL에서는 자동화된 머신러닝(__Auto-ML__) 도구를 제공합니다. 본 튜토리얼에서는 Auto-ML을 사용하여 타이타닉에서 살아남을 수 있는 승객을 예측합니다. ThanoSQL에서 제공하는 Auto-ML은 모델(Model)개발을 위한 프로세스를 자동화하고, 데이터 과학(Data Science)에 대한 전문지식이 없어도 데이터의 수집 및 저장, 머신러닝 모델의 개발 및 배포(엔드투엔드 머신러닝 파이프라인)를 하나의 언어(ThanoSQL)만으로 가능하도록 지원합니다.

__자동화된 ML을 사용하면 다음과 같은 장점이 있습니다__ 

1. 광범위한 프로그래밍 또는 데이터 과학에 대한 지식이 없어도 머신러닝 솔루션의 구현 및 배포가 가능  
2. 개발모델의 배포에 들어가는 시간 및 리소스를 절약   
3. 의사결정을 위해 보유하고 있는 데이터를 이용한 신속한 문제해결이 가능  

이제부터 ThanoSQL을 사용하여 간단하게 타이타닉에서 살아남을 수 있는 승객을 예측하는 분류 모델을 만들어 봅니다.

## __0. 데이터 세트 준비__

ThanoSQL의 쿼리 구문을 사용하기 위해서는 [ThanoSQL 워크스페이스](https://docs.thanosql.ai/getting_started/how_to_use_ThanoSQL/#5-thanosql)
에서 언급된 것처럼 API 토큰을 생성하고 아래의 쿼리를 실행해야 합니다. 


```python
%load_ext thanosql
%thanosql API_TOKEN=<발급받은_API_TOKEN>
```

### __데이터 세트 준비__


```python
%%thanosql
GET THANOSQL DATASET titanic_data
OPTIONS (overwrite=True)
```

    Success


<div class="admonition note">
    <h4 class="admonition-title">쿼리 세부 정보</h4>
    <ul>
        <li>"<strong>GET THANOSQL DATASET</strong>" 쿼리 구문을 사용하여 원하는 데이터 세트를 워크스페이스에 저장합니다. </li>
        <li>"<strong>OPTIONS</strong>" 쿼리 구문을 통해 <strong>GET THANOSQL DATASET</strong> 에 사용할 옵션을 지정합니다.
        <ul>
            <li>"overwrite" : 동일 이름의 데이터 세트가 존재하는 경우 덮어쓰기 가능 유무 설정. True일 경우 기존 데이터 세트는 새로운 데이터 세트로 변경됨 (True|False, DEFAULT : False) </li>
        </ul>
        </li>
    </ul>
</div>


```python
%%thanosql
COPY titanic_train 
OPTIONS (overwrite=True)
FROM "thanosql-dataset/titanic_data/titanic_train.csv"
```

    Success



```python
%%thanosql
COPY titanic_test 
OPTIONS (overwrite=True)
FROM "thanosql-dataset/titanic_data/titanic_test.csv"
```

    Success


<div class="admonition note">
    <h4 class="admonition-title">쿼리 세부 정보</h4>
    <ul>
        <li>"<strong>COPY</strong>" 쿼리 구문을 사용하여 DB에 복사 할 데이터 세트명을 지정합니다. </li>
        <li>"<strong>OPTIONS</strong>" 쿼리 구문을 통해 <strong>COPY</strong> 에 사용할 옵션을 지정합니다.
        <ul>
            <li>"overwrite" : 동일 이름의 데이터 세트가 DB상에 존재하는 경우 덮어쓰기 가능 유무 설정. True일 경우 기존 데이터 세트는 새로운 데이터 세트로 변경됨 (True|False, DEFAULT : False) </li>
        </ul>
        </li>
    </ul>
</div>

## __1. 데이터 세트 확인__

생존자 예측 분류 모델을 만들기 위해 우리는 ThanoSQL DB에 저장되어 있는 <mark style="background-color:#FFEC92 "><strong>titanic_train</strong></mark> 테이블을 사용합니다. 아래의 쿼리문을 실행하면서 테이블 내용을 확인합니다.


```python
%%thanosql
SELECT * 
FROM titanic_train
LIMIT 5 
```




<div class="df_size">
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>passengerid</th>
      <th>survived</th>
      <th>pclass</th>
      <th>name</th>
      <th>sex</th>
      <th>age</th>
      <th>sibsp</th>
      <th>parch</th>
      <th>ticket</th>
      <th>fare</th>
      <th>cabin</th>
      <th>embarked</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>0</td>
      <td>3</td>
      <td>Braund, Mr. Owen Harris</td>
      <td>male</td>
      <td>22</td>
      <td>1</td>
      <td>0</td>
      <td>A/5 21171</td>
      <td>7.2500</td>
      <td>None</td>
      <td>S</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>1</td>
      <td>1</td>
      <td>Cumings, Mrs. John Bradley (Florence Briggs Th...</td>
      <td>female</td>
      <td>38</td>
      <td>1</td>
      <td>0</td>
      <td>PC 17599</td>
      <td>71.2833</td>
      <td>C85</td>
      <td>C</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>1</td>
      <td>3</td>
      <td>Heikkinen, Miss. Laina</td>
      <td>female</td>
      <td>26</td>
      <td>0</td>
      <td>0</td>
      <td>STON/O2. 3101282</td>
      <td>7.9250</td>
      <td>None</td>
      <td>S</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>1</td>
      <td>1</td>
      <td>Futrelle, Mrs. Jacques Heath (Lily May Peel)</td>
      <td>female</td>
      <td>35</td>
      <td>1</td>
      <td>0</td>
      <td>113803</td>
      <td>53.1000</td>
      <td>C123</td>
      <td>S</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>0</td>
      <td>3</td>
      <td>Allen, Mr. William Henry</td>
      <td>male</td>
      <td>35</td>
      <td>0</td>
      <td>0</td>
      <td>373450</td>
      <td>8.0500</td>
      <td>None</td>
      <td>S</td>
    </tr>
  </tbody>
</table>
</div>



<div class="admonition note">
    <h4 class="admonition-title">데이터 이해하기</h4>
    <p><mark style="background-color:#FFEC92 "><strong>tianic_train</strong></mark> 데이터 세트는 다음과 같은 정보를 담고 있습니다.</p>
    <ul>
        <li><mark style="background-color:#D7D0FF">passengerid</mark> : 탑승승객 아이디</li>
        <li><mark style="background-color:#D7D0FF">survived</mark> : 탑승승객  생존 여부</li>
        <li><mark style="background-color:#D7D0FF">pclass</mark> : 탑승승객 티켓 등급</li>
        <li><mark style="background-color:#D7D0FF">name</mark> : 탑승승객 이름</li>
        <li><mark style="background-color:#D7D0FF">sex</mark> : 탑승승객 성별</li>
        <li><mark style="background-color:#D7D0FF">age</mark> : 탑승승객 나이</li>
        <li><mark style="background-color:#D7D0FF">sibsp</mark> : 탑승한 형제 자매 또는 배우자 수</li>
        <li><mark style="background-color:#D7D0FF">parch</mark> : 탑승한 부모 또는 자녀의 수</li>
        <li><mark style="background-color:#D7D0FF">ticket</mark> : 티켓 번호</li>
        <li><mark style="background-color:#D7D0FF">fare</mark> : 요금</li>
        <li><mark style="background-color:#D7D0FF">cabin</mark> : 선실</li>
        <li><mark style="background-color:#D7D0FF">embarked</mark> : 승선지 또는 항구</li>
    </ul>
</div>
 
이번 튜토리얼에서는 추가적인 쿼리문을 이용한 데이터 전처리가 필요한
<mark style="background-color:#D7D0FF">name</mark>, <mark style="background-color:#D7D0FF">ticket</mark>, <mark style="background-color:#D7D0FF">cabin</mark>컬럼을 제외하고 모델 학습을 진행하겠습니다.

## __2. 분류 모델 생성__

이전 단계에서 확인한 <mark style="background-color:#FFEC92 ">titanic_train</mark> 데이터를 사용하여 생존자 예측 분류 모델을 만듭니다. 아래의 쿼리 구문을 실행시켜 <mark style="background-color:#E9D7FD ">titanic_automl_classification</mark> 이름의 모델을 만들어 봅니다.  
(쿼리 실행 시 예상 소요 시간: 8 min)


```python
%%thanosql
BUILD MODEL titanic_automl_classification
USING AutomlClassifier 
OPTIONS (
    target='survived', 
    impute_type='iterative',  
    features_to_drop=['name', 'ticket', 'passengerid', 'cabin'],
    time_left_for_this_task=300,
    overwrite=True
    ) 
AS 
SELECT * 
FROM titanic_train
```

    Building model...
    Success


<div class="admonition note">
    <h4 class="admonition-title">쿼리 세부 정보</h4>
    <ul>
        <li>"<strong>BUILD MODEL</strong>" 쿼리 구문을 사용하여 <mark style="background-color:#E9D7FD ">titanic_automl_classification</mark>이라는 모델을 만들고 학습시킵니다.</li>
        <li>"<strong>OPTIONS</strong>" 쿼리 구문을 통해 모델 생성에 사용할 옵션을 지정합니다.
        <ul>
            <li>"target" : 분류 모델의 목푯값이 담겨 있는 컬럼명</li>
            <li>"impute_type" : 데이터 테이블의 빈 값(NaN)을 처리하는 방법 설정 ('simple'|'iterative' , DEFAULT : 'simple')        </li>
            <li>"features_to_drop" : 데이터 테이블에서 학습에 이용하지 못하는 컬럼명 리스트</li>
            <li>"time_left_for_this_task" : 적합한 분류 예측 모델을 찾는데 소요되는 시간 (DEFAULT : 300)</li>
            <li>"overwrite" : 동일 이름의 모델이 존재하는 경우 덮어쓰기 가능 유무 설정. True일 경우 기존 모델은 새로운 모델로 변경됨 (True|False, DEFAULT : False) </li>
        </ul>
        </li>
    </ul>
</div>

## __3. 생성된 모델 평가__

아래의 쿼리문을 실행하여 이전 단계에서 만든 예측 모델의 성능을 평가합니다. 


```python
%%thanosql 
EVALUATE USING titanic_automl_classification 
OPTIONS (
    target = 'survived'
    )
AS
SELECT *
FROM titanic_train
```




<div class="df_size">
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>metrics</th>
      <th>value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>accuracy</td>
      <td>0.886644</td>
    </tr>
    <tr>
      <th>1</th>
      <td>ROCAUC</td>
      <td>0.888667</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Recall</td>
      <td>0.895082</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Precision</td>
      <td>0.798246</td>
    </tr>
    <tr>
      <th>4</th>
      <td>f1-score</td>
      <td>0.843895</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Kappa</td>
      <td>0.755364</td>
    </tr>
    <tr>
      <th>6</th>
      <td>MCC</td>
      <td>0.758416</td>
    </tr>
  </tbody>
</table>
</div>



<div class="admonition note">
    <h4 class="admonition-title">쿼리 세부 정보</h4>
    <ul>
        <li>"<strong>EVALUATE USING</strong>" 쿼리 구문을 사용하여 구축한  <mark style="background-color:#E9D7FD ">titanic_automl_classification</mark>이라는 모델을 평가합니다. </li>
        <li>"<strong>OPTIONS</strong>" 쿼리 구문을 통해 모델 평가에 사용할 옵션을 지정합니다.
        <ul>
            <li>"target" : 분류 예측 모델에 목푯값이 되는 컬럼의 이름</li>
        </ul>
        </li>
    </ul>
</div>

<div class="admonition warning">
    <h4 class="admonition-title">평가용 데이터 세트</h4>
    <p>평가용 데이터 세트는 학습 데이터 세트의 일부를 분리하여 학습에 사용되지 않아야 하나 튜토리얼에서는 편의상 학습 데이터를 사용합니다</p>
</div>

## __4. 생성된 모델을 사용하여 생존자 예측__

이전 단계에서 생성한 생존자 예측 모델을 사용해 탑승 승객 정보에 따른 생존 여부를 예측해 봅니다. 테스트용 데이터 세트(학습에 이용되지 않은 데이터 테이블, <mark style="background-color:#FFEC92 "><strong>titanic_test</strong></mark>)를 사용합니다.


```python
%%thanosql 
PREDICT USING titanic_automl_classification
AS 
SELECT * 
FROM titanic_test
```




<div class="df_size">
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>passengerid</th>
      <th>pclass</th>
      <th>name</th>
      <th>sex</th>
      <th>age</th>
      <th>sibsp</th>
      <th>parch</th>
      <th>ticket</th>
      <th>fare</th>
      <th>cabin</th>
      <th>embarked</th>
      <th>predicted</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>892</td>
      <td>3</td>
      <td>Kelly, Mr. James</td>
      <td>male</td>
      <td>34.5</td>
      <td>0</td>
      <td>0</td>
      <td>330911</td>
      <td>7.8292</td>
      <td>None</td>
      <td>Q</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>893</td>
      <td>3</td>
      <td>Wilkes, Mrs. James (Ellen Needs)</td>
      <td>female</td>
      <td>47.0</td>
      <td>1</td>
      <td>0</td>
      <td>363272</td>
      <td>7.0000</td>
      <td>None</td>
      <td>S</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>894</td>
      <td>2</td>
      <td>Myles, Mr. Thomas Francis</td>
      <td>male</td>
      <td>62.0</td>
      <td>0</td>
      <td>0</td>
      <td>240276</td>
      <td>9.6875</td>
      <td>None</td>
      <td>Q</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>895</td>
      <td>3</td>
      <td>Wirz, Mr. Albert</td>
      <td>male</td>
      <td>27.0</td>
      <td>0</td>
      <td>0</td>
      <td>315154</td>
      <td>8.6625</td>
      <td>None</td>
      <td>S</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>896</td>
      <td>3</td>
      <td>Hirvonen, Mrs. Alexander (Helga E Lindqvist)</td>
      <td>female</td>
      <td>22.0</td>
      <td>1</td>
      <td>1</td>
      <td>3101298</td>
      <td>12.2875</td>
      <td>None</td>
      <td>S</td>
      <td>0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>413</th>
      <td>1305</td>
      <td>3</td>
      <td>Spector, Mr. Woolf</td>
      <td>male</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>A.5. 3236</td>
      <td>8.0500</td>
      <td>None</td>
      <td>S</td>
      <td>0</td>
    </tr>
    <tr>
      <th>414</th>
      <td>1306</td>
      <td>1</td>
      <td>Oliva y Ocana, Dona. Fermina</td>
      <td>female</td>
      <td>39.0</td>
      <td>0</td>
      <td>0</td>
      <td>PC 17758</td>
      <td>108.9000</td>
      <td>C105</td>
      <td>C</td>
      <td>1</td>
    </tr>
    <tr>
      <th>415</th>
      <td>1307</td>
      <td>3</td>
      <td>Saether, Mr. Simon Sivertsen</td>
      <td>male</td>
      <td>38.5</td>
      <td>0</td>
      <td>0</td>
      <td>SOTON/O.Q. 3101262</td>
      <td>7.2500</td>
      <td>None</td>
      <td>S</td>
      <td>0</td>
    </tr>
    <tr>
      <th>416</th>
      <td>1308</td>
      <td>3</td>
      <td>Ware, Mr. Frederick</td>
      <td>male</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>359309</td>
      <td>8.0500</td>
      <td>None</td>
      <td>S</td>
      <td>0</td>
    </tr>
    <tr>
      <th>417</th>
      <td>1309</td>
      <td>3</td>
      <td>Peter, Master. Michael J</td>
      <td>male</td>
      <td>NaN</td>
      <td>1</td>
      <td>1</td>
      <td>2668</td>
      <td>22.3583</td>
      <td>None</td>
      <td>C</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
<p>418 rows × 12 columns</p>
</div>



<div class="admonition note">
    <h4 class="admonition-title">쿼리 세부 정보</h4>
    <p>"<strong>PREDICT USING</strong>" 쿼리 구문을 사용하여 이전 단계에서 만든 <mark style="background-color:#E9D7FD ">titanic_automl_classification</mark> 모델을 예측에 사용합니다. "<strong>PREDICT</strong>"의 경우 생성된 모델의 절차를 따르기 때문에 특별한 옵션이 필요없습니다. </p>
</div>

## __5. 튜토리얼을 마치며__

이번 튜토리얼에서는 [캐글](https://www.kaggle.com/)의 <mark style="background-color:#FFD79C"> <strong>Titanic: Machine Learning from Disaster</strong></mark> 데이터를 사용하여 타이타닉 생존자 분류 예측 모델을 만들어 보았습니다. 초급 단계 튜토리얼인만큼 정확도 향상을 위한 과정보다는 전반적인 프로세스 위주의 설명으로 진행 하였습니다. 향상 된 분류 모델 구축에 대해 자세히 알고 싶다면 중급 튜토리얼을 진행해 볼 것을 권장드립니다.

다음 [중급 분류 예측 모델 만들기] 튜토리얼에서는 정확도 향상을 위한 "__OPTIONS__"에 대해 더욱 심도있게 다뤄보겠습니다. 중급, 고급 단계를 마치고 나만의 서비스/프로덕트를 위한 분류 예측 모델을 만들어 보세요. 중급 단계에서는 ThanoSQL의 AutoML이 제공하는 다양한 "__OPTIONS__"를 활용하여 정교한 분류 예측 모델을 만들어 볼 예정입니다. 또한, 중급 단계를 마치신 이후 고급 단계에서는 비정형 데이터를 수치화시킨 후 AutoML의 학습 요소로 포함하여 분류 예측 모델을 만들 수 있습니다.

* [나만의 데이터 업로드하기](https://docs.thanosql.ai/getting_started/data_upload/)
* [중급 이미지 분류 모델 만들기]
* [이미지 변환과 Auto-ML을 이용한 나만의 모델 만들기]
* [나만의 이미지 분류 모델 배포하기](https://docs.thanosql.ai/how-to_guides/reference/)

<div class="admonition tip">
    <h4 class="admonition-title">나만의 서비스를 위한 모델 배포 관련 문의</h4>
    <p>ThanoSQL을 활용해 나만의 모델을 만들거나, 나의 서비스에 적용하는데 어려움이 있다면 언제든 아래로 문의주세요😊</p>
    <p>분류 모델 구축 관련 문의: contact@smartmind.team</p>
</div>
