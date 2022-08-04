---
title: ThanoSQL 웹 사용법
---

# __ThanoSQL 웹 사용법__ 

## 시작 전 사전 정보

- 읽는데 걸리는 시간 : 3분
- 마지막 수정날짜 : {{ git_revision_date_localized }}

## __1. ThanoSQL Web 에 접속하기__

- [ThanoSQL](https://www.thanosql.ai/)에 접속하셔서 Login 버튼을 클릭하시면 로그인 화면으로의 진입이 가능합니다.

<a href = "/img/getting_started/img0.png">
      <img src = "/img/getting_started/img0.png"></img>
</a>

!!! note ""
      ThanoSQL은 6월 1일부터 8월 31일 프로모션 기한 동안 제한 없이 모든 사람들이 무료로 이용할 수 있습니다.
      ThanoSQL 사용자들은 콘솔을 사용 하기 위해서는 필수로 회원가입을 진행해야 합니다.


## __2. 회원가입하기__

- 회원이 아닌 사용자께서는 ThanoSQL 로그인 화면에서 "회원가입 하기"를 클릭해 회원가입을 진행합니다.

<a href = "/img/getting_started/img1.png">
      <img src = "/img/getting_started/img1.png" width = 350px></img>
</a>

- 회원가입 진행을 위해서는 이메일 인증과 비밀번호 입력 후, 동의 약관 항목에 체크해야 합니다. 

<a href = "/img/getting_started/img2.png">
      <img src = "/img/getting_started/img2.png" width=350px></img>
</a>

- 이메일 인증 후 "계정 만들기"를 클릭해 회원가입을 완료합니다.

<a href = "/img/getting_started/signup_complete.png">
      <img src = "/img/getting_started/signup_complete.png" width=350px></img>
</a>

## __3. ThanoSQL Console 사용__

- 회원가입을 완료한 후 로그인 페이지로 돌아가 이메일과 비밀번호를 입력해 로그인합니다.
처음 로그인을 진행한 경우, 워크 스페이스를 생성하면 서비스를 사용할 수 있습니다.

!!! note ""
      프로모션 기간 동안에는 계정 당 한개의 워크 스페이스로 제한됩니다.

<a href = "/img/getting_started/img3.png">
      <img src = "/img/getting_started/img3.png" width = 400px></img>
</a>

!!! warning "워크 스페이스 생성시 주의사항" 
      워크 스페이스 생성시 이름을 20자 이하의 영어 소문자와 숫자를 조합하여 사용할 수 있으며, 타인의 워크스페이스와 이름이 중복될 경우에는 생성되지 않습니다. 워크 스페이스 이름의 첫 글자는 반드시 소문자 영어로 시작해야 합니다.

## __4. 워크 스페이스 생성__

- 워크 스페이스를 생성하시고 나면 아래와 같이 입력하신 아이디와 함께 워크 스페이스를 확인하실 수 있습니다. 

<a href = "/img/getting_started/img7.png">
      <img src = "/img/getting_started/img7.png" width = 400px></img>
</a>

- 생성 후, 워크 스페이스 페이지 내에서 열기 버튼을 통해 ThanoSQL Console 내부로 들어갈 수 있습니다.

<a href = "/img/getting_started/img4.png">
      <img src = "/img/getting_started/img4.png"></img>
</a>

## __5. ThanoSQL 워크스페이스__
- ThanoSQL 서비스를 이용하기 위해서는 API 토큰을 사용해야 합니다. API 토큰은 새롭게 발급 받을수 있지만 새롭게 발급 받으면 이전에 발급 받은 토큰은 더 이상 사용 할수 없는 점 유의하시기 바랍니다. 

- 자신의 워크 스페이스에서 오른쪽 상단에 보이는 GET API_TOKEN 버튼을 누르면 자동으로 API 토큰이 생성되며 클립보드에 복사 됩니다. 아래의 쿼리를 통해 정상적으로 서비스를 사용할 수 있습니다. 

- 전반적인 워크스페이스 사용법은 [ThanoSQL 워크스페이스 사용법](/getting_started/hello_ThanoSQL/)에서 확인할 수 있습니다.

```sql
%load_ext thanosql
%thanosql API_TOKEN=<발급받은_API_TOKEN>
```

ex)

```sql
%load_ext thanosql
%thanosql API_TOKEN=eyGVFDdfafddvczs
```

<a href = "/img/getting_started/img6.png">
      <img src = "/img/getting_started/img6.png"></img>
</a>

!!! danger  
      ThanoSQL 워크 스페이스 상에서 ThanoSQL 문법을 사용하기 위해서는 파일 상단에서 항상 위 쿼리를 실행시켜야 합니다. 
