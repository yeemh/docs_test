---
title: ThanoSQL 워크스페이스 사용법
---

# __ThanoSQL 워크스페이스 사용법__ 

ThanoSQL의 워크스페이스는 [Jupyter Lab](https://github.com/jupyterlab/jupyterlab)을 기반으로 하는 웹 기반 컴퓨팅 환경입니다.

이 환경에서 **ThanoSQL**을 본격적으로 사용하기 위해서는 먼저 **thanosql** cell magic을 불러와야 합니다.

!!! tip ""
    상단의 실행 버튼을 누르거나, **Ctrl + Enter** 혹은 **Shift + Enter** 단축키로도 실행할 수 있습니다.

## __1. ThanoSQL cell magic 불러오기__

```sql
%load_ext thanosql
```
## __2. API_TOKEN 설정하기__

다음으로, 각 사용자의 워크스페이스 API_TOKEN 설정을 위해 브라우저 상단의 **GET API_TOKEN** 버튼을 누른 후 붙여넣기하여 설정해줍니다. 

```sql
%thanosql API_TOKEN=<발급받은_API_TOKEN>
```

ex)

```sql
%thanosql API_TOKEN=eyGVFDdfafddvczs
```

!!! notice "ThanoSQL의 쿼리문 작성 방법"
    ThanoSQL에서 쿼리문을 작성하는 방법에는 one-line 작성 방법과 multi-line 작성 방법이 있습니다.  
    - one-line 문법의 경우, 쿼리를 테이블 형식으로 반환하며 변수에 테이블을 할당할 때, 주로 사용됩니다. 또한 워크스페이스 사용법 1번과 2번처럼 ThanoSQL cell magic과 Token을 할당할 때 사용합니다.  
    - multi-line 문법의 경우, 다른 DBMS를 사용할 때와 같은 사용자 경험을 제공하며 테이블을 조회하거나 ThanoSQL 확장 문법을 실행할 때 사용합니다.



## __3. LIST 쿼리 구문으로 ThanoSQL 모델/튜토리얼 목록 확인하기__

**ThanoSQL**을 사용할 모든 준비가 끝났습니다.

아래 ThanoSQL문을 실행시키면 Pre-built된 ThanoSQL 모델 목록을 확인할 수 있습니다.

```sql
%%thanosql
LIST THANOSQL MODEL
```

[![IMAGE](/img/getting_started/img6.png)](/img/getting_started/img6.png)

아래 ThanoSQL문을 실행시키면 [ThanoSQL 기술 문서](https://docs.thanosql.ai)에 있는 튜토리얼 목록을 확인할 수 있습니다.

```sql
%%thanosql
LIST THANOSQL TUTORIAL
```

[![IMAGE](/img/getting_started/img9.png)](/img/getting_started/img9.png)


아래 ThanoSQL문을 실행시키면 [ThanoSQL 기술 문서](https://docs.thanosql.ai)에 있는 튜토리얼에서 사용된 데이터 테이블 리스트를 확인할 수 있습니다.

```sql
%%thanosql
LIST THANOSQL DATASET
```

[![IMAGE](/img/getting_started/img10.png)](/img/getting_started/img10.png)
