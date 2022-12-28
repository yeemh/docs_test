---
title: Preprocess
---

# __Preprocess__

## __1. Preprocess 문__

사용자는 "__Preprocess__" 구문을 사용하여 ThanoSQL의 "__Preprocess__" 함수를 통해 데이터를 전처리합니다.

## __2. Preprocess 함수__

### __2-1. video_to_df__

"__video_to_df__"는 비디오를 일정한 간격으로 분할하여 파일로 저장하고 데이터 테이블을 만드는 기능입니다.

__video_to_df 구문__

```sql
query_statement:
    query_expr

FUNCTION preprocess.video_to_df
OPTIONS (
    expression [ , ...]
    )
{ AS (query_expr) | FROM {file_path_expression} }
```

__OPTIONS 절__

```sql
OPTIONS (
    [method={'split'}],
    [interval=VALUE],
    (result_col=expression),
    (result_dir=expression)
    )
```

"__OPTIONS__" 절에서 매개변수의 값을 기본값으로부터 변경할 수 있습니다. 각 매개변수의 의미는 아래와 같습니다.

- "method": 영상 전처리 방식을 설정합니다. 현재는 'split'만 지원합니다. (str)
- "interval": 간격을 초 단위로 설정합니다. (int, optional, default: 1)
- "result_col": 전처리 후 분할 영상 경로를 담을 컬럼 이름을 설정합니다. (str)
- "result_dir": 전처리 후 분할 영상이 저장될 폴더 이름을 설정합니다. (str)

__video_to_df 예시__

```sql
%%thanosql
FUNCTION preprocess.video_to_df
OPTIONS (
   method='split',
   interval=1,
   result_col='video_split_path', 
   result_dir='video_split_folder'
    )
FROM 'thanosql-dataset/kinetics700_data/video/1ejgHKw8E3Y.mp4'
```