---
title: ThanoSQL Syntax
---

# __ThanoSQL Syntax__

## 시작 전 사전 정보

- 마지막 수정날짜 : {{ git_revision_date_localized }}

!!! tip ""
    __ThanoSQL에서 사용하는 문법들의 기능과 사용방법을 확인할 수 있습니다.  
    안내 가이드와 함께 활용하면서 모델을 만들고 배포해 보세요.__   

    - ThanoSQL은 [ANSI/ISO 표준](https://en.wikipedia.org/wiki/SQL_syntax)을 준수하며 표준 SQL 쿼리 구문들과 대부분 호환 가능합니다.  
    - ThanoSQL만의 쿼리 기능을 통해 이미지/동영상 내의 내용 검색이 가능합니다.  
    - ThanoSQL만의 쿼리 기능을 통해 이미지 분류 모델, 회귀 예측, 분류 예측, 추천 모델 등의 인공지능 알고리즘들을 SQL 쿼리 구문으로 쉽고 간단하게 만들 수 있습니다.  
    
<div class="card">
  <header>
    <h2 id="card-h2"> ThanoSQL Query 다루기</h2>
  </header>
  <ul class="fullclick">
    <li>
      <a href="../ThanoSQL_query/LIST_SYNTAX/">
        <h3>저장한 모델 확인하기</h3>
        <p>
          ThanoSQL에 저장한 모델을 조회하는 방법을 알아봅니다.
        </p>
      </a>
    </li>
    <li>
      <a href="../ThanoSQL_query/UPLOAD_SYNTAX/">
        <h3>모델 업로드 하기</h3>
        <p>
          직접 만든 모델을 업로드하여 ThanoSQL 내부에서 사용하는 방법을 알아봅니다.
        </p>
      </a>
    </li>
    <li>
      <a href="../ThanoSQL_query/GET_SYNTAX/">
        <h3>모델 및 튜토리얼 데이터 세트 가져오기</h3>
        <p>
          ThanoSQL Pre-built 모델 및 튜토리얼 데이터 세트 가져오는 방법을 알아봅니다. 
        </p>
      </a>
    </li>
    <li>
      <a href="../ThanoSQL_query/CREATE_TABLE_SYNTAX/">
        <h3>비정형 데이터 변환하기</h3>
        <p>
          비정형 데이터(이미지, 오디오, 비디오)를 인공지능 모델을 통해 수치화하여 데이터 테이블을 생성하는 방법을 알아봅니다.
        </p>
      </a>
    </li>
    <li>
      <a href="../ThanoSQL_query/CONVERT_USING_SYNTAX/">
        <h3>비정형 특성 추가하기</h3>
        <p>
          비정형 데이터(이미지, 오디오, 비디오)의 정보를 인공지능 모델을 통해 수치화하고 사용할 데이터 테이블에 추가하는 방법을 알아봅니다.
        </p>
      </a>
    </li>
    <li>
      <a href="../ThanoSQL_query/SEARCH_SYNTAX/">
        <h3>비정형 데이터 검색하기</h3>
        <p>
          비정형 데이터(이미지, 오디오, 비디오)에서 내용이나 의미 또는 유사도 등을 검색하는 방법을 알아봅니다.
        </p>
      </a>
    </li>
    <li>
      <a href="../ThanoSQL_query/PRINT_SYNTAX/">
        <h3>결과 출력하기</h3>
        <p>
          비정형 데이터(이미지, 오디오, 비디오)을 출력하는 방법을 알아봅니다.
        </p>
      </a>
    </li>
  </ul>
</div>

<div class="card">
  <header>
    <h2 id="card-h2">ThanoSQL ML 다루기</h2>
  </header>
  <ul class="fullclick">
    <li>
      <a href="../ThanoSQL_ml/BUILD_MODEL_SYNTAX/">
        <h3>모델 학습하기</h3>
        <p>
            ThanoSQL 모델을 학습시키는 방법을 알아봅니다.
        </p>
      </a>
    </li>
    <li>
      <a href="../ThanoSQL_ml/EVALUATE_MODEL_SYNTAX/">
        <h3>모델 평가하기</h3>
        <p>
            ThanoSQL 모델을 하는 평가하는 방법을 알아봅니다.
        </p>
      </a>
    </li>
    <li>
      <a href="../ThanoSQL_ml/FIT_MODEL_SYNTAX/">
        <h3>모델 재학습하기</h3>
        <p>
            ThanoSQL 모델을 재학습시키는 방법을 알아봅니다.
        </p>
      </a>
    </li>
    <li>
      <a href="../ThanoSQL_ml/TRANSFORM_MODEL_SYNTAX/">
        <h3>모델 적용을 위한 데이터 전처리하기</h3>
        <p>
            예측을 위해 학습한 ThanoSQL 모델을 통해 전처리하는 방법을 알아봅니다.
        </p>
      </a>
    </li>
    <li>
      <a href="../ThanoSQL_ml/PREDICT_MODEL_SYNTAX/">
        <h3>모델 적용하기</h3>
        <p>
            학습한 ThanoSQL 모델을 이용하여 예측하는 방법을 알아봅니다. 
        </p>
      </a>
    </li>    
    <li>
      <a href="../ThanoSQL_ml/DELETE_MODEL_SYNTAX/">
        <h3>모델 삭제하기</h3>
        <p>
            모델 관리를 위해 학습한 ThanoSQL 모델을 삭제하는 방법을 알아봅니다.
        </p>
      </a>
    </li>
  </ul>
</div>

<div class="card">
  <header>
    <h2 id="card-h2"> ThanoSQL Rest API 다루기</h2>
  </header>
  <ul class="fullclick">
    <li>
      <a href="../ThanoSQL_connecting/data_upload/">
        <h3>데이터 불러오기</h3>
        <p>
            REST API를 사용하기 위해 데이터를 불러오는 방법을 알아봅니다.
        </p>
      </a>
    </li>
    <li>
      <a href="../ThanoSQL_connecting/thanosql_api/rest_api_token/">
        <h3>REST API 사용하기</h3>
        <p>
            REST API를 사용하여 ThanoSQL을 사용하는 방법을 알아봅니다.
        </p>
      </a>
    </li>
  </ul>
</div>