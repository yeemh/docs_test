---
title: 인증 가능한 토큰 만들기
---

# __인증 가능한 토큰 만들기__

## 시작 전 사전 정보

- 마지막 수정날짜 : {{ git_revision_date_localized }}

ThanoSQL의 REST API를 사용하기 위해서는 자신만의 토큰을 발행해야 합니다. 토큰을 발행하기 위해서는 [ThanoSQL 웹 페이지](https://thanosql.ai)를 방문하여 밑의 절차에 따라 간단하게 받아보실수 있습니다. API 토큰은 새롭게 발급 받을수 있지만 새롭게 발급 받으면 이전에 발급 받은 토큰은 더 이상 사용 할수 없는 점 유의하시기 바랍니다. 

먼저 [ThanoSQL 웹 페이지](https://thanosql.ai)에서 로그인 후 오른쪽 상단에 보이는 콘솔 버튼을 누르고 자신의 워크스페이스에 들어갑니다. 

![IMAGE](/img/thanosql_api/restapi_token_img1.png) </br>

자신의 워크스페이스에서 오른쪽 상단에 보이는 GET API_TOKEN 버튼을 누르면 자동으로 API 토큰이 생성 되어 클립보드에 복사 됩니다. 생성된 API 토큰을 이용하여 ThanoSQL의 모든 REST API를 사용하실수 있습니다. 

![IMAGE](/img/thanosql_api/restapi_token_img2.jpg) </br>

!!! danger  
    API 토큰은 새롭게 발급 받을수 있지만 새롭게 발급 받으면 이전에 발급 받은 토큰은 더 이상 사용 할수 없는 점 유의하시기 바랍니다. 