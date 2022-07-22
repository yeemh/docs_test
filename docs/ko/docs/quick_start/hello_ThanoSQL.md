# __ThanoSQL 워크스페이스 사용법__ 

**[이전 문서 - ThanoSQL 웹 사용법](/quick_start/how_to_use_ThanoSQL/)** <br>**[다음 문서 - 안내 가이드](/quick_start/algorithm_list/)**


ThanoSQL의 워크스페이스는 [Jupyter Lab](https://github.com/jupyterlab/jupyterlab)을 기반으로 하는 웹 기반 컴퓨팅 환경입니다.

이 환경에서 **ThanoSQL**을 본격적으로 사용하기 위해서는 먼저 **thanosql** cell magic을 불러와야 합니다.

!!! tip ""
    상단의 실행 버튼을 누르거나, **Ctrl + Enter** 혹은 **Shift + Enter** 단축키로도 실행할 수 있습니다.

```sql
%load_ext thanosql
```
다음으로, 각 사용자의 워크스페이스 API_TOKEN 설정을 위해 브라우저 상단의 **GET API_TOKEN** 버튼을 누른 후 붙여넣기하여 설정해줍니다. 

```sql
%thanosql API_TOKEN=<발급받은_API_TOKEN>

예시)
%thanosql API_TOKEN=eyJhbGciOiJIUzI1NiIsInR..
```

**ThanoSQL**을 사용할 모든 준비가 끝났습니다.

아래 ThanoSQL문을 실행시키면 Pre-built된 ThanoSQL 모델 목록을 확인할 수 있습니다.

```sql
%%thanosql
LIST THANOSQL MODEL
```

<a href = "/img/quick_start/img6.png">
    <img src = "/img/quick_start/img6.png"></img>
</a>

아래 ThanoSQL문을 실행시키면 [ThanoSQL 기술 문서](https://docs.thanosql.ai)에 있는 튜토리얼 목록을 확인할 수 있습니다.

```sql
%%thanosql
LIST THANOSQL TUTORIAL
```

<a href = "/img/quick_start/img9.png">
    <img src = "/img/quick_start/img9.png"></img>
</a>


아래 ThanoSQL문을 실행시키면 [ThanoSQL 기술 문서](https://docs.thanosql.ai)에 있는 튜토리얼에서 사용된 데이터 테이블 리스트를 확인할 수 있습니다.

```sql
%%thanosql
LIST THANOSQL DATASET
```

<a href = "/img/quick_start/img10.png">
    <img src = "/img/quick_start/img10.png"></img>
</a>
