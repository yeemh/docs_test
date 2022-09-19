---
title: ThanoSQL 데이터 업로드하기
---

# __ThanoSQL 데이터 업로드하기__ 

## __개요__

사용자는 아래 두가지 방법으로 데이터를 업로드할 수 있습니다. 

!!! note "데이터를 업로드하는 방법"
    1. 워크스페이스 환경에서 로컬 데이터 업로드하기
    2. 개인키를 사용하여 SFTP 연결하여 로컬 데이터 업로드하기

## __1. 워크스페이스 환경에서 로컬 데이터 업로드하기__

ThanoSQL 워크스페이스에 로컬 데이터를 업로드하려면 다음 단계를 따르세요.
!!! note ""
    1. ThanoSQL 콘솔에 로그인합니다. 
    2. Upload 버튼을 클릭합니다. (Jupyter Lab 방식과 동일)
    3. 업로드할 파일을 선택합니다. 

!!! warning "업로드 주의사항" 
    많은 양의 파일을 다중 선택으로 업로드할 경우 한 번에 많은 연결 요청이 발생하다 보니 문제가 발생할 수 있습니다. 대량의 데이터를 처리할 때는 zip 파일로 업로드하여 사용하시면 조금 더 안전하게 데이터를 업로드할 수 있습니다.

## __2. 원격 시스템에 접속하여 데이터 업로드하기(SFTP)__

!!! note "SFTP(Secure File Transfer Protocol)란?"
    FTP(File Transfer Protocol)와 유사한 사용자 인터페이스가 있는 대화식 파일 전송 프로그램을 의미합니다. 하지만 SFTP는 SSH FTP(File Transfer Protocol)를 사용하여 서버에 대한 보안 연결을 만듭니다. 

!!! danger ""
    - SFTP는 Ubuntu와 Linux 환경만 지원합니다  
    - 명령으로 사용할 수 있는 옵션 중 일부는 SFTP 명령에 포함되어 있지 않지만 대부분의 명령이 포함되어 있습니다.

원격 시스템에 접속하여 데이터를 업로드하려면 다음 단계를 따르세요.

### __STEP 1. 개인키와 ID 확인하기__

SFTP 연결을 하기 위해서는 개인키와 ID가 필요합니다. 웹페이지 상단 `내 프로파일`에 들어가면 개인키를 확인 또는 재발급 받으실 수 있습니다. SFTP 접속에 필요한 ID에는 워크스페이스 이름을 입력하세요.

개인키는 다음과 같은 형태로 구성되어 있습니다.

```pem
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaCtdjEAAAAABG5vbmUtesttesttestteZQAAAAAAAAABAAABlwAAAAdzc2gtcn
NhAAAAAwEAEAwf0AHzaPEz3m81l56ScriVThc4v/9/erUZQtaRkT6TRasdasdrY96O4AZE
3bprMXloa1d73wESoGasdsaneoznug3XEufi2r3Iom7QKS1yF90sf3SSsaU8LGadMLQ5fd
VEHDk2K94Y6120IH/SUs8mKnh1DiasdoplgbLrWv90TYLl7WAInyruads0hYQTedZhdWYz
WLZlSO0bac+8N8SPHLFr2VgKUmzZatpdasJwIk6nAP23aYuYD5GIH/cEQvvquhSNsaHKhm
AdnUS6MlSQxD2LOnBEPNdadzzseJs9fPJtR0Zgdw9sFzm24c/0ixxs7RUd+56hbyviadow
iAkUmA6BXP49vK4VcF9asdGTKCdoEvDBwXJ+fEpAAAFkAFunosBbp6LAAAAB3NzaC1yca2
iAkUmA6BXP49vK4VcF9asdGTKCdoEvDBwXJ+fEpAAAFkAFunosBbp6LAAAAB3NzaC1yca2
iAkUmA6BXP49vK4VcF9asdGTKCdoEvDBwXJ+fEpAAAFkAFunosBbp6LAAAAB3NzaC1yca2
iAkUmA6BXP49vK4VcF9asdGTKCdoEvDBwXJ+fEpAAAFkAFunosBbp6LAAAAB3NzaC1yca2
EAAMH9AB82jxM95vNZeeknK4lU4XOdas/dE2C5e1gCJ8q7tIWEE3mYXVmM1i2ZUjtAEdhB
KG3Ejxyxa9lasdaYClJadasybUdGYHcPbBc5tuHP9IscbO0VHfueoW8r4qMIgBUBYip1BR
7lEc2xXpOA23asdasdgzxMzUGvpuzvBJg0oeW3J6NiDtOVdyKnUpjo6Off5QTeoRRQ1+Sd
0HKZ32q7f0yozK9gRublbeilsTzEkWhvnDz7aobfsTiN2JXvGp3rx7manR4adsasds/JrM
u9O1QqfvqUTcd++ax1WGk5CPvsCYLninZXXqR7y8IQ5HWRIFEBsI4rLfoJmmKZgepTa2zw
d0mmCMjP5HzeikNoLJhyunlOTUUtoIzwAAAMEAyUD2N93Iwf09rlNYrOURANjXJ7JLnVOT
iqgFRgzG7ELjBlVknasdasdVyQSwXqPQeV0LKqIK/pro3u84c20U9G3Hj6/RU/spVgMFLu
yQmkvxp//a7emwLtUgOa76IYckBePZUduaELPWdjVOliajZyOEIlUPslD7cTr1zLffhNBx
l2J5BknmWy4lng3m44vE2f7ZmDCdmopK6EXQs3vPxbJkaROUQTEqQAsBFLU1KkkgAJDkGc
kp1GuW0kIgRvSHAAAAE3Jvb3RAdGVzdGluZy1zZXJ2ZXIBAgMEBQYHasdasdasdasdsdww
-----END OPENSSH PRIVATE KEY-----
```
### __STEP 2. 로컬에 개인키 등록하기 - pem 파일 생성__

로컬 환경에서 vim 명령어를 사용하여 `key.pem` 파일을 생성해 줍니다. 생성된 파일에 이전 단계에서 발급받은 개인키를 붙여넣습니다. 

```bash
$ vim key.pem
```

### __STEP 3. pem 파일 권한 변경하기__

로컬 환경에서 개인키가 등록된 pem 파일의 권한을 read로 변경해줍니다.

```bash
$ chmod 400 key.pem
```

### __STEP 4. SFTP 연결하기__
로컬 환경에서 파일 권한이 변경 완료되면 SFTP에 접속해 줍니다.
!!! warning ""
    SFTP에 접속하기 위해서는 pem 파일의 절대 경로와 STEP 1에서 확인한 워크스페이스 이름이 필요합니다.

```bash
sftp -i [pem 파일 절대경로] [워크스페이스 이름]@engine.thanosql.ai
```

연결되면 아래와 같은 화면을 확인하실 수 있습니다.

[![IMAGE](/img/thanosql_syntax/connecting/img1.png)](/img/thanosql_syntax/connecting/img1.png)

### __STEP 5. 데이터 파일 접속하기__

SFTP 접속이 완료되었다면 현재 위치를 파악하고 권한이 부여된 'drive' 폴더에 접속합니다. ('drive' 폴더 외에는 권한이 부여되지 않아 접속이 불가합니다.)

```bash
sftp> pwd
sftp> cd /drive
```

### __STEP 6. 폴더나 파일 전송하기__

원격 작업 디렉토리와 로컬 작업 디렉토리를 확인하고 파일을 전송합니다.

```bash
put image_folder
```

위 단계들을 거쳐 원격 작업 디렉토리인 `/drive`에 로컬 작업 디렉토리인 `image_folder` 안에 있는 이미지 파일들을 전송할 수 있습니다. 


### __(Optional) SFTP 명령어 테이블__
|명령|설명|
|:---|:---|
|sftp remote-system|원격 시스템에 대한 sftp 연결을 설정합니다.|
|sftp remote-system: file|remote-system에서 명명된 file을 복사합니다.|
|bye|sftp 세션을 종료합니다.|
|help|sftp 명령을 모두 나열합니다.|
|ls|원격 작업 디렉토리의 내용을 나열합니다.|
|lls|로컬 작업 디렉토리의 내용을 나열합니다.|
|pwd|원격 작업 디렉토리의 이름을 표시합니다.|
|cd|원격 작업 디렉토리를 변경합니다.|
|lcd|로컬 작업 디렉토리를 변경합니다.|
|mkdir|원격 시스템에서 디렉토리를 만듭니다.|
|rmdir|원격 시스템에서 디렉토리를 삭제합니다.|
|get|원격 작업 디렉토리에서 로컬 작업 디렉토리로 파일을 복사합니다.|
|put|로컬 작업 디렉토리에서 원격 작업 디렉토리로 파일을 복사합니다.|
|delete|원격 작업 디렉토리에서 파일을 삭제합니다.|

<br>