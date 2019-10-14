# **MongoDB의 기본 개념에 대해 알아보기**
#### 참고 사이트
[Elky Essay, Mongodb 서버 구축 및 아키텍쳐](https://elky84.github.io/2018/09/26/mongodb_architecture/)

# 1. MongoDB 특징
## MongoDB 소개
- 10gen사에서 개발한 솔루션(C++)
- Key-value와 다르게 여러 용도로 사용이 가능 (범용적)
- 스키마를 고정하지 않는 형태
    - 스키마 변경으로 오는 문제 없음
    - 데이터를 구조화해서 json 형태로 저장 (데이터를 key-value화 저장)
- join이 불가능하기 때문에 join이 필요없도록 데이터 설계 필요

## MongoDB 특징
- 메모리맵 형태의 파일엔진 DB이기 때문에 메모리에 의존적
    - 메모리 크기가 성능을 좌우
    - 메모리를 넘어서는 경우 성능이 급격히 저하됨
- 쌓아놓고 삭제가 없는 경우가 적합
    - "로그 데이터"
    - "이벤트 참여 내역"
    - "세션"
- 트랜잭션이 필요한, 금융, 결제, 빌링, 회원벙보 등에는 부적합
    - RDBMS를 활용해야 함

## 도큐먼트 데이터 모델
- 속성의 이름과 값으로 이루어진 쌍의 집합
- 속성은 문자열이나 숫자, 날짜 가능
- 배열 또는 다른 도큐먼트를 지정하는 것도 가능
- 하나의 document에 필요한 정보를 모두 담아야 함
- one query로 모두 해결이 되게끔 collection model 설계를 해야 함
- join이 불가능하므로 미리 embedding 시켜야 함
```
  도큐먼트 데이터: 데이터 설계를 "종이문서" 설계하듯이 설계해야 한다
```

### 도큐먼트 형태의 구조
- comment > 임베디드 되어 안에 또다른 도큐먼트 존재
```
{
    "_id':ObjectId("4c03e856e258c2701930c091"),
    "title":"Wlecome to MongoDB",
    "body":"Today, we're gonna totally rock your world...",
    "published":true,
    "created":"Mon May 31 2010 12:48:22 GMT-0400 (EDT)",
    "updated":"Mon May 31 2010 12:48:22 GMT-0400 (EDT)",
    "tags":["databases","MongoDB","awesome"],
    "comments":[ (
                  "comment_id":"9023091210",
                  "author":"Bob",
                  "email":"bob@example.com",
                  "body":"My mind has been totally blown!",
                  "created":"Mon May 31 2010 12:48:22 GMT (EDT)"
                  )
                ]
}
```

## JSON
- JSON: JavaScript Object Notation
- lightweight data 교환 형식
- 사람이 읽고 쓰기 쉽고, 기계가 파싱하고 생성하기 쉬움
- JavaScript의 Array 문법으로 데이터 구조를 기술하는 방법으로 XML이 가지는 유연성과 구조적 데이터 표현기능을 확보하면서, XML이 가진 오버헤드를 줄이는 방법으로 사요오딤
- 클라이언트에서 처리 퍼포먼스 높음
- XML이 표현하는 구조적인 정보 모두 표현가능
- 송수신 데이터로 XML를 사용하는 것에 비해 훨씬 빠르고, 간편하다.

## JSON 표기법
- 각 객체는 중괄호({})로 시작하고 끝남
- 문자열과 값으로 구성되어 있고 콜론(:)으로 구분
- 각 멤버들은 콤마(,)로 구분
- 값은 object, string, number, array, true, false, null 사용 가능
- 문자의 경우엔 따옴표를 사용하고, 숫자의 경우 따옴표를 사용하지 않음
- 배열의 경우 대괄호([])로 시작하고 끝나며 각 값은 콤마(,)로 구분

```JSON
{
    "firstName":"John",
    "lastName":"Smith",
    "address": {
                    "streetAddress":"21 2nd Street",
                    "city":"New York",
                    "state":"NY",
                    "postalCode":10021
               },
    "phoneNumbers": ["22 555-1234", "646 555-4567"]
}
```

# 2. MongoDB 장점/단점
## MongoDB 장점
- Schema-less 구조
    - 다양한 형태의 데이터 저장 가능
    - 데이터 모델의 유연한 변화 가능 (데이터 모델 변경, 필드 확장 용이)
- Read/Write 성능 뛰어남
- Scale Out 구조
    - 많은 데이터 저장 가능
    - 장비 확장이 간단함
- JSON 구조: 데이터 직관적 이해 가능
- 사용 방법 쉽고, 개발이 편리함

## MongoDB 단점
- 데이터 업데이트 중 장애 발생 시, 데이터 손실 가능
- 많은 인덱스 사용 시, 충분한 메모리 확보 필요
- 데이터 공간 소모가 RDBMS에 비해 많음 (비효율적인 Key 중복 입력)
- 복잡한 JOIN 사용시 성능 제약이 따름
- transactions 지원이 RDMBS 대비 미약함
- 제공하는 MapReduce 작업이 Hadoop에 비해 성능이 떨어짐

## 빅데이터 처리 특화
- Memory Mapped(데이터 쓰기 시에 OS의 가상 메모리에 데이터를 넣은 후 비동기로 디스크에 기록하는 방식)를 사용
- 방대한 데이터를 빠르게 처리 가능
- OS의 메모리를 활용하기 때문에 메모리가 차면 하드디스크로 데이터 처리를 하여 속도가 급격히 느려짐
- 하드웨어적인 측면에서 투자가 필요

## MongoDB 불안정성
- 데이터의 양이 많을 경우
    - 일부 데이터가 손실 가능성 존재
    - 샤딩의 비정상적인 동작 가능성
    - 레플리카 프로세스의 비정상 동작 가능성

## MongoDB vs MySQL
- MongoDB와 MySQL에서 동일한 데이터를 가지고 CRUD를 수행할 때, 대부분의 결과가 MongoDB가 빠르게 나옴
    ```
    CRUD: 대부분의 SW가 가지는 기본적인 데이터 처리 기능인 
          Create(생성), Read(읽기), Update(갱신), Delete(삭제)를 일컫음
    ```
- MongoDB의 경우, Single Node와 Multi node 간에 성능 차이는 거의 없음 (Delete 연산을 제외하고 대부분 Multi Node가 근소하게 빠름)
- MongoDB Multi Node의 Insert 연산 중 연산 실패가 일어나는 경우 발생
- 분산을 목적으로 한 DBMS 선택할 경우, RDBMS에 비해 낮은 비용과 빠른 성능을 제공하는 MongoDB가 유리함

## MongoDB vs MySQL Server 2008
- 쓰기 작업 성능 비교: MongoDB가 100배 이상 빠름
- 읽기 작업 성능 비교: MongoDB가 평균 3배 이상 빠름
- 읽기/쓰기 동시 작업 성능 비교: 역시 3배 이상 빠름

# 3. MongoDB 주요 기능
## MongoDB Query
1. C (Create)<br>
`db.person.save({'name':'john'});`
2. R (Read)<br>
`db.persone.find();`
3. U (Update)<br>
`db.users.update({name:'johnny'}, {name:'Cash',languages:['Korean']});`
4. D (Delete)<br>
`db.users.remove({name:'Sue'});`

## MongoDB 인덱스
- 다수 인덱스 설정 가능
- 복합 인덱스 지원
- 빠른 검색 지원
- 도큐먼트에 저장된 데이터와 중복 저장 문제
- 메모리가 부족한 시스템에서는 검색 속도 저하 문제

## MongoDB 복제
- Master-Slave 구조 구성
- 데이터 복사본을 Slave에 배치
- Master 장애에 따른 데이터 손실없이 Slave 데이터 사용 가능
- Master 장애가 발생했을 때, Slave에서 Master를 선출가능 (**중단없는 서비스 가능**)
- 데이터 손실을 최소화하기 위해 저널링 지원 (MongoDB의 데이터 변화에 따른 모든 연산에 대한 로그 적재)

## MongoDB 샤딩
- 대용량 데이터를 저장하기 위한 방법
    - 소프트웨어적으로 데이터베이스를 분산시켜 처리하는 구조
    - [왜 샤딩(Sharding)이 필요할까요?](https://nesoy.github.io/articles/2018-05/Database-Shard)
    ```
      <샤딩(Sharding)>
      Vertica lPartitioning: 도메인에 따라 쉽게 분리 가능
                             도메인에 영향을 많이 받기 대문에 대부분 application level에서 CRUD 구현
      Sharding: 같은 테이블스키마를 가진 데이터를 다수의 데이터베이스에 분산하여 저장하는 방법
                application leel에서도 가능하지만 database level에서도 가능
                Horizontal Partitioning이라고 볼 수 있음
      샤딩을 적용하기에 앞서,
          - 샤딩 적용 > 프로그래밍, 운영적인 복잡도는 더 높아지는 단점 존재
          - 가능하면 Sharding을 피하거나 지연시킬 수 있는 방법을 찾는 것을 우선해야 함
              - Scale-in: HW가 더 좋은 PC 사용
              - Read 부하가 크면? Cache나 Database의 Replication을 적용
              - Table의 일부 컬럼만 자주 사용한다면?
                  - Vertically Partition
                  - Data를 Hot, Warm, Cold Data로 분리
      샤딩에 필요한 원리
          1. 분산된 데이터 셋에서 Data를 어떻게 Read할 것인가?
          2. 분산된 Database에 Data를 어떻게 잘 분산시켜서 저장할 것인가
              - 분산이 잘 되지 않고, 한 쪽으로 Data가 몰리게되면 자연스럽게 HotSpot이 되어 성능이 느려지게 됨
              - 그렇기 때문에 균일하게 분산하는 것이 중요한 목표
    ```
- 샤딩 방식
    - 데이터베이스가 저장하고 있는 테이블을 단위로 분리하는 방법
    - 데이터베이스가 저장하고 있는 테이블 자체를 분할하는 방법
- 분산 데이터베이스의 전통적인 3계층 구조 지원
    - 응용 계층, 중개자 계층, 데이터 계층
    - 응용 계층은 데이터에 접근하기 위해 중개자를 통해 모든 데이터의 입출력을 처리
    - 추상화된 한개의 데이터베이스가 존재하는 것처럼 운용

![title](https://elky84.github.io/images/mongodb_sharding_internals.jpg)

## MongoDB 맵리듀스
- 대용량의 데이터를 안전하고 빠르게 처리하기 위한 방법
    - 데이터를 분산하여 연산하고 다시 합치는 기술
    - 맵과 리듀스 단계로 나누어 처리하며, 사용자가 임의 코딩 가능
    - 입/출력 데이터는 Key-Value 형태로 구성
- 한대 이상의 하드웨어를 활용하는 분산 프로그래밍 모델
    - 분산을 통해 분할된 조각으로 처리한 다음, 다시 모아서 훨씬 짧은 시간에 계산을 완료
- 대용량 파일에 대한 로그 분석, 색인 구축 검색 등에 활용
- 일괄처리 방식으로 전체 데이터 셋을 분석할 필요가 있는 문제에 적합
    
![title](https://miro.medium.com/max/1260/1*d44Wk6HQCr6s_WROEc1uIg.png)

# Conclusion
- MongoDB
    - 10gen사에서 C++로 개발한 Schema-less NoSQL 데이터베이스. 데이터를 JSON 형태로 관리하며
      빅데이터 처리 속도 향상을 위해 Join을 지원하지 않음
- MongoDB 장점/단점
    - 대용량의 데이터를 신속하게 처리 가능, 시스템 확장 및 장애 대응 가능, 스키마 변경에 따른 리스크가 적음
      분산시스템이기 때문에 데이터 유실 가능성 있음
- MongoDB 주요 기능
    - 전용 Query를 이용한 데이터 입출력, 인덱스 기능, 데이터 복제 및 장애 대응, 데이터 분산 저장(Sharding), 데이터 분산 연산(MapReduce) 기능 등

