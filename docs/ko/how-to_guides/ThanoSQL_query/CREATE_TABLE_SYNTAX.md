---
title: 비정형 데이터 변환하기
---

# __비정형 데이터 변환하기 (CREATE TABLE)__

## 시작 전 사전 정보

- 마지막 수정날짜 : {{ git_revision_date_localized }}

## __1. CREATE TABLE 쿼리 구문 개요__ 

사용자는 "__CREATE TABLE__" 구문을 사용하여 비정형 데이터 (이미지, 오디오, 비디오 등)를 수치화 알고리즘을 사용하여 벡터 형식으로 변환한 데이터 테이블을 생성할 수 있습니다.

## __2. CREATE TABLE 구문__ 

```sql
CREATE TABLE [사용자 지정 데이터 테이블 이름]
USING [사용할 인공지능 모델]
OPTIONS (
    overwrite=True 
) -- default:False
FROM [사용할 데이터 세트]
```

!!! note "__쿼리 세부 정보__"    
    - "__OPTIONS__" 쿼리 구문을 통해 __CREATE TABLE__ 에 사용할 옵션을 지정합니다.  
        - "overwrite" : 동일 이름의 데이터 세트가 DB상에 존재하는 경우 덮어쓰기 가능 유무 설정. True일 경우 기존 데이터 세트는 새로운 데이터 세트로 변경됨 (True|False, DEFAULT : False) 


## __3. CREATE TABLE 구문 예시__ 

아래 예는 'data/thanosAlgo/image_search/junyoung_test/' `Color_descriptor`라는 속성 추출 인공지능 모델을 사용하여 경로에 존재하는 이미지 파일들을 사용자가 지정한 이름인 `color_descriptor_table_test` 라는 데이터 테이블로 ThanoSQL DB 내에 생성합니다. 

```sql
%%thanosql
CREATE TABLE color_descriptor_table_test 
USING Color_Descriptor 
OPTIONS (
    data_type='image',
    file_type=['.jpg'],
    overwrite = True
    ) 
FROM 'data/thanosAlgo/image_search/junyoung_test/'
```
