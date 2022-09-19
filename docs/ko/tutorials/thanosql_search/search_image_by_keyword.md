---
title: 키워드로 이미지 검색하기
---

# __키워드로 이미지 검색하기__ 

## 시작 전 사전 정보 

- 튜토리얼 난이도 : ★☆☆☆☆
- 읽는데 걸리는 시간 : 10분  
- 사용 언어 : [SQL](https://ko.wikipedia.org/wiki/SQL) (100%)
- 실행 파일 위치 : tutorial/thanosql_search/search_image_by_keyword.ipynb  
- 참고 문서 : [음식 이미지 및 영양정보 텍스트 소개 데이터 세트](https://aihub.or.kr/aihubdata/data/view.do?currMenu=115&topMenu=100&aihubDataSe=realm&dataSetSn=74)

## 튜토리얼 소개 

<div class="admonition note">
    <h4 class="admonition-title">키워드-이미지 검색 서비스 이해하기</h4>
    <p>ThanoSQL에서는 키워드를 통해 이미지를 결과로 돌려받을 수 있는 검색 기능을 제공합니다. 이미지 분류 모델 등을 이용해서 사용자가 원하는 키워드를 목푯값(Target)으로 설정하고, 학습 된 모델을 사용해서 업데이트 되는 이미지를 색인한 컬럼을 추가합니다. 텍스트-이미지 검색 기능이 이미지로부터 의미를 분석하고 유사한 이미지를 제공한다면 키워드-이미지 검색은 원하는 목푯값(범주)에 해당하는 이미지를 찾아줍니다. </p>
</div>

검색은 "책이나 컴퓨터에서 목적에 따라 필요한 자료들을 찾아내는 일"이라는 사전적인 의미를 가지고 있습니다. ThanoSQL 키워드-이미지 검색은 특정 단어(키워드)의 포함 여부로 DB 내의 정보를 검색하는 방식과는 조금 다르게 작동합니다. 키워드 기반 이미지 검색은 이미지의 특징으로부터 단어를 미리 학습하고 예측하는 모델을 만들고, 해당 모델을 통해 특정 키워드에 포함 될 확률이 가장 높은 이미지를 제공합니다. 

**아래는 ThanoSQL 키워드 이미지 검색 알고리즘의 활용 및 예시 입니다.**

- 쇼핑 카테고리의 범주 등을 학습 데이터로 활용하여 학습모델을 만들고 이를 이용해서 기존/신규 이미지에 색인 컬럼을 만듭니다. 해당 색인 컬럼은 이미지 등록일자 등과 같은 수치형 특성들과 결합해서 더욱 정교한 검색을 제공합니다.  
- 이어지는 튜토리얼에서 다루는 유사 이미지 검색 결과나 텍스트-이미지 검색 결과 등 다양한 결과를 같이 활용해서 나만의 이미지 검색 서비스를 만들 수 있습니다.


<div class="admonition note">
    <h4 class="admonition-title">본 튜토리얼에서는</h4>
    <p>👉 ThanoSQL에서 제공하는 비정형 데이터 검색 구문인 "<strong>SEARCH</strong>" 쿼리 구문과 기존 PostgreSQL에서 제공하는 정형 데이터 검색 구문인 "<strong>SELECT</strong>" 쿼리 구문을 같이 사용하여 특정 키워드를 이용해 원하는 이미지 검색을 진행합니다. </p>
</div>


<div class="admonition tip">
    <h4 class="admonition-title">데이터 세트 설명</h4>
    <p><code>음식 이미지 및 영양정보 텍스트 소개</code> 데이터 세트는 과학기술정보통신부가 주관하고 한국지능정보사회진흥원이 지원하는 '인공지능 학습용 데이터 구축사업'으로 구축된 데이터로 한국인 다빈도 섭취 외식 메뉴와 한식메뉴 400종을 선정하여 양질의 이미지 데이터로 구성이 되어 있습니다. 842,000 장의 이미지로 구성되어 있으며, 본 튜토리얼에서는 해당 데이터 세트에서 일부(10종, 1,190장)의 사진만을 사용합니다. </p>
</div>

## __0. 데이터 세트 준비__

ThanoSQL의 쿼리 구문을 사용하기 위해서는 [ThanoSQL 워크스페이스](https://docs.thanosql.ai/getting_started/how_to_use_ThanoSQL/#5-thanosql)에서 언급된 것처럼 API 토큰을 생성하고 아래의 쿼리를 실행해야 합니다.


```python
%load_ext thanosql
%thanosql API_TOKEN=<발급받은_API_TOKEN>
```

### __데이터 세트 준비__


```python
%%thanosql
GET THANOSQL DATASET diet_data
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
COPY diet 
OPTIONS (overwrite=True)
FROM "thanosql-dataset/diet_data/diet.csv"
```

    Success


<div class="admonition note">
    <h4 class="admonition-title">쿼리 세부 정보</h4>
    <ul>
        <li>"<strong>COPY</strong>" 쿼리 구문을 사용하여 DB에 저장 할 데이터 세트명을 지정합니다. </li>
        <li>"<strong>OPTIONS</strong>" 쿼리 구문을 통해 <strong>COPY</strong> 에 사용할 옵션을 지정합니다.
        <ul>
            <li>"overwrite" : 동일 이름의 데이터 세트가 DB상에 존재하는 경우 덮어쓰기 가능 유무 설정. True일 경우 기존 데이터 세트는 새로운 데이터 세트로 변경됨 (True|False, DEFAULT : False) </li>
        </ul>
        </li>
    </ul>
</div>

## __1. 데이터 세트 확인__

키워드-이미지 검색 모델을 만들기 위해 ThanoSQL DB에 저장되어 있는 <mark style="background-color:#FFEC92">diet</mark> 테이블을 사용합니다. 아래의 쿼리 구문을 실행하고 테이블의 내용을 확인합니다.


```python
%%thanosql
SELECT * 
FROM diet
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
      <th>image_path</th>
      <th>label</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>thanosql-dataset/diet_data/diet/백향과/0_A220148X...</td>
      <td>백향과</td>
    </tr>
    <tr>
      <th>1</th>
      <td>thanosql-dataset/diet_data/diet/백향과/0_A220148X...</td>
      <td>백향과</td>
    </tr>
    <tr>
      <th>2</th>
      <td>thanosql-dataset/diet_data/diet/백향과/1_A220148X...</td>
      <td>백향과</td>
    </tr>
    <tr>
      <th>3</th>
      <td>thanosql-dataset/diet_data/diet/백향과/0_A220148X...</td>
      <td>백향과</td>
    </tr>
    <tr>
      <th>4</th>
      <td>thanosql-dataset/diet_data/diet/백향과/0_A220148X...</td>
      <td>백향과</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>1185</th>
      <td>thanosql-dataset/diet_data/diet/사과파이/0_A020511...</td>
      <td>사과파이</td>
    </tr>
    <tr>
      <th>1186</th>
      <td>thanosql-dataset/diet_data/diet/사과파이/0_A020511...</td>
      <td>사과파이</td>
    </tr>
    <tr>
      <th>1187</th>
      <td>thanosql-dataset/diet_data/diet/사과파이/1_A020511...</td>
      <td>사과파이</td>
    </tr>
    <tr>
      <th>1188</th>
      <td>thanosql-dataset/diet_data/diet/사과파이/0_A020511...</td>
      <td>사과파이</td>
    </tr>
    <tr>
      <th>1189</th>
      <td>thanosql-dataset/diet_data/diet/사과파이/0_A020511...</td>
      <td>사과파이</td>
    </tr>
  </tbody>
</table>
<p>1190 rows × 2 columns</p>
</div>



<div class="admonition note">
    <h4 class="admonition-title">데이터 테이블 이해하기</h4>
    <p><mark style="background-color:#FFEC92">diet</mark> 테이블은 아래와 같은 정보를 담고 있습니다.   </p>
    <ul>
        <li><mark style="background-color:#D7D0FF">image_path</mark> : 이미지 경로 </li>
        <li><mark style="background-color:#D7D0FF">label</mark> : 파일 이름</li>
    </ul>
</div>

## __2. 키워드 검색 모델 생성__ 

이미지 검색을 위해서는 기존 데이터 테이블을 학습하여 추후 검색의 기준을 만들어줘야 합니다. 이를 위해서 이전 단계에서 확인한 데이터 세트를 사용하여 이미지 분류 모델을 만듭니다. 아래의 쿼리 구문을 실행하여  <mark style="background-color:#E9D7FD ">diet_image_classification</mark>이라는 이름의 모델을 만듭니다.  
(쿼리 실행 시 예상 소요 시간: 3 min)


```python
%%thanosql
BUILD MODEL diet_image_classification
USING ConvNeXt_Tiny
OPTIONS (
    image_col='image_path', 
    label_col='label', 
    epochs=1,
    overwrite=True
    )
AS 
SELECT *
FROM diet
```

    Building model...
    Success


<div class="admonition note">
    <h4 class="admonition-title">쿼리 세부 정보</h4>
    <ul>
        <li>"<strong>BUILD MODEL</strong>" 쿼리 구문을 사용하여 <mark style="background-color:#E9D7FD ">diet_image_classification</mark> 모델을 만들고 학습시킵니다.</li>
        <li>"<strong>USING</strong>" 쿼리 구문을 통해 베이스 모델로 <code>ConvNeXt_Tiny</code>를 사용할 것을 명시합니다.</li>
        <li>"<strong>OPTIONS</strong>" 쿼리 구문을 통해 모델 생성에 사용할 옵션을 지정합니다.
        <ul>
            <li>"image_col" : 이미지 경로를 담은 컬럼의 이름</li>
            <li>"label_col" : 목푯값의 정보를 담은 컬럼의 이름</li>
            <li>"epochs" : 모든 학습 데이터 세트를 학습하는 횟수</li>
            <li>"overwrite" : 동일 이름의 모델이 존재하는 경우 덮어쓰기 가능 유무 설정. True일 경우 기존 모델은 새로운 모델로 변경됨 (True|False, DEFAULT : False)</li>
        </ul>
        </li>
    </ul>
</div>

## __3. 생성된 모델을 사용하여 키워드-이미지 검색 모델 확인__

이전 단계에서 만든 이미지 예측 모델(<mark style="background-color:#E9D7FD ">diet_image_classification</mark>)을 사용해서 특정 이미지의 목푯값을 예측해 봅니다. 아래 쿼리를 수행하고 나면, 예측 결과는 <mark style="background-color:#D7D0FF">predicted</mark> 컬럼에 저장되어 반환됩니다.


```python
%%thanosql
PREDICT USING diet_image_classification
AS 
SELECT *
FROM diet
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
      <th>image_path</th>
      <th>predicted</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>thanosql-dataset/diet_data/diet/백향과/0_A220148X...</td>
      <td>백향과</td>
    </tr>
    <tr>
      <th>1</th>
      <td>thanosql-dataset/diet_data/diet/백향과/0_A220148X...</td>
      <td>백향과</td>
    </tr>
    <tr>
      <th>2</th>
      <td>thanosql-dataset/diet_data/diet/백향과/1_A220148X...</td>
      <td>백향과</td>
    </tr>
    <tr>
      <th>3</th>
      <td>thanosql-dataset/diet_data/diet/백향과/0_A220148X...</td>
      <td>백향과</td>
    </tr>
    <tr>
      <th>4</th>
      <td>thanosql-dataset/diet_data/diet/백향과/0_A220148X...</td>
      <td>백향과</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>1185</th>
      <td>thanosql-dataset/diet_data/diet/사과파이/0_A020511...</td>
      <td>사과파이</td>
    </tr>
    <tr>
      <th>1186</th>
      <td>thanosql-dataset/diet_data/diet/사과파이/0_A020511...</td>
      <td>빵</td>
    </tr>
    <tr>
      <th>1187</th>
      <td>thanosql-dataset/diet_data/diet/사과파이/1_A020511...</td>
      <td>사과파이</td>
    </tr>
    <tr>
      <th>1188</th>
      <td>thanosql-dataset/diet_data/diet/사과파이/0_A020511...</td>
      <td>보쌈</td>
    </tr>
    <tr>
      <th>1189</th>
      <td>thanosql-dataset/diet_data/diet/사과파이/0_A020511...</td>
      <td>사과파이</td>
    </tr>
  </tbody>
</table>
<p>1190 rows × 2 columns</p>
</div>



<div class="admonition note">
    <h4 class="admonition-title">쿼리 세부 정보</h4>
    <ul>
        <li>"<strong>PREDICT USING</strong>" 쿼리 구문을 통해 이전 단계에서 만든 <mark style="background-color:#E9D7FD ">diet_image_classification</mark> 모델을 예측에 사용합니다.</li>
    </ul>
</div>

## __4. 생성된 모델을 이용한 검색__ 

이제 "__PREDICT USING__", "__SELECT__", "__WHERE__" 쿼리 구문을 사용하여 특정 조건의 데이터만을 검색합니다. <mark style="background-color:#E9D7FD ">label</mark>이 '사과파이'이고, 예측 결과 또한 '사과파이'인 데이터만을 검색하고 다음처럼 쿼리 구문을 작성할 수 있습니다.


```python
%%thanosql
SELECT A.image_path, A.label, B.predicted 
FROM diet A
LEFT JOIN (
    SELECT * 
    FROM (PREDICT USING diet_image_classification 
    AS SELECT * FROM diet)) B 
ON A.image_path = B.image_path
WHERE A.label = B.predicted
AND A.label LIKE '사과파이'
LIMIT 10
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
      <th>image_path</th>
      <th>label</th>
      <th>predicted</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>thanosql-dataset/diet_data/diet/사과파이/0_A020511...</td>
      <td>사과파이</td>
      <td>사과파이</td>
    </tr>
    <tr>
      <th>1</th>
      <td>thanosql-dataset/diet_data/diet/사과파이/0_A020511...</td>
      <td>사과파이</td>
      <td>사과파이</td>
    </tr>
    <tr>
      <th>2</th>
      <td>thanosql-dataset/diet_data/diet/사과파이/0_A020511...</td>
      <td>사과파이</td>
      <td>사과파이</td>
    </tr>
    <tr>
      <th>3</th>
      <td>thanosql-dataset/diet_data/diet/사과파이/0_A020511...</td>
      <td>사과파이</td>
      <td>사과파이</td>
    </tr>
    <tr>
      <th>4</th>
      <td>thanosql-dataset/diet_data/diet/사과파이/0_A020511...</td>
      <td>사과파이</td>
      <td>사과파이</td>
    </tr>
    <tr>
      <th>5</th>
      <td>thanosql-dataset/diet_data/diet/사과파이/0_A020511...</td>
      <td>사과파이</td>
      <td>사과파이</td>
    </tr>
    <tr>
      <th>6</th>
      <td>thanosql-dataset/diet_data/diet/사과파이/0_A020511...</td>
      <td>사과파이</td>
      <td>사과파이</td>
    </tr>
    <tr>
      <th>7</th>
      <td>thanosql-dataset/diet_data/diet/사과파이/0_A020511...</td>
      <td>사과파이</td>
      <td>사과파이</td>
    </tr>
    <tr>
      <th>8</th>
      <td>thanosql-dataset/diet_data/diet/사과파이/0_A020511...</td>
      <td>사과파이</td>
      <td>사과파이</td>
    </tr>
    <tr>
      <th>9</th>
      <td>thanosql-dataset/diet_data/diet/사과파이/0_A020511...</td>
      <td>사과파이</td>
      <td>사과파이</td>
    </tr>
  </tbody>
</table>
</div>



<div class="admonition note">
    <h4 class="admonition-title">쿼리 세부 정보</h4>
    <ul>
        <li>"<strong>SELECT * FROM (...)</strong>" 쿼리 구문을 통해  "<strong>PREDICT USING</strong>"으로 시작하는 쿼리 구문의 결과를 모두 선택합니다.</li>
        <li>"<strong>WHERE</strong>" 쿼리 구문을 통해 조건을 설정합니다. 이 조건은 "<strong>AND</strong>"를 통해 이어집니다.
        <ul>
            <li>"label = predicted" : <mark style="background-color:#D7D0FF ">label</mark> 컬럼과 <mark style="background-color:#D7D0FF ">predicted</mark> 컬럼의 값이 같은 데이터만 추출합니다.</li>
            <li>"label = '사과파이'" : <mark style="background-color:#D7D0FF ">label</mark> 컬럼이 '사과파이'인 데이터만 추출합니다.</li>
        </ul>
        </li>
    </ul>
</div>

## __5. 튜토리얼을 마치며__

이번 튜토리얼에서는 `음식 이미지 데이터세트`를 사용해 키워드와 관련된 음식 이미지를 검색하는 모델을 구축하고 활용까지 해보았습니다. 이번 튜토리얼에서는 정확도 보다는 작동위주로 빠르게 진행되었습니다. 빌드 옵션의 학습 횟수, 데이터 수등 다양한 옵션들을 조절하여 모델의 정확도를 향상시킬 수 있습니다. 이어지는 유사이미지 검색모델, 텍스트 설명을 이용한 이미지 검색 튜토리얼을 진행하고 나만의 다양한 검색 서비스를 만들어 보세요.

<div class="admonition tip">
    <h4 class="admonition-title">나만의 서비스를 위한 모델 배포 관련 문의</h4>
    <p>ThanoSQL을 활용해 나만의 모델을 만들거나, 서비스에 적용하는데 어려움이 있다면 언제든 아래로 문의주세요😊</p>
    <p>키워드-이미지 검색 모델 구축 관련 문의: contact@smartmind.team</p>
</div>
