# MongoDB와 Node.js의 동작 방식 배우기

# 1. MongoDB 기본 동작 원리
## MongoDB 구성
- MongoDB는 수평적 확장이라는 특징을 가짐
- 한 대 이상의 서버로 구성하는 것이 일반적
- MongoDB는 메모리 사용 가능량에 대비하여 성능이 좌우되기 때문에 독립된 서버에서 실행을 권장
- 기본적으로 MongoDB는 mongod라는 실행파일을 단위로 실행
- 데이터를 한곳에만 저장하면 데이터 손상 시 복구가 불가능하기 때문에 보통 3개의 Replica 단위로 구성하여 데이터를 복제
- mongos 서버를 통해 마치 한대의 데이터베이스 서버처럼 사용

## MongoDB 구성
![title](https://github.com/jinmang2/database/blob/master/mongodb%EA%B5%AC%EC%84%B12.PNG?raw=true)

## MongoDB 설치
- MongoDB는 총 4개의 운영체제를 지원
- MongoDB를사용하는 대부분의 유저는 90%가 리눅스 사용
    - 빅데이터 처리를 위해서는 많은 수의 머신이 필수적으로 필요
    - 운영체제에 대한 비용이 상대적으로 적은 리눅스를 사용
- MongoDB의 철학: "메모리 관리는 운영체제에 마기자"
    - 상대적으로 메모리 관리에 뛰어난 *nix 베이스의 운영체제를 선택
- 윈도우용 MongoDB는 설치 및 사용이 간편함

## MongoDB 설치
- 지원 언어:
    - Java / Java Script
    - Perl / PHP / Python
    - Ruby / Scala / Erlang / Haskell
- 지원 운영체제
    - Windows, Linux, Unix Solaris, Mac OS X
- MongoDB 공식 사이트 (mongodb.org) 접속
- 다운로드 페이지에서 운영체제에 맞는 배포판 선택 및 다운로드
