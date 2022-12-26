---
title: COPY
---

# __COPY__

## __1. COPY 문__

사용자는 "__COPY__" 구문을 사용하여 현재 ThanoSQL 워크스페이스에 있는 데이터 파일, 데이터 폴더, 그리고 작업중인 pandas 데이터프레임을 ThanoSQL 워크스페이스 데이터베이스 안의 데이터 테이블로 생성할 수 있습니다.

!!! note "__지원 가능한 데이터 파일 형식__"
    - csv
    - pkl, pickle
    - xls, xlsx, xlsm, xlsb

## __2. COPY 구문__

```sql
COPY (table_name_expression)
OPTIONS (overwrite=True)
FROM (file_path | dir_path)
```

!!! note "쿼리 세부 정보"
    - "OPTIONS" 절은 매개변수의 값을 기본값에서 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.
        - "overwrite": 동일 이름의 테이블이 존재하는 경우 덮어쓰기 가능 유무를 설정합니다. True일 경우 기존 테이블은 새로운 테이블로 변경됩니다. (bool, optional, True|False, default: False)

## __3. COPY 예시__

### __3-1. 데이터 파일 사용시__

아래 예는 "COPY" 구문에 사용자가 정의한 example.csv 파일을 사용하는 방법을 보여줍니다. 정의된 데이터 파일은 ThanoSQL Engine을 통해 데이터베이스의 테이블로 생성됩니다.

```sql
%%thanosql
COPY mytable
OPTIONS (overwrite=True)
FROM 'data/example.csv'
```

### __3-2. 데이터 폴더 사용시__

아래 예는 "COPY" 구문에 사용자가 정의한 diet_image_data 폴더를 사용하는 방법을 보여줍니다. 정의된 데이터 폴더는 ThanoSQL Engine을 통해 데이터베이스의 테이블로 생성됩니다.

!!! note "__데이터 폴더 COPY 사용법__"
    - 이미지, 오디오, 혹은 비디오가 있는 데이터 폴더의 경로를 입력하면 "__COPY__" 구문이 폴더 내의 각각의 파일들을 자동으로 행으로 변화하여 데이터 테이블로 재생성합니다.

```sql
%%thanosql
COPY mytable
OPTIONS (overwrite=True)
FROM 'diet_image_data/'
```

### __3-3. Pandas 데이터프레임 사용시__

아래 예는 "COPY" 구문에 사용자가 정의한 Pandas 데이터프레임을 사용하는 방법을 보여줍니다. 정의된 데이터프레임은 ThanoSQL Engine을 통해 데이터베이스의 테이블로 생성됩니다.

#### Pandas 데이터프레임 준비
```python
# Pandas Dataframe을 만듭니다 
df = pd.read_csv("./diet_image_data/sample.csv")
# 데이터프레임은 JSON으로 변환 되어야 하며, 'orient'는 꼭 'records'로 명시합니다
df_in_json = df.to_json(orient="records")

# f-string을 사용하여 'df_in_json'을 COPY 구문으로 감싸줍니다
copy_pandas_df = f'''
COPY mytable
OPTIONS (overwrite=True)
FROM '{df_in_json}'
'''
```

#### COPY 구문으로 Pandas 데이터프레임 사용하기 

```sql
%thanosql $copy_pandas_df
```

!!! warning "__Warning__"
    - Pandas 데이터프레임은 __COPY__ 구문으로 감싸기 전에 JSON으로 변환하여야 합니다.
    - __${변수_이름}__ 은 __%thanosql__ 뒤에 명시되어야 합니다.