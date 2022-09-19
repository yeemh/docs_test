---
title: 텍스트로 이미지 검색하기
---

# __텍스트로 이미지 검색하기__ 

## 시작 전 사전 정보

- 튜토리얼 난이도 : ★★☆☆☆
- 읽는데 걸리는 시간 : 7분
- 사용 언어 : [SQL](https://ko.wikipedia.org/wiki/SQL) (100%)
- 실행 파일 위치 : tutorial/thanosql_search/search_image_by_text.ipynb  
- 참고 문서 : [Unsplash Dataset - Lite](https://unsplash.com/data), [Learning Transferable Visual Models From Natural Language Supervision](https://arxiv.org/abs/2103.00020)

## 튜토리얼 소개

<div class="admonition note">
    <h4 class="admonition-title">텍스트 수치화 기술 이해하기</h4>
    <p>자연어를 컴퓨터가 이해하려면 자연어를 수치화 해야 합니다. 최근 <a href="https://en.wikipedia.org/wiki/BERT_(language_model)">BERT</a>나 <a href="https://en.wikipedia.org/wiki/GPT-3">GPT-3</a>와 같은 사전학습 모델에 대한 연구가 활발히 이루어지고 있으며, 주목할 만한 성과를 보여주고 있습니다. 이러한 모델들은 <a href="https://en.wikipedia.org/wiki/Self-supervised_learning">자가 학습(Self-Supervised Learning)</a>을 기반으로 각 문장들의 의미를 파악하고 유사한 의미를 갖는 각 문장들을 가깝게 위치하도록 저차원 공간에 수치화하여 표현합니다. 문장 간의 순서를 무작위로 섞거나 일부 단어를 마스킹하는 방식 등을 이용해 각 문장/문맥의 참/거짓 여부를 판단함으로써 라벨링 작업이 없어도 학습이 가능하도록 지원합니다.</p>
</div>

텍스트와 이미지 같이 다른 형태의 입력 자료를 함께 다루는 문제를 멀티 모달(Multi-modal)이라고 합니다. "**CLIP: Connecting Text and Image**"은 대표적인 멀티 모달 모델로 수치화 된 저차원 공간에 대한 이해를 다루고 있습니다. 기존 모델이 이미지 자체의 <a href="https://ko.wikipedia.org/wiki/%ED%8A%B9%EC%A7%95_(%EA%B8%B0%EA%B3%84_%ED%95%99%EC%8A%B5)">특징(Feature)</a>만을 학습 했다면, 멀티 모달 모델에서는 이미지와 텍스트를 모두 입력 자료로 사용하면서 해당 이미지를 설명하는 텍스트에 대한 특징까지 동시에 학습할 수 있습니다. 또한, 텍스트와 이미지가 저차원 공간에 함께 위치함으로써 텍스트와 이미지 사이의 유사도를 판단할 수 있게 되며, 이를 응용하면 검색 알고리즘으로 사용할 수 있습니다.

ThanoSQL은 인공지능 알고리즘을 이용해서 데이터 세트를 수치화 합니다. 이렇게 수치화 된 데이터는 DB의 컬럼 내에 저장되고, 입력받은 텍스트의 수치화 결과와 유사도 계산을 통해 비슷한 이미지를 검색하는데 사용됩니다.

__아래는 ThanoSQL 텍스트-이미지 검색 알고리즘의 활용 및 예시 입니다.__

- 사용자가 보유하고 있는 이미지나 동영상에서 원하는 장면을 텍스트로 묘사하고 이와 가장 유사한 이미지를 검색합니다. 사용자가 검색하는 상품에 대한 키워드가 아닌 텍스트 기반의 설명을 듣고 가장 유사한 상품 이미지를 노출합니다.
- 유튜브 영상 등에서 내가 원하는 광고를 넣고 싶은 시간을 검색합니다. 여행 광고를 넣기 위해서 산이나 캠핑 장면 등이 나오는 장면을 손쉽게 검색하고 광고를 삽입합니다. 

<div class="admonition note">
    <h4 class="admonition-title">본 튜토리얼에서는</h4>
    <p>👉 Unsplash는 20만 명 이상의 사진가들이 참여한 이미지들을 AI를 위한 데이터 세트로 무료로 공개했습니다. <code>Unsplash Dataset - Lite</code>는 25,000 장의 자연을 테마로한 이미지로 구성되어 있으며, 25,000 개의 키워드를 함께 제공합니다. </p>
</div>

이번 튜토리얼에서는 텍스트-이미지 검색 모델을 사용하여, ThanoSQL DB의 `Unsplash Dataset - Lite` 데이터 세트의 25,000 장의 이미지 중에서 텍스트로 원하는 이미지를 검색해 봅니다.

## __0. 데이터 세트 및 모델 준비__

ThanoSQL의 쿼리 구문을 사용하기 위해서는 [ThanoSQL 워크스페이스](https://docs.thanosql.ai/getting_started/how_to_use_ThanoSQL/#5-thanosql)
에서 언급된 것처럼 API 토큰을 생성하고 아래의 쿼리를 실행해야 합니다.


```python
%load_ext thanosql
%thanosql API_TOKEN=<발급받은_API_TOKEN>
```

### __데이터 세트 준비__


```python
%%thanosql
GET THANOSQL DATASET unsplash_data
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
COPY unsplash_data 
OPTIONS (overwrite=True)
FROM 'thanosql-dataset/unsplash_data/unsplash.csv'
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

### __모델 준비__


```python
%%thanosql
GET THANOSQL MODEL tutorial_search_clip
OPTIONS (overwrite=True)
AS tutorial_search_clip
```

    Success


<div class="admonition note">
    <h4 class="admonition-title">쿼리 세부 정보</h4>
    <ul>
        <li>"<strong>GET THANOSQL MODEL</strong>" 쿼리 구문을 사용하여 원하는 모델을 워크스페이스 및 DB에 저장합니다. </li>
        <li>"<strong>OPTIONS</strong>" 쿼리 구문을 통해 <strong>GET THANOSQL MODEL</strong> 에 사용할 옵션을 지정합니다.
        <ul>
            <li>"overwrite" : 동일 이름의 데이터 세트가 존재하는 경우 덮어쓰기 가능 유무 설정. True일 경우 기존 데이터 세트는 새로운 데이터 세트로 변경됨 (True|False, DEFAULT : False) </li>
        </ul>
        </li>
        <li>"<strong>AS</strong>" 쿼리 구문을 사용하여 해당 모델의 이름을 지정합니다. AS 구문을 사용하지 않을 경우 <code>THANOSQL MODEL</code>의 이름을 그대로 사용합니다. </li>
    </ul>
</div>

## __1. 데이터 세트 확인__

텍스트-이미지 검색 모델을 만들기 위해 우리는 ThanoSQL DB에 저장되어 있는 `unsplash_data` 테이블을 사용합니다. 아래의 쿼리문을 실행하고 테이블의 내용을 확인합니다.


```python
%%thanosql
SELECT photo_id, image_path, photo_image_url, photo_description, ai_description
FROM unsplash_data
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
      <th>photo_id</th>
      <th>image_path</th>
      <th>photo_image_url</th>
      <th>photo_description</th>
      <th>ai_description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>XMyPniM9LF0</td>
      <td>thanosql-dataset/unsplash_data/XMyPniM9LF0.jpg</td>
      <td>https://images.unsplash.com/uploads/1411949294...</td>
      <td>Woman exploring a forest</td>
      <td>woman walking in the middle of forest</td>
    </tr>
    <tr>
      <th>1</th>
      <td>rDLBArZUl1c</td>
      <td>thanosql-dataset/unsplash_data/rDLBArZUl1c.jpg</td>
      <td>https://images.unsplash.com/photo-141633941111...</td>
      <td>Succulents in a terrarium</td>
      <td>succulent plants in clear glass terrarium</td>
    </tr>
    <tr>
      <th>2</th>
      <td>cNDGZ2sQ3Bo</td>
      <td>thanosql-dataset/unsplash_data/cNDGZ2sQ3Bo.jpg</td>
      <td>https://images.unsplash.com/photo-142014251503...</td>
      <td>Rural winter mountainside</td>
      <td>rocky mountain under gray sky at daytime</td>
    </tr>
    <tr>
      <th>3</th>
      <td>iuZ_D1eoq9k</td>
      <td>thanosql-dataset/unsplash_data/iuZ_D1eoq9k.jpg</td>
      <td>https://images.unsplash.com/photo-141487280988...</td>
      <td>Poppy seeds and flowers</td>
      <td>red common poppy flower selective focus phography</td>
    </tr>
    <tr>
      <th>4</th>
      <td>BeD3vjQ8SI0</td>
      <td>thanosql-dataset/unsplash_data/BeD3vjQ8SI0.jpg</td>
      <td>https://images.unsplash.com/photo-141700759404...</td>
      <td>Silhouette near dark trees</td>
      <td>trees during night time</td>
    </tr>
  </tbody>
</table>
</div>



<div class="admonition note">
    <h4 class="admonition-title">데이터 이해하기</h4>
    <ul>
        <li><code>photo_id</code> 이미지의 고유 id 컬럼 명</li>
        <li><code>image_path</code> 이미지가 위치한 경로의 컬럼 명</li>
        <li><code>photo_image_url</code> 웹사이트 unsplash에서의 원본 이미지 주소를 나타내는 컬럼 명</li>
        <li><code>photo_description</code> 해당 이미지에 대해 사람이 작성한 짧은 설명을 나타내는 컬럼 명</li>
        <li><code>ai_description</code> AI가 생성해낸 해당 이미지에 대한 설명을 나타내는 컬럼 명</li>
    </ul>
</div>


```python
%%thanosql
PRINT IMAGE 
AS
SELECT image_path 
FROM unsplash_data 
LIMIT 5
```

    /home/jovyan/thanosql-dataset/unsplash_data/XMyPniM9LF0.jpg



    
![jpeg](/img/tutorials/thanosql_search/search_image_by_text/output_16_1.jpg)
    


    /home/jovyan/thanosql-dataset/unsplash_data/rDLBArZUl1c.jpg



    
![jpeg](/img/tutorials/thanosql_search/search_image_by_text/output_16_3.jpg)
    


    /home/jovyan/thanosql-dataset/unsplash_data/cNDGZ2sQ3Bo.jpg



    
![jpeg](/img/tutorials/thanosql_search/search_image_by_text/output_16_5.jpg)
    


    /home/jovyan/thanosql-dataset/unsplash_data/iuZ_D1eoq9k.jpg



    
![jpeg](/img/tutorials/thanosql_search/search_image_by_text/output_16_7.jpg)
    


    /home/jovyan/thanosql-dataset/unsplash_data/BeD3vjQ8SI0.jpg



    
![jpeg](/img/tutorials/thanosql_search/search_image_by_text/output_16_9.jpg)
    


## __2. 텍스트 검색을 위한 이미지 수치화 모델 생성하기__

<div class="admonition danger">
    <h4 class="admonition-title">참고 사항</h4>
    <p>텍스트-이미지 검색 알고리즘은 학습에 오랜 시간이 걸리고 총 4억 개의 데이터 세트로 사전 학습된 모델을 사용하기 때문에 "<strong>BUILD MODEL</strong>" 쿼리 구문을 이용한 학습 과정을 본 튜토리얼에서는 생략합니다. <code>tutorial_search_clip</code> 모델은 베이스 알고리즘으로 <code>clipen</code>을 사용한 사전학습 된 모델을 가져와서 사용하게 됩니다. "<strong>CONVERT USING</strong>" 쿼리 구문을 실행하게 되면 "모델명(<code>tutorial_search_clip</code>)_베이스 알고리즘명(<code>clipen</code>)"으로 이미지가 수치화 된 컬럼이 자동으로 생성이 되며, "<strong>SEARCH IMAGE</strong>" 쿼리 구문을 실행하게 되면 "모델명(<code>tutorial_search_clip</code>)_베이스 알고리즘 명(<code>clipen</code>)_similarity수(1)"로 이미지 유사도 컬럼이 자동으로 생성 됩니다. 여기서 "수"는 검색에 사용한 텍스트의 갯수를 의미합니다. 2개 이상의 텍스트로 검색이 이루어 질 경우 순서에 따라 컬럼의 수가 순차적으로 증가되어 생성 됩니다. 자세한 사항은 아래 내용을 참고하세요.</p>
</div>
(쿼리 실행 시 예상 소요 시간: 3 min)  

<p>다음 "<strong>CONVERT USING</strong>" 쿼리 구문을 실행하여 <code>unsplash_data</code> 이미지들을 수치화 합니다. 수치화된 결과값은 새로 생긴 <mark style="background-color:#D7D0FF ">tutorial_search_clip_clipen</mark> 컬럼에 저장됩니다. (결과 컬럼명은 {model_name}_{base_model_name}으로 추가됩니다) </p>


```python
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
      <th>photo_id</th>
      <th>image_path</th>
      <th>photo_image_url</th>
      <th>photo_description</th>
      <th>ai_description</th>
      <th>tutorial_search_clip_clipen</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>XMyPniM9LF0</td>
      <td>thanosql-dataset/unsplash_data/XMyPniM9LF0.jpg</td>
      <td>https://images.unsplash.com/uploads/1411949294...</td>
      <td>Woman exploring a forest</td>
      <td>woman walking in the middle of forest</td>
      <td>[-0.0148640582, 0.0549194068, 0.0118315415, 0....</td>
    </tr>
    <tr>
      <th>1</th>
      <td>rDLBArZUl1c</td>
      <td>thanosql-dataset/unsplash_data/rDLBArZUl1c.jpg</td>
      <td>https://images.unsplash.com/photo-141633941111...</td>
      <td>Succulents in a terrarium</td>
      <td>succulent plants in clear glass terrarium</td>
      <td>[-0.0345519036, 0.0314463899, -0.0065574604, 0...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>cNDGZ2sQ3Bo</td>
      <td>thanosql-dataset/unsplash_data/cNDGZ2sQ3Bo.jpg</td>
      <td>https://images.unsplash.com/photo-142014251503...</td>
      <td>Rural winter mountainside</td>
      <td>rocky mountain under gray sky at daytime</td>
      <td>[-0.031663157000000004, 0.0538793579, 0.013499...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>iuZ_D1eoq9k</td>
      <td>thanosql-dataset/unsplash_data/iuZ_D1eoq9k.jpg</td>
      <td>https://images.unsplash.com/photo-141487280988...</td>
      <td>Poppy seeds and flowers</td>
      <td>red common poppy flower selective focus phography</td>
      <td>[0.0018179789000000001, 0.009972040500000001, ...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>BeD3vjQ8SI0</td>
      <td>thanosql-dataset/unsplash_data/BeD3vjQ8SI0.jpg</td>
      <td>https://images.unsplash.com/photo-141700759404...</td>
      <td>Silhouette near dark trees</td>
      <td>trees during night time</td>
      <td>[-0.0223454703, 0.0129929734, -0.0019434979, -...</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>24963</th>
      <td>c7OrOMxrurA</td>
      <td>thanosql-dataset/unsplash_data/c7OrOMxrurA.jpg</td>
      <td>https://images.unsplash.com/photo-159300793778...</td>
      <td>None</td>
      <td>black metal fence during daytime</td>
      <td>[-0.0114668589, -0.0021708228, -0.0104938177, ...</td>
    </tr>
    <tr>
      <th>24964</th>
      <td>15IuQ5a0Qwg</td>
      <td>thanosql-dataset/unsplash_data/15IuQ5a0Qwg.jpg</td>
      <td>https://images.unsplash.com/photo-159296761254...</td>
      <td>Pearl earrings and seashells</td>
      <td>white and brown seashell on white surface</td>
      <td>[-0.0289349593, 0.0516048148, 0.0157920811, -0...</td>
    </tr>
    <tr>
      <th>24965</th>
      <td>w8nrcXz8pwk</td>
      <td>thanosql-dataset/unsplash_data/w8nrcXz8pwk.jpg</td>
      <td>https://images.unsplash.com/photo-159299937329...</td>
      <td>None</td>
      <td>leopard on brown tree trunk during daytime</td>
      <td>[0.006948946, -0.032078824900000004, -0.013961...</td>
    </tr>
    <tr>
      <th>24966</th>
      <td>n1jHrRhehUI</td>
      <td>thanosql-dataset/unsplash_data/n1jHrRhehUI.jpg</td>
      <td>https://images.unsplash.com/photo-159192792878...</td>
      <td>Floral truck in the streets of Rome</td>
      <td>woman in beige coat and white hat standing on ...</td>
      <td>[0.0052709519, -0.0013724031000000002, 0.02522...</td>
    </tr>
    <tr>
      <th>24967</th>
      <td>Ic74ACoaAX0</td>
      <td>thanosql-dataset/unsplash_data/Ic74ACoaAX0.jpg</td>
      <td>https://images.unsplash.com/photo-159240763188...</td>
      <td>None</td>
      <td>green plants on brown rocky mountain under blu...</td>
      <td>[-0.0143270288, 0.0594773069, 0.0045367181, -0...</td>
    </tr>
  </tbody>
</table>
<p>24968 rows × 6 columns</p>
</div>



<div class="admonition note">
    <h4 class="admonition-title">쿼리 세부 정보</h4>
    <ul>
        <li>"<strong>CONVERT USING</strong>" 쿼리 구문은 <code>tutorial_search_clip</code> 모델을 이미지 수치화를 위한 알고리즘으로 사용합니다.  </li>
        <li>"<strong>OPTIONS</strong>" 쿼리 구문은 이미지 수치화 시 필요한 변수들을 정의합니다.
        <ul>
            <li>"table_name" : ThanoSQL DB 내에 저장될 테이블 이름</li>
            <li>"image_col" : 이미지 경로를 담고 있는 컬럼 명</li>
            <li>"batch_size" : 한번의 학습에서 읽는 데이터 세트 묶음의 크기. 논문에 따르면 클 수록 학습 성능이 증가하지만 메모리의 크기를 고려하여 128을 사용합니다. (DEFAULT : 16)  </li>
        </ul>
        </li>
    </ul>
</div>


```python
%%thanosql
SELECT *
FROM unsplash_data
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
      <th>photo_id</th>
      <th>image_path</th>
      <th>photo_image_url</th>
      <th>photo_description</th>
      <th>ai_description</th>
      <th>tutorial_search_clip_clipen</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>XMyPniM9LF0</td>
      <td>thanosql-dataset/unsplash_data/XMyPniM9LF0.jpg</td>
      <td>https://images.unsplash.com/uploads/1411949294...</td>
      <td>Woman exploring a forest</td>
      <td>woman walking in the middle of forest</td>
      <td>[-0.0148640582, 0.0549194068, 0.0118315415, 0....</td>
    </tr>
    <tr>
      <th>1</th>
      <td>rDLBArZUl1c</td>
      <td>thanosql-dataset/unsplash_data/rDLBArZUl1c.jpg</td>
      <td>https://images.unsplash.com/photo-141633941111...</td>
      <td>Succulents in a terrarium</td>
      <td>succulent plants in clear glass terrarium</td>
      <td>[-0.0345519036, 0.0314463899, -0.0065574604, 0...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>cNDGZ2sQ3Bo</td>
      <td>thanosql-dataset/unsplash_data/cNDGZ2sQ3Bo.jpg</td>
      <td>https://images.unsplash.com/photo-142014251503...</td>
      <td>Rural winter mountainside</td>
      <td>rocky mountain under gray sky at daytime</td>
      <td>[-0.031663157000000004, 0.0538793579, 0.013499...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>iuZ_D1eoq9k</td>
      <td>thanosql-dataset/unsplash_data/iuZ_D1eoq9k.jpg</td>
      <td>https://images.unsplash.com/photo-141487280988...</td>
      <td>Poppy seeds and flowers</td>
      <td>red common poppy flower selective focus phography</td>
      <td>[0.0018179789000000001, 0.009972040500000001, ...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>BeD3vjQ8SI0</td>
      <td>thanosql-dataset/unsplash_data/BeD3vjQ8SI0.jpg</td>
      <td>https://images.unsplash.com/photo-141700759404...</td>
      <td>Silhouette near dark trees</td>
      <td>trees during night time</td>
      <td>[-0.0223454703, 0.0129929734, -0.0019434979, -...</td>
    </tr>
  </tbody>
</table>
</div>



## __3. 텍스트로 이미지 검색하기__

"__SEARCH IMAGE__"  쿼리 구문과 생성한 `tutorial_search_clip` 모델을 사용하여 텍스트 기반 이미지 검색을
 할 수 있습니다. 다음 쿼리 구문을 실행하여 "a black cat" 이라는 텍스트와 임베딩 된 `unsplash_data` 
이미지들의 유사도를 계산합니다. 결괏값은 새로 추가된 <mark style="background-color:#D7D0FF ">tutorial_search_clip_clipen_similarity1</mark> 컬럼에 
저장됩니다.


```python
%%thanosql
SEARCH IMAGE text="a black cat"
USING tutorial_search_clip
AS 
SELECT * 
FROM unsplash_data
```

    Searching...





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
      <th>photo_id</th>
      <th>image_path</th>
      <th>photo_image_url</th>
      <th>photo_description</th>
      <th>ai_description</th>
      <th>tutorial_search_clip_clipen</th>
      <th>tutorial_search_clip_clipen_similarity1</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>XMyPniM9LF0</td>
      <td>thanosql-dataset/unsplash_data/XMyPniM9LF0.jpg</td>
      <td>https://images.unsplash.com/uploads/1411949294...</td>
      <td>Woman exploring a forest</td>
      <td>woman walking in the middle of forest</td>
      <td>[-0.0148640582, 0.0549194068, 0.0118315415, 0....</td>
      <td>0.185725</td>
    </tr>
    <tr>
      <th>1</th>
      <td>rDLBArZUl1c</td>
      <td>thanosql-dataset/unsplash_data/rDLBArZUl1c.jpg</td>
      <td>https://images.unsplash.com/photo-141633941111...</td>
      <td>Succulents in a terrarium</td>
      <td>succulent plants in clear glass terrarium</td>
      <td>[-0.0345519036, 0.0314463899, -0.0065574604, 0...</td>
      <td>0.148399</td>
    </tr>
    <tr>
      <th>2</th>
      <td>cNDGZ2sQ3Bo</td>
      <td>thanosql-dataset/unsplash_data/cNDGZ2sQ3Bo.jpg</td>
      <td>https://images.unsplash.com/photo-142014251503...</td>
      <td>Rural winter mountainside</td>
      <td>rocky mountain under gray sky at daytime</td>
      <td>[-0.031663157000000004, 0.0538793579, 0.013499...</td>
      <td>0.187703</td>
    </tr>
    <tr>
      <th>3</th>
      <td>iuZ_D1eoq9k</td>
      <td>thanosql-dataset/unsplash_data/iuZ_D1eoq9k.jpg</td>
      <td>https://images.unsplash.com/photo-141487280988...</td>
      <td>Poppy seeds and flowers</td>
      <td>red common poppy flower selective focus phography</td>
      <td>[0.0018179789000000001, 0.009972040500000001, ...</td>
      <td>0.177512</td>
    </tr>
    <tr>
      <th>4</th>
      <td>BeD3vjQ8SI0</td>
      <td>thanosql-dataset/unsplash_data/BeD3vjQ8SI0.jpg</td>
      <td>https://images.unsplash.com/photo-141700759404...</td>
      <td>Silhouette near dark trees</td>
      <td>trees during night time</td>
      <td>[-0.0223454703, 0.0129929734, -0.0019434979, -...</td>
      <td>0.218824</td>
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
    </tr>
    <tr>
      <th>24963</th>
      <td>c7OrOMxrurA</td>
      <td>thanosql-dataset/unsplash_data/c7OrOMxrurA.jpg</td>
      <td>https://images.unsplash.com/photo-159300793778...</td>
      <td>None</td>
      <td>black metal fence during daytime</td>
      <td>[-0.0114668589, -0.0021708228, -0.0104938177, ...</td>
      <td>0.226402</td>
    </tr>
    <tr>
      <th>24964</th>
      <td>15IuQ5a0Qwg</td>
      <td>thanosql-dataset/unsplash_data/15IuQ5a0Qwg.jpg</td>
      <td>https://images.unsplash.com/photo-159296761254...</td>
      <td>Pearl earrings and seashells</td>
      <td>white and brown seashell on white surface</td>
      <td>[-0.0289349593, 0.0516048148, 0.0157920811, -0...</td>
      <td>0.147114</td>
    </tr>
    <tr>
      <th>24965</th>
      <td>w8nrcXz8pwk</td>
      <td>thanosql-dataset/unsplash_data/w8nrcXz8pwk.jpg</td>
      <td>https://images.unsplash.com/photo-159299937329...</td>
      <td>None</td>
      <td>leopard on brown tree trunk during daytime</td>
      <td>[0.006948946, -0.032078824900000004, -0.013961...</td>
      <td>0.227299</td>
    </tr>
    <tr>
      <th>24966</th>
      <td>n1jHrRhehUI</td>
      <td>thanosql-dataset/unsplash_data/n1jHrRhehUI.jpg</td>
      <td>https://images.unsplash.com/photo-159192792878...</td>
      <td>Floral truck in the streets of Rome</td>
      <td>woman in beige coat and white hat standing on ...</td>
      <td>[0.0052709519, -0.0013724031000000002, 0.02522...</td>
      <td>0.169803</td>
    </tr>
    <tr>
      <th>24967</th>
      <td>Ic74ACoaAX0</td>
      <td>thanosql-dataset/unsplash_data/Ic74ACoaAX0.jpg</td>
      <td>https://images.unsplash.com/photo-159240763188...</td>
      <td>None</td>
      <td>green plants on brown rocky mountain under blu...</td>
      <td>[-0.0143270288, 0.0594773069, 0.0045367181, -0...</td>
      <td>0.152199</td>
    </tr>
  </tbody>
</table>
<p>24968 rows × 7 columns</p>
</div>



<div class="admonition note">
    <h4 class="admonition-title">쿼리 세부 정보</h4>
    <ul>
        <li>"<strong>SEARCH IMAGE</strong>" 쿼리 구문을 사용하여 이미지를 찾을 것임을 명시합니다. "text" 변수를 이용해서 찾고자 하는 이미지의 텍스트 내용을 입력합니다. </li>
        <li>"<strong>USING</strong>" 쿼리 구문을 통해 검색에 사용할 모델로 <code>tutorial_search_clip</code>을 사용할 것을 명시합니다.</li>
    </ul>
</div>

아래 쿼리 구문을 실행하여 'a black cat' 텍스트와 가장 유사한 이미지 5개의 유사도를 확인합니다.


```python
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

    Searching...





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
      <th>tutorial_search_clip_clipen_similarity1</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>thanosql-dataset/unsplash_data/UMyfDjQ6Ep8.jpg</td>
      <td>0.316560</td>
    </tr>
    <tr>
      <th>1</th>
      <td>thanosql-dataset/unsplash_data/7XJ3d0xK444.jpg</td>
      <td>0.311931</td>
    </tr>
    <tr>
      <th>2</th>
      <td>thanosql-dataset/unsplash_data/m8HsSWh-y6E.jpg</td>
      <td>0.310819</td>
    </tr>
    <tr>
      <th>3</th>
      <td>thanosql-dataset/unsplash_data/6ST6S6i9IGM.jpg</td>
      <td>0.310214</td>
    </tr>
    <tr>
      <th>4</th>
      <td>thanosql-dataset/unsplash_data/aFyD5aWKu6k.jpg</td>
      <td>0.309158</td>
    </tr>
  </tbody>
</table>
</div>



<div class="admonition note">
    <h4 class="admonition-title">쿼리 세부 정보</h4>
    <ul>
        <li>"<strong>SEARCH IMAGE</strong>" 쿼리 구문은 입력한 텍스트와 이미지 사이의 유사도를 계산하여 반환합니다.</li>
        <li>첫 번째 "<strong>SELECT</strong>" 쿼리 구문은 괄호 안의 쿼리 결과에서 <mark style="background-color:#D7D0FF ">image_path</mark> 컬럼과 <mark style="background-color:#D7D0FF ">tutorial_search_clip_clipen_similarity1</mark> 컬럼을 선택합니다.</li>
        <li>"<strong>ORDER BY</strong>" 쿼리 구문은 결과를 <mark style="background-color:#D7D0FF ">tutorial_search_clip_clipen_similarity1</mark> 컬럼의 값을 기준으로 정렬하는데, 정렬은 내림차순("<strong>DESC</strong>")이며, 그 중 상위 5개("<strong>LIMIT</strong>" 5)의 결과를 출력합니다.</li>
    </ul>
</div>

이전 쿼리 구문을 "__PRINT__"문과 함께 응용하여, 결과 이미지를 바로 확인할 수 있습니다.


```python
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

    Searching...
    /home/jovyan/thanosql-dataset/unsplash_data/UMyfDjQ6Ep8.jpg



    
![jpeg](/img/tutorials/thanosql_search/search_image_by_text/output_29_1.jpg)
    


    /home/jovyan/thanosql-dataset/unsplash_data/7XJ3d0xK444.jpg



    
![jpeg](/img/tutorials/thanosql_search/search_image_by_text/output_29_3.jpg)
    


    /home/jovyan/thanosql-dataset/unsplash_data/m8HsSWh-y6E.jpg



    
![jpeg](/img/tutorials/thanosql_search/search_image_by_text/output_29_5.jpg)
    


    /home/jovyan/thanosql-dataset/unsplash_data/6ST6S6i9IGM.jpg



    
![jpeg](/img/tutorials/thanosql_search/search_image_by_text/output_29_7.jpg)
    


    /home/jovyan/thanosql-dataset/unsplash_data/aFyD5aWKu6k.jpg



    
![jpeg](/img/tutorials/thanosql_search/search_image_by_text/output_29_9.jpg)
    


<div class="admonition note">
    <h4 class="admonition-title">쿼리 세부 정보</h4>
    <p>이 쿼리는 위의 쿼리와 합쳐 세 단계로 구성됩니다.</p>
    <ul>
        <li>첫 번째 괄호 안의 "<strong>SELECT</strong>" 쿼리 구문을 통해 바로 위 단계의 결과를 생성합니다.</li>
        <li>"<strong>PRINT IMAGE</strong>" 쿼리 구문을 사용하여 해당 이미지를 출력합니다.</li>
    </ul>
</div>


```python
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

    Searching...
    /home/jovyan/thanosql-dataset/unsplash_data/jZUr3AuI8io.jpg



    
![jpeg](/img/tutorials/thanosql_search/search_image_by_text/output_31_1.jpg)
    


    /home/jovyan/thanosql-dataset/unsplash_data/nnKBZTWzlMQ.jpg



    
![jpeg](/img/tutorials/thanosql_search/search_image_by_text/output_31_3.jpg)
    


    /home/jovyan/thanosql-dataset/unsplash_data/HG2KpOO0vSc.jpg



    
![jpeg](/img/tutorials/thanosql_search/search_image_by_text/output_31_5.jpg)
    


    /home/jovyan/thanosql-dataset/unsplash_data/f6qFneRNwNI.jpg



    
![jpeg](/img/tutorials/thanosql_search/search_image_by_text/output_31_7.jpg)
    


    /home/jovyan/thanosql-dataset/unsplash_data/GKY4WDO3QgY.jpg



    
![jpeg](/img/tutorials/thanosql_search/search_image_by_text/output_31_9.jpg)
    



```python
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

    Searching...
    /home/jovyan/thanosql-dataset/unsplash_data/Xo4vJrtrmmA.jpg



    
![jpeg](/img/tutorials/thanosql_search/search_image_by_text/output_32_1.jpg)
    


    /home/jovyan/thanosql-dataset/unsplash_data/QheWOfwEUio.jpg



    
![jpeg](/img/tutorials/thanosql_search/search_image_by_text/output_32_3.jpg)
    


    /home/jovyan/thanosql-dataset/unsplash_data/_zHYUQmWrzk.jpg



    
![jpeg](/img/tutorials/thanosql_search/search_image_by_text/output_32_5.jpg)
    


    /home/jovyan/thanosql-dataset/unsplash_data/Tu_lH5CFFZw.jpg



    
![jpeg](/img/tutorials/thanosql_search/search_image_by_text/output_32_7.jpg)
    


    /home/jovyan/thanosql-dataset/unsplash_data/DfYPBHaOR04.jpg



    
![jpeg](/img/tutorials/thanosql_search/search_image_by_text/output_32_9.jpg)
    



```python
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

    Searching...
    /home/jovyan/thanosql-dataset/unsplash_data/nDLYtRqJtMw.jpg



    
![jpeg](/img/tutorials/thanosql_search/search_image_by_text/output_33_1.jpg)
    


    /home/jovyan/thanosql-dataset/unsplash_data/qNJpGSCv_Jc.jpg



    
![jpeg](/img/tutorials/thanosql_search/search_image_by_text/output_33_3.jpg)
    


    /home/jovyan/thanosql-dataset/unsplash_data/Yb5OBk-OxJY.jpg



    
![jpeg](/img/tutorials/thanosql_search/search_image_by_text/output_33_5.jpg)
    


    /home/jovyan/thanosql-dataset/unsplash_data/6etH6346MHE.jpg



    
![jpeg](/img/tutorials/thanosql_search/search_image_by_text/output_33_7.jpg)
    


    /home/jovyan/thanosql-dataset/unsplash_data/7GX5aICb5i4.jpg



    
![jpeg](/img/tutorials/thanosql_search/search_image_by_text/output_33_9.jpg)
    


## __4. 튜토리얼을 마치며__

이번 튜토리얼에서는 멀티 모달 텍스트/이미지 수치화 모델을 사용하여 `unsplash 데이터 세트`에서 텍스트를 통한 이미지 검색을 해보았습니다. 초급 단계의 튜토리얼인 만큼 간단한 쿼리를 통해 눈에 보이는 결과를 얻는 것 위주로 진행했습니다. 이미지 검색을 조금 더 다채로운 쿼리와 함께 사용한다면, 보다 원하는 결과에 가까운 값을 얻을 수 있을 것입니다.

<div class="admonition tip">
    <h4 class="admonition-title">나만의 서비스를 위한 모델 배포 관련 문의</h4>
    <p>ThanoSQL을 활용해 나만의 모델을 만들거나, 나의 서비스에 적용하는데 어려움이 있다면 언제든 아래로 문의주세요😊</p>
    <p>텍스트-이미지 검색 모델 구축 관련 문의: contact@smartmind.team</p>
</div>
