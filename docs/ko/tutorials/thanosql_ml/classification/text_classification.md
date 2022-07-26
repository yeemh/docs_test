---
title: 텍스트 분류 모델 만들기
---

# __텍스트 분류 모델 만들기__

## 시작 전 사전 정보

- 튜토리얼 난이도: ★☆☆☆☆
- 읽는데 걸리는 시간 : 10분
- 사용 언어 : [SQL](https://ko.wikipedia.org/wiki/SQL) (100%)
- 실행 파일 위치 : tutorial/thanosql_ml/classification/text_classification.ipynb  
- 참고 문서 : [(캐글) IMDB Movie Reviews](https://www.kaggle.com/code/lakshmi25npathi/sentiment-analysis-of-imdb-movie-reviews/data), [ELECTRA: Pre-training Text Encoders as Discriminators Rather Than Generators](https://arxiv.org/abs/2003.10555)

## 튜토리얼 소개

<div class="admonition note">
    <h4 class="admonition-title">분류 작업 이해하기</h4>
    <p>분류 작업은 목푯값(Target)이 속한 범주(Category 또는 Class)를 예측하기 위해 사용하는 <a href="https://ko.wikipedia.org/wiki/%EA%B8%B0%EA%B3%84_%ED%95%99%EC%8A%B5">머신러닝(기계학습/Machine Learning)</a>의 한 형태입니다. 예를 들어, 남성 또는 여성을 분류하는 이진 분류와 동물의 종(개, 고양이, 토끼 등)을 예측하는 다중 분류 모두 분류 작업에 포함됩니다.</p>
</div>

[자연어 처리(NLP, Natural Language Processing)](https://ko.wikipedia.org/wiki/%EC%9E%90%EC%97%B0%EC%96%B4_%EC%B2%98%EB%A6%AC)는 인공지능의 한 분야로서 머신러닝을 사용하여 텍스트 기반 데이터를 처리하고 해석합니다.

<div class="admonition tip">
    <h4 class="admonition-title"><a href="https://ko.wikipedia.org/wiki/%EC%9E%90%EC%97%B0%EC%96%B4_%EC%B2%98%EB%A6%AC">자연어 처리(NLP, Natural Language Processing)</a>란</h4>
    <p>NLP는 작업의 목적에 따라 자연어 이해(NLU, Natural Language Understanding)와 자연어 생성(NLG, Natural Language Generation)으로 분류할 수 있습니다. 자연어 이해는 사람이 이해하는 자연어를 컴퓨터가 이해할 수 있는 값으로 바꾸는 처리를 의미합니다. 반면에 자연어 생성은 더 나아가 컴퓨터가 이해할 수 있는 값을 사람이 이해하도록 자연어로 바꾸는 과정을 의미합니다.</p>
</div>

 최근 <a href="https://en.wikipedia.org/wiki/BERT_(language_model)">BERT</a>, [GPT-3](https://en.wikipedia.org/wiki/GPT-3) 등 사전 훈련 기술의 발전으로 많은 양의 목푯값(Target)이 없는 텍스트를 활용하여 감정 분석이나 질의 응답과 같은 특정 NLP 작업에 대해 미세 조정하기 전에 언어 이해의 일반적인 모델 구축을 가능하게 합니다.

<div class="admonition summary">
    <p>즉, 대량의 데이터 세트에 대한 <a href="https://en.wikipedia.org/wiki/Labeled_data">데이터 라벨링</a> 작업을 최소화 함으로써 더욱 많은 데이터를 효율적으로 활용할 수 있습니다.</p>
</div>

ThanoSQL에서는 다양한 사전 훈련 된 인공지능 모델을 제공하고 있으며, 소량의 데이터 라벨링을 통해서도 쉽게 사용자만의 텍스트 분류 모델을 만들 수 있도록 다양한 기능을 제공합니다. 이를 통해, 사용자는 적절하게 학습된 텍스트 분류 모델로부터 특징을 정량화하기 힘든 텍스트 데이터로부터 잠재적인 인사이트를 추출하고 다양한 서비스에 활용할 수 있습니다.

__아래는 ThanoSQL 텍스트 분류 모델의 활용 및 예시입니다.__

- ThanoSQL 텍스트 분류 모델은 챗봇을 통한 상담이나 문의, 게시판 내의 텍스트의 감정 분석이나 범주 구분 작업에 텍스트 분류 모델을 쉽게 사용할 수 있게 합니다. 이 작업은 추후 고객에게 적절한 담당자와의 연결을 가능하게 합니다.

- ThanoSQL 텍스트 분류 모델은 뉴스나 게시물 공유 서비스에서 게시 콘텐츠의 그룹을 분류할 수 있게 합니다. 게시 콘텐츠의 댓글에 텍스트 분류 모델을 적용함으로써 감정 분석을 가능하게 합니다. 이 작업은 갑자기 이슈가 되거나 욕설이나 비방에 의해 발생할 수 있는 문제를 효율적으로 관리를 가능하게 합니다.

<div class="admonition note">
    <h4 class="admonition-title">본 튜토리얼에서는</h4>
    <p>👉 대표적인 머신러닝 경진대회 플랫폼인 <a href="https://www.kaggle.com/">캐글</a>의  <mark style="background-color:#FFD79C">IMDB Movie Reviews</mark> 데이터 세트를 사용하여 영화 리뷰의 감정을 분류하는 모델을 만듭니다. 이 데이터 세트는 50,000 개의 영화 리뷰 텍스트와 긍정 또는 부정 감정에 대한 목푯값(Target)으로 구성되어 있습니다. 영화 평점을 기준으로 5보다 작은 값을 부정, 7보다 큰 값을 긍정으로 표현하였으며 각 개별 영화는 30 개 이상의 리뷰 결과를 갖지 않습니다.</p>
</div>

<div class="admonition warning">
    <h4 class="admonition-title">튜토리얼 주의 사항</h4>
    <ul>
        <li>텍스트 분류 모델은 하나의 텍스트에서 하나의 목푯값(Target, 범주)를 예측하는 용도로 사용할 수 있습니다.</li>
        <li>텍스트를 나타내는 컬럼과, 텍스트의 목푯값을 나타내는 컬럼이 존재해야 합니다.</li>
        <li>해당 텍스트 분류 모델의 베이스 모델(<code>ELECTRA</code>)은 GPU를 사용합니다. 사용한 모델의 크기와 배치 사이즈에 따라 GPU 메모리가 부족할 수 있습니다. 이 경우, 더 작은 모델을 사용하시거나 배치 사이즈를 줄여보십시오.</li>
    </ul>
</div>

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
GET THANOSQL DATASET movie_review_data
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
COPY movie_review_train
OPTIONS (overwrite=True) 
FROM "thanosql-dataset/movie_review_data/movie_review_train.csv"
```

    Success



```python
%%thanosql
COPY movie_review_test 
OPTIONS (overwrite=True) 
FROM "thanosql-dataset/movie_review_data/movie_review_test.csv"
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
GET THANOSQL MODEL tutorial_text_classification
OPTIONS (overwrite=True)
AS tutorial_text_classification
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

영화 리뷰 감정 분류 모델을 만들기 위해 우리는 ThanoSQL DB에 저장되어 있는 <mark style="background-color:#FFEC92 ">movie_review_train</mark> 테이블을 사용합니다. 아래의 쿼리문을 실행하여 테이블 내용을 확인합니다.


```python
%%thanosql
SELECT *
FROM movie_review_train
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
      <th>review</th>
      <th>sentiment</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>This is the kind of movie that BEGS to be show...</td>
      <td>negative</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Bulletproof is quite clearly a disposable film...</td>
      <td>negative</td>
    </tr>
    <tr>
      <th>2</th>
      <td>A beautiful shopgirl in London is swept off he...</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>3</th>
      <td>VERY dull, obvious, tedious Exorcist rip-off f...</td>
      <td>negative</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Do we really need any more narcissistic garbag...</td>
      <td>negative</td>
    </tr>
  </tbody>
</table>
</div>



<div class="admonition note">
    <h4 class="admonition-title">데이터 이해하기</h4>
    <ul>
        <li><mark style="background-color:#D7D0FF ">review</mark> : 영화 리뷰 텍스트</li>
        <li><mark style="background-color:#D7D0FF ">sentiment</mark> : 해당 리뷰가 긍정적인지(positive), 부정적인지(negative)를 나타내는 목푯값(Target)</li>
    </ul>
</div>

## __2. 사전 학습된 모델을 사용하여 영화 리뷰 감정 분류 결과 예측__

먼저 저희가 사전에 학습해둔 모델로 바로 결과를 예측해보겠습니다. 다음 쿼리문을 실행하면 <mark style="background-color:#E9D7FD ">tutorial_text_classification</mark>모델을 사용하여 영화 리뷰 분류 결과를 예측해볼 수 있습니다.


```python
%%thanosql
PREDICT USING tutorial_text_classification
OPTIONS (
    text_col='review'
    )
AS
SELECT *
FROM movie_review_test
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
      <th>review</th>
      <th>sentiment</th>
      <th>predicted</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>I read the book before seeing the movie, and t...</td>
      <td>positive</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>1</th>
      <td>"9/11," hosted by Robert DeNiro, presents foot...</td>
      <td>positive</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Yesterday I attended the world premiere of "De...</td>
      <td>positive</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Moonwalker is a Fantasy Music film staring Mic...</td>
      <td>positive</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Welcome to Oakland, where the dead come out to...</td>
      <td>positive</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>995</th>
      <td>I remember catching this movie on one of the S...</td>
      <td>negative</td>
      <td>negative</td>
    </tr>
    <tr>
      <th>996</th>
      <td>CyberTracker is set in Los Angeles sometime in...</td>
      <td>negative</td>
      <td>negative</td>
    </tr>
    <tr>
      <th>997</th>
      <td>There is so much that is wrong with this film,...</td>
      <td>negative</td>
      <td>negative</td>
    </tr>
    <tr>
      <th>998</th>
      <td>I am a firm believer that a film, TV serial or...</td>
      <td>positive</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>999</th>
      <td>I think vampire movies (usually) are wicked. E...</td>
      <td>negative</td>
      <td>negative</td>
    </tr>
  </tbody>
</table>
<p>1000 rows × 3 columns</p>
</div>



## __3. 텍스트 분류 모델 만들기__

이전 단계에서 확인한 <mark style="background-color:#FFEC92 ">movie_review_train</mark> 데이터 세트를 사용하여 텍스트 분류 모델을 만듭니다. 아래의 쿼리 구문을 실행하여 <mark style="background-color:#E9D7FD ">my_movie_review_classifier</mark>라는 이름의 모델을 만듭니다.  
(쿼리 실행 시 예상 소요 시간 : 3 min)


```python
%%thanosql
BUILD MODEL my_movie_review_classifier
USING ElectraEn
OPTIONS (
    text_col='review',
    label_col='sentiment',
    epochs=1,
    batch_size=4,
    overwrite=True
    )
AS
SELECT *
FROM movie_review_train
```

    Building model...
    Success


<div class="admonition note">
    <h4 class="admonition-title">쿼리 세부 정보</h4>
    <ul>
        <li>"<strong>BUILD MODEL</strong>" 쿼리 구문을 사용하여 <mark style="background-color:#E9D7FD ">my_movie_review_classifier</mark>라는 모델을 만들고 학습시킵니다. </li>
        <li>"<strong>USING</strong>" 쿼리 구문을 통해 베이스 모델로 <code>ElectraEn</code>을 사용할 것을 명시합니다. </li>
        <li>"<strong>OPTIONS</strong>" 쿼리 구문을 통해 모델 생성에 사용할 옵션을 지정합니다. "text_col"은 학습에 사용할 텍스트를 담은 컬럼의 이름이며, "label_col"은 목푯값의 정보를 담은 컬럼의 이름입니다. "epochs"를 통해 몇 번의 학습을 반복할 지를 지정합니다. "batch_size"는 한 번의 학습에서 읽는 데이터 세트 묶음의 크기입니다.</li>
    </ul>
</div>

<div class="admonition tip">
    <p>여기서는 빠르게 학습하기 위해 "epochs"를 1로 지정했습니다. 일반적으로 숫자가 클수록 많은 계산 시간이 소요되지만 학습이 진행됨에 따라 예측 성능이 올라갑니다.</p>
</div>

<div class="admonition note">
    <p><strong>overwrite가 True일 때</strong>, 사용자는 이전 생성했던 데이터 테이블과 같은 이름의 데이터 테이블을 생성할 수 있습니다.<br>
    반면, <strong>overwrite가 False일 때</strong>, 사용자는 이전에 생성했던 데이터 테이블과 같은 이름의 데이터 테이블을 생성할 수 없습니다.</p>
</div>

## __4. 생성된 모델을 사용하여 영화 리뷰 감정 분류 결과 예측__

이전 단계에서 만든 텍스트 분류 예측 모델을 사용해서 특정 리뷰(학습에 이용되지 않은 데이터 테이블, <mark style="background-color:#FFEC92 ">movie_review_test</mark>)의 목푯값을 예측해 봅니다.


```python
%%thanosql
PREDICT USING my_movie_review_classifier
OPTIONS (
    text_col='review'
    )
AS
SELECT *
FROM movie_review_test
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
      <th>review</th>
      <th>sentiment</th>
      <th>predicted</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>I read the book before seeing the movie, and t...</td>
      <td>positive</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>1</th>
      <td>"9/11," hosted by Robert DeNiro, presents foot...</td>
      <td>positive</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Yesterday I attended the world premiere of "De...</td>
      <td>positive</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Moonwalker is a Fantasy Music film staring Mic...</td>
      <td>positive</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Welcome to Oakland, where the dead come out to...</td>
      <td>positive</td>
      <td>negative</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>995</th>
      <td>I remember catching this movie on one of the S...</td>
      <td>negative</td>
      <td>negative</td>
    </tr>
    <tr>
      <th>996</th>
      <td>CyberTracker is set in Los Angeles sometime in...</td>
      <td>negative</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>997</th>
      <td>There is so much that is wrong with this film,...</td>
      <td>negative</td>
      <td>negative</td>
    </tr>
    <tr>
      <th>998</th>
      <td>I am a firm believer that a film, TV serial or...</td>
      <td>positive</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>999</th>
      <td>I think vampire movies (usually) are wicked. E...</td>
      <td>negative</td>
      <td>negative</td>
    </tr>
  </tbody>
</table>
<p>1000 rows × 3 columns</p>
</div>



<div class="admonition note">
    <h4 class="admonition-title">쿼리 세부 정보</h4>
    <p>"<strong>PREDICT USING</strong>" 쿼리 구문을 통해 이전 단계에서 만든 <mark style="background-color:#E9D7FD ">my_movie_review_classifier</mark> 모델을 예측에 사용합니다.
    "<strong>OPTIONS</strong>"를 통해 예측에 사용할 옵션을 지정합니다. <mark style="background-color:#D7D0FF">review</mark>는 예측에 사용할 텍스트를 담은 컬럼의 이름입니다.
    예측 결과는 <mark style="background-color:#D7D0FF">predicted</mark> 컬럼에 저장되어 반환됩니다.</p>
</div>

## __5. 튜토리얼을 마치며__

이번 튜토리얼에서는  <mark style="background-color:#FFD79C">IMDB Movie Reviews</mark> 데이터 세트를 사용하여 텍스트 분류 모델을 만들어 보았습니다. 초급 단계 튜토리얼인만큼 정확도 향상을 위한 과정 설명보다는 작동 위주의 설명으로 진행했습니다. 텍스트 분류 모델은 각 플랫폼이나 서비스에 맞는 정밀한 튜닝을 통해 정확도를 향상 시킬 수 있습니다. 나만의 데이터를 이용해서 베이스 모델을 학습하거나, [자가학습(Self-supervised Learning)](https://en.wikipedia.org/wiki/Self-supervised_learning) 모델 등을 이용해 나의 데이터를 수치화하여 변환한 후 자동화 된 머신러닝(Auto-ML) 기법을 이용한 배포 또한 가능합니다. 다양한 비정형 데이터(이미지, 오디오, 비디오 등)와 수치형 데이터들을 결합하여 나만의 모델을 만들고 경쟁력있는 서비스를 제공해 보세요.

다음 단계인 [중급 텍스트 분류 모델 만들기] 튜토리얼에서는 텍스트 분류 모델을 더욱 심도있게 다뤄봅니다. 내 서비스를 위한 나만의 텍스트 분류 모델 구축 방법에 대해 더욱 자세히 알고 싶다면 다음 튜토리얼들을 진행해보세요.

* [나만의 데이터 업로드하기](https://docs.thanosql.ai/getting_started/data_upload/)
* [중급 텍스트 분류 모델 만들기]
* [텍스트 변환과 Auto-ML을 이용한 나만의 모델 만들기]
* [나만의 텍스트 분류 모델 배포하기](https://docs.thanosql.ai/how-to_guides/reference/)

<div class="admonition tip">
    <h4 class="admonition-title">나만의 서비스를 위한 모델 배포 관련 문의</h4>
    <p>ThanoSQL을 활용해 나만의 모델을 만들거나, 나의 서비스에 적용하는데 어려움이 있다면 언제든 아래로 문의주세요😊</p>
    <p>텍스트 분류 모델 구축 관련 문의: contact@smartmind.team</p>
</div>
