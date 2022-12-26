---
title: 개요
---

# __ThanoSQL__

- 읽는데 걸리는 시간: 3분
- 마지막 수정날짜: {{ git_revision_date_localized }}

## __ThanoSQL 정의__

ThanoSQL은 정형과 __비정형 데이터__[^1] 모두 __SQL__[^2]의 [쿼리(Query, 질의)](https://ko.wikipedia.org/wiki/%EC%BF%BC%EB%A6%AC)를 사용하여 AI(인공지능) 모델링 및 검색을 가능하게 한 통합 플랫폼입니다. __RDB__[^3](Relational DataBase, 관계형 데이터베이스), AI 그리고 Big Data Platform의 기능을 하나의 플랫폼에서 적용할 수 있으며, AI 기반의 디지털 전환 시 발생하는 비효율성을 획기적으로 줄일 수 있습니다.

- ThanoSQL에서는 정형과 비정형 데이터 모두 SQL 쿼리 형식으로 검색이 가능하며, 빠른 AI 모델링 또한 가능합니다.   
- __람다 아키텍처__[^4] 기반의 비정형 빅데이터 플랫폼을 ThanoSQL 하나로 대체하여 개발, 배포 그리고 운영을 하나의 프로세스로 통합합니다.  
- 빅데이터 처리 및 분산 병렬처리 기술을 기반으로 하여 기존 대비 2배 이상 빠르게 데이터 처리가 가능합니다.

## __ThanoSQL의 장점__

AI 기반 디지털 전환은 다양한 언어, 프레임 워크 그리고 람다 아키텍처 기반의 빅데이터 플랫폼 등 광범위한 지식과 기술 구현, 지속적인 인사이트 발굴이 필요합니다. 이러한 복잡한 요구 사항들로 인해 기술 구현의 난이도와 프로세스의 복잡성이 점진적으로 증가하여 결국 개발, 배포, 운영 등에 많은 인력과 시간을 요구합니다.

아래 그림은 데이터 엔지니어링 에코시스템 맵으로, 데이터 사이언스 및 빅데이터 분석 시스템 구축을 위한 60여 가지의 기술들을 한눈에 보여주고 있습니다. 

[![IMAGE](/img/index/img1.png)](/img/index/img1.png)

이러한 배경에서 ThanoSQL은

- 다양한 개발/운영 플랫폼, 언어, 프레임워크를 하나로 통합하여 획기적으로 개발 기간을 단축하고 비효율을 제거합니다. 

ThanoSQL을 활용하면 위 그림의 수많은 기술 스택들을 전혀 알 필요가 없으며, 오직 SQL과 Python만 사용해서 그 모든 기술 스택을 전부 아는 개발자(또는 데이터 엔지니어)들보다 빠르게 좋은 결과를 지속적으로 만들어낼 수 있습니다. 

[ThanoSQL의 혁신적인 결과를 지금 체험해보세요](https://www.thanosql.ai/){ .md-button .md-button--primary target="_blank"}

<div class="card">
    <header>
        <h2 id="card-h2"> ThanoSQL 시작하기</h2>
    </header>
    <ul class="fullclick">
        <li>
            <a href="/ko/getting_started/how_to_use_ThanoSQL/">
                <h3>
                    ThanoSQL 웹 사용법
                </h3>
                <p>
                    ThanoSQL 웹에서의 계정 생성 및 관리 관련하여 제공되는 화면 및 기능에 대해 설명합니다. 
                </p>
            </a>
        </li>
        <li>
            <a href="/ko/getting_started/hello_ThanoSQL/">
                <h3>
                    ThanoSQL 워크스페이스 사용법
                </h3>
                <p>
                    ThanoSQL 워크스페이스의 기본적인 사용법에 대해 설명합니다.  
                </p>
            </a>
        </li>
        <li>
            <a href="/ko/getting_started/data_upload/">
                <h3>
                    ThanoSQL 데이터 업로드하기
                </h3>
                <p>
                    ThanoSQL 워크스페이스에 로컬 컴퓨터에 있는 데이터를 업로드하는 2가지 방법에 대해 설명합니다.
                </p>
            </a>
        </li>
    </ul>
</div>


<div class="card">
    <header>
        <h2 id="card-h2"> ThanoSQL 튜토리얼</h2>
    </header>
    <ul class="fullclick">
        <li>
            <a href="/ko/tutorials/algorithm_list/">
                <h3>
                    ThanoSQL 튜토리얼
                </h3>
                <p>
                    ThanoSQL을 활용한 튜토리얼 예제를 설명합니다. 
                </p>
            </a>
        </li>
    </ul>
</div>

<div class="card">
    <header>
        <h2 id="card-h2"> ThanoSQL 참조</h2>
    </header>
    <ul class="fullclick">
        <li>
            <a href="/ko/how-to_guides/reference/">
                <h3>
                    ThanoSQL 참조
                </h3>
                <p>
                    ThanoSQL에서 제공하는 문법 및 기능에 대한 공식적인 요약을 제공합니다. 
                </p>
            </a>
        </li>
    </ul>
</div>

[^1]: 식별 가능한 구조나 아키텍처가 없는 데이터입니다. 텍스트, 음성, 영상 등이 주로 해당되며 사전 정의 된 데이터 모델을 따르지 않으므로 관계형 데이터베이스에 적합하지 않습니다. 하지만 이러한 유연성으로 인해 확장 가능하며 제약이 없습니다. 비정형 데이터를 올바르게 분석하면 다양한 애플리케이션에 사용할 수 있으며 가치 있는 [비즈니스 인텔리전스](https://ko.wikipedia.org/wiki/%EB%B9%84%EC%A6%88%EB%8B%88%EC%8A%A4_%EC%9D%B8%ED%85%94%EB%A6%AC%EC%A0%84%EC%8A%A4) 통찰력(Insight)을 제공할 수 있습니다.

[^2]: 관계형 데이터베이스 관리 시스템(RDBMS: Relational DataBase Management System)의 데이터를 처리하기 위해 설계된 특수 목적의 프로그래밍 언어이며 쿼리(Query) 언어라고 불리기도 합니다.

[^3]: 관계형 데이터 모델에 기초를 둔 데이터베이스를 의미합니다. 관계형 데이터 모델이란, 데이터를 구성하는 데 필요한 방법 중 하나로 모든 데이터를 2차원의 테이블 형태로 표현합니다. 일반적으로 정형데이터를 다룹니다.

[^4]: 대량의 데이터를 실시간으로 분석하기 어렵기 때문에, 실시간으로 획득이 가능한 데이터 테이블과 어떤 특정 정해진 시간에 계산을 미리 해놓은 배치(Batch) 테이블을 결합하여 대용량 데이터에서도 어느 정도의 실시간 분석이 가능하도록 혼합해서 사용하는 방식을 말합니다.
