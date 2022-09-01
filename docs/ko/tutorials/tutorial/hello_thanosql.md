---
jupyter:
  jupytext:
    text_representation:
      extension: .md
      format_name: markdown
      format_version: '1.3'
      jupytext_version: 1.14.1
  kernelspec:
    display_name: Python 3.8.9 64-bit
    language: python
    name: python3
---

<!-- #region tags=[] -->
# __Hello ThanoSQL!__

ThanoSQL은 정형과 비정형 데이터 모두 SQL 만으로 쿼리(Query, 질의)와 AI(인공지능) 모델링이 가능한 통합 플랫폼입니다. RDB(Relational DataBase, 관계형 데이터베이스), AI 그리고 Big Data Platform의 기능을 하나의 플랫폼에서 적용할 수 있으며 AI 기반의 디지털 전환 시 발생하는 비효율성을 획기적으로 줄일 수 있습니다.

- ThanoSQL은 정형과 비정형 데이터 모두 SQL만으로 쿼리가 가능하며, 빠른 AI 모델링이 가능합니다.
- 람다 아키텍처 기반의 비정형 빅데이터 플랫폼을 ThanoSQL 하나로 대체하여 개발, 배포 그리고 운영을 하나의 프로세스로 가능하게 합니다.
- 빅데이터 처리 및 분산 병렬처리 기술을 기반으로 하여 기존 대비 2배 이상 빠르게 데이터 처리가 가능합니다.

## __ThanoSQL 워크스페이스 사용__
ThanoSQL의 워크스페이스는 [Jupyter Lab](https://github.com/jupyterlab/jupyterlab)을 기반으로 하는 웹 기반 컴퓨팅 환경입니다.

이 환경에서 `ThanoSQL`을 본격적으로 사용하기 위해서는 먼저 `thanosql` cell magic을 불러와야 합니다.

(상단의 실행 버튼을 누르거나, `Ctrl + Enter` 혹은 `Shift + Enter` 단축키로도 실행할 수 있습니다.)
<!-- #endregion -->

```python tags=[]
%load_ext thanosql
```

다음으로, 각 사용자의 워크스페이스 API_TOKEN 설정을 위해 브라우저 상단의 `Get API_TOKEN` 버튼을 누른 후 붙여넣기하여 설정해줍니다.

```
%thanosql API_TOKEN=<발급받은_API_TOKEN>

예시)
%thanosql API_TOKEN=eyJhbGciOiJIUzI1NiIsInR..
```

```python tags=[]
%thanosql API_TOKEN=<발급받은_API_TOKEN>
```

`ThanoSQL`을 사용할 모든 준비가 끝났습니다.

아래 ThanoSQL문을 실행시키면 Pre-built된 ThanoSQL 모델 목록을 확인 할 수 있습니다.

```python tags=[]
%%thanosql
LIST THANOSQL MODEL
```

## __튜토리얼 가져오기__
특정 튜토리얼을 가지고 오고 싶다면 아래의 문을 실행시키면 됩니다. 가져올 튜토리얼의 Github URL은 아래의 링크를 통해 확인할 수 있습니다.

[튜토리얼 Github URL 리스트](https://docs.thanosql.ai/tutorial/tutorial_wget_list/)

```python
!wget [가져올 Tutorial의 Github URL]
```

더 많은 ThanoSQL 사용법과 Tutorial은 [ThanoSQL 기술 문서](https://docs.thanosql.ai)에서 확인하세요.
