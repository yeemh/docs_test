---
jupyter:
  jupytext:
    text_representation:
      extension: .md
      format_name: markdown
      format_version: '1.3'
      jupytext_version: 1.14.1
  kernelspec:
    display_name: Python 3 (ipykernel)
    language: python
    name: python3
---

# __오디오 파일을 받아쓰는 음성 인식 모델 만들기__


## 시작 전 사전 정보

- 튜토리얼 난이도 : ★☆☆☆☆
- 읽는데 걸리는 시간 : 10분
- 사용 언어 : [SQL](https://ko.wikipedia.org/wiki/SQL) (100%)
- 실행 파일 위치 : tutorial/thanosql_ml/audio_recognition/audio_recognition_wav2vec.ipynb
- 참고 문서 : [LibriSpeech 데이터 세트](http://www.openslr.org/12), [wav2vec 2.0: A Framework for Self-Supervised Learning of Speech Representations](https://arxiv.org/abs/2006.11477)


## 튜토리얼 소개

<div class="admonition note">
    <h4 class="admonition-title">음성 인식 기술 이해하기</h4>
    <p>컴퓨터 음성 인식 또는 음성-텍스트 변환(Speech-to-Text)이라고도 부르는 음성 인식 기술은 프로그램이 사람의 음성을 텍스트 형식으로 처리할 수 있도록 해줍니다. 최근 자동차, 의료 분야, 인공지능 스피커나 스마트폰을 이용한 일상 생활까지 광범위한 분야에서 활용되고 있습니다. 최근 <a href="https://ko.wikipedia.org/wiki/%EA%B8%B0%EA%B3%84_%ED%95%99%EC%8A%B5">머신러닝(기계학습/Machine Learning)</a> 알고리즘을 활용한 음성 인식 기술은 문법, 구문, 구조, 오디오와 음성 신호의 구성을 통합하여 음성을 이해하고 처리합니다.</p>
</div>

<div class="admonition warning">
    <p>일반적으로 음성 인식(Voice Recognition)과 혼동 될 수 있는데, 음성 인식은 개별 사용자의 목소리를 식별하는 데만 집중합니다.</p>
</div>

오늘날 음성 인식 기술은 다양한 산업 분야에서 응용되고 있습니다. 음성 인식 기술의 발전은 단순 여행용 자동 통역에서 난이도가 높은 비즈니스 회의 통역까지 확대되고 있습니다. 또한, 음성 인식 기술은 음성 합성 기술로 발전하여 가상 안내원이나 비서 역할을 수행하기도 하고 특정 연예인이나 유명인의 목소리를 흉내내어 정해진 지문을 목소리로 변환하기도 하며 응용되고 있습니다.

__아래는 ThanoSQL 음성 인식 모델의 활용 및 예시입니다.__

- 음성 인식 기술은 전화 상담 데이터를 텍스트로 변환하여 고객의 감정 분석이나 상담 트렌드 분석 등을 가능하게 합니다. 상담자는 음성 인식 기술을 통해, 고객의 문의에 대한 답변이 되는 정보나 과거의 유사 사례 등을 빠르게 제공받아 고객 상담을 개선할 수 있습니다.
또한, 상담 종료 후에는 음성으로 저장되어 있는 데이터를 활용하여 감정 분석을 통해 고객의 만족도 등을 간접적으로 측정하여 트렌드 별 고객의 만족도를 확인할 수 있습니다.

- 음성 인식 기술을 사용하면 키보드로 작성하는 메모보다 빠르게 작성이 가능하고, 긴 음성 파일에서도 빠르게 특정 키워드를 검색 할 수 있습니다.

<div class="admonition note">
    <h4 class="admonition-title">본 튜토리얼에서는</h4>
    <p>👉 Librispeech [Panayotov et al. 2015]는 음성인식 연구에 있어서 가장 널리 사용되는 대규모 영어 음성 데이터 중 하나로 사용자 참여형 오디오북 프로젝트인 <a href="https://librivox.org/">LibriVox project</a>의 결과물입니다. 16kHz로 샘플링된 약 1,000시간 분량의 녹음된 오디오북 데이터를 가공하여 만들었습니다. 튜토리얼을 위한 대상 테이블은 미리 업로드 된 음성 파일의 경로와 스크립트로 구성되어 있습니다. 본 튜토리얼은 음성 파일을 텍스트로 변환하는 것을 목표로 하고 있습니다. </p>
</div>

<div class="admonition warning">
    <h4 class="admonition-title">튜토리얼 주의 사항</h4>
    <ul>
        <li>ThanoSQL에서 현재 지원하는 오디오 파일의 형식은 '.wav', '.flac' 입니다.</li>
        <li>오디오 파일 경로를 나타내는 컬럼(Column)과 목푯값(Target)에 해당하는 텍스트를 나타내는 컬럼이 테이블에 존재해야 합니다.</li>
        <li>해당 음성 인식 모델의 베이스 모델(<code>Wav2Vec2En</code>)은 GPU를 사용합니다. 사용한 모델의 크기와 배치 사이즈에 따라 GPU 메모리가 부족할 수 있습니다. 이 경우, 더 작은 모델을 사용하시거나 배치 사이즈를 줄여보십시오.</li>
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
GET THANOSQL DATASET librispeech_data
OPTIONS (overwrite=True)
```

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
COPY librispeech_train 
OPTIONS (overwrite=True)
FROM "thanosql-dataset/librispeech_data/librispeech_train.csv"
```

```python
%%thanosql
COPY librispeech_test 
OPTIONS (overwrite=True)
FROM "thanosql-dataset/librispeech_data/librispeech_test.csv"
```

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
GET THANOSQL MODEL tutorial_audio_recognition
OPTIONS (overwrite=True)
AS tutorial_audio_recognition
```

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

본 튜토리얼을 진행하기 위해 우리는 ThanoSQL DB에 저장되어 있는  <mark style="background-color:#FFEC92 ">librispeech_train</mark> 테이블을 사용합니다. 아래의 쿼리문을 실행하여 테이블 내용을 확인합니다.

```python
%%thanosql
SELECT *
FROM librispeech_train
LIMIT 5
```

<div class="admonition note">
    <h4 class="admonition-title">데이터 이해하기</h4>
    <ul>
        <li><mark style="background-color:#D7D0FF ">audio_path</mark>: 음성 파일의 위치 경로</li>
        <li><mark style="background-color:#D7D0FF ">text</mark>: 해당 음성의 목푯값(Target, 스크립트)</li>
    </ul>
</div>

```python
%%thanosql
PRINT AUDIO 
AS
SELECT audio_path
FROM librispeech_train
LIMIT 3
```

## __2. 사전 학습된 모델을 사용하여 음성 인식 결과 예측__

다음 쿼리 구문을 실행하여 사전 학습된 음성인식 모델인 <mark style="background-color:#E9D7FD ">tutorial_audio_recognition</mark>을 사용하여 
결과를 예측합니다.

```python
%%thanosql
PREDICT USING tutorial_audio_recognition
OPTIONS (
    audio_col='audio_path',
    batch_size=8
    )
AS 
SELECT * 
FROM librispeech_train
```

<!-- #region tags=[] -->
## __3. 음성 인식 모델 만들기__

이전 단계에서 확인한 <mark style="background-color:#FFEC92 ">librispeech_train</mark> 데이터 세트를 사용하여 음성 인식 모델을 만듭니다. 아래의 쿼리 구문을 실행하여 <mark style="background-color:#E9D7FD ">my_speech_recognition_model</mark>이라는 이름의 모델을 만듭니다.  
(쿼리 실행 시 예상 소요 시간 : 1 min)
<!-- #endregion -->

```python
%%thanosql
BUILD MODEL my_speech_recognition_model
USING Wav2Vec2En
OPTIONS (
    audio_col='audio_path',  
    text_col='text',  
    epochs=1,  
    batch_size=4,
    overwrite= True  
    )
AS
SELECT *
FROM librispeech_train
```

<div class="admonition note">
    <h4 class="admonition-title">쿼리 세부 정보</h4>
    <ul>
        <li>"<strong>BUILD MODEL</strong>" 쿼리 구문을 사용하여  <mark style="background-color:#E9D7FD ">my_speech_recognition_model</mark> 모델을 만들고 학습시킵니다.</li>
        <li>"<strong>USING</strong>" 쿼리 구문을 통해 베이스 모델로 <code>Wav2Vec2En</code>을 사용할 것을 명시합니다.</li>
        <li>"<strong>OPTIONS</strong>" 쿼리 구문을 통해 모델 생성에 사용할 옵션을 지정합니다.
        <ul>
            <li>"audio_col" : 학습에 사용할 오디오 경로를 담은 컬럼의 이름</li>
            <li>"text_col" :  오디오의 스크립트 정보를 담은 컬럼의 이름</li>
            <li>"epochs" : 모든 학습 데이터 세트를 학습하는 횟수</li>
            <li>"batch_size" : 한 번의 학습에서 읽는 데이터 세트 묶음의 크기</li>
            <li>"overwrite" : 동일 이름의 모델이 존재하는 경우 덮어쓰기 가능 유무 설정. True일 경우 기존 모델은 새로운 모델로 변경됨 (True|False, DEFAULT : False)</li>
        </ul>
        </li>
    </ul>
</div>

<div class="admonition tip">
    <p>여기서는 빠르게 학습하기 위해 "epochs"를 1로 지정했습니다. 일반적으로 숫자가 클수록 많은 계산 시간이 소요되지만 학습이 진행됨에 따라 예측 성능이 올라갑니다.</p>
</div>


## __4. 만든 모델을 사용하여 음성 인식 결과 예측__

이전 단계에서 만든 음성 인식 모델을 사용해서 특정 음성(학습에 이용되지 않은 데이터 테이블,  <mark style="background-color:#FFEC92 ">librispeech_test</mark>)의 목푯값(스크립트)를 예측해 봅니다. 아래 쿼리를 수행하고 나면, 예측 결과는 <mark style="background-color:#D7D0FF">predicted</mark> 컬럼에 저장되어 반환됩니다.

```python
%%thanosql
PREDICT USING my_speech_recognition_model
OPTIONS (
    audio_col='audio_path'
    )
AS
SELECT *
FROM librispeech_test
```

<div class="admonition note">
    <h4 class="admonition-title">쿼리 세부 정보</h4>
    <ul>
        <li>"<strong>PREDICT USING</strong>" 쿼리 구문을 통해 이전 단계에서 만든 <mark style="background-color:#E9D7FD ">my_speech_recognition_model</mark> 모델을 예측에 사용합니다.</li>
        <li>"<strong>OPTIONS</strong>" 쿼리 구문을 통해 예측에 사용할 옵션을 지정합니다.
        <ul>
            <li>"audio_col" : 예측에 사용할 오디오 경로를 담은 컬럼의 이름</li>
        </ul>
        </li>
    </ul>
</div>


## __5. 튜토리얼을 마치며__

이번 튜토리얼에서는 <mark style="background-color:#FFD79C">LibriSpeech</mark> 데이터 세트를 사용하여 음성 인식 모델을 만들어 보았습니다. 초급 단계 튜토리얼인 만큼 정확도 향상을 위한 과정 설명보다는 작동 위주의 설명으로 진행했습니다. 음성 인식 모델은 각 플랫폼이나 서비스에 맞는 정밀한 튜닝을 통해 정확도를 향상 시킬 수 있습니다. 나만의 데이터를 이용해서 베이스 모델을 학습을 진행하고 성능을 향상시켜 보세요. 다양한 비정형 데이터(이미지, 비디오, 텍스트 등)와 수치형 데이터들을 결합하여 나만의 모델을 만들고 경쟁력있는 서비스를 만들 수 있습니다.

다음 단계인 [중급 음성 인식 모델 만들기] 튜토리얼에서는 음성 인식 모델을 더욱 심도있게 다뤄봅니다. 내 서비스를 위한 나만의 음성 인식 모델 구축 방법에 대해 더욱 자세히 알고 싶다면 다음 튜토리얼들을 진행해보세요.

* [나만의 데이터 업로드하기](https://docs.thanosql.ai/how-to_guides/ThanoSQL_connecting/data_upload/)
* [중급 음성 인식 모델 만들기]
* [나만의 음성 인식 모델 배포하기](https://docs.thanosql.ai/how-to_guides/ThanoSQL_connecting/thanosql_api/rest_api_thanosql_query/)

<div class="admonition tip">
    <h4 class="admonition-title">나만의 서비스를 위한 모델 배포 관련 문의</h4>
    <p>ThanoSQL을 활용해 나만의 모델을 만들거나, 나의 서비스에 적용하는데 어려움이 있다면 언제든 아래로 문의주세요😊</p>
    <p>음성 인식 모델 구축 관련 문의: contact@smartmind.team</p>
</div>
