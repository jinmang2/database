# **NoSQL 데이터 모델링 기법 살펴보기**

# 1. NoSQL 데이터 모델링 개념
## Denormalization
>### "비정규화" = 데이터 중복 허용
>    - 쿼리 프로세싱을 간단하게 하거나 최적화하기 위해서 또는, 사용자의
>      데이터를 특정한 데이터 모델에 맞추기 위해서 같은 데이터를 여러
>      도큐먼트나 테이블에 복제하여 중복하는 것을 허용함
>### 비정규화로 인한 트레이드-오프
>    - Query data volume or IO per query VS total data volume
>      (쿼리당 I/O 또는 쿼리 데이터 사이즈 VS 전체 데이터 사이즈)
>    - Processing complexity VS total data volume
>      (쿼리 수행의 복잡도 VS 전체 데이터 사이즈)

- 비정규화를 하면 쿼리 수행을 위한 모든 데이터를 한곳에 모아놓고 쿼리를 수행하기 때문에,
  쿼리 수행을 위한 I/O 숫자를 줄여 전체 성능을 향상시킬 수 있음
- 데이터 모델링 시점에서 데이터 정규화(중복 제거)를 하거나 쿼리 실행 시점에서
  데이터 간의 연속적인 JOIN은 쿼리 프로세스의 복잡도를 증가시킴
- 시스템과 데이터 사이즈가 큰 분산 환경에서는 그 복잡도가 더욱 더 높아짐
- 데이터 비정규화를 하면 쿼리에 필요한 모든 데이터를 한 곳에 쿼리-친화적인 구조로 모아놓을 수
  있기 때문에 전체적인 쿼리 프로세싱을 단순화하고 수행 시간 단축이 가능
- 해당 데이터는 다른 쿼리 수행을 위해 다른 도큐먼트나 테이블에 중복 저장되기 때문에
  사이즈는 필연적으로 증가함

## Aggregates
"유연한 스키마"속성은 복잡하고 다양한 구조의 내부 요소(nested entities)를 가지고 있는 데이터 클래스를 구성 가능케 함
- 1:n 관계를 최소화하여 결과적으로 JOIN 연산을 줄임(수행 시간 단축, 저렴한 비용의 대용량 데이터 지원 가능)
- 복잡하고 다양한 비즈니스 요소를 담을 수 있음 (추후 확장성 및 변동성에 대한 유연한 대응 가능)
```
  대부분의 NoSQL 솔루션은 기본적으로 "유연한 스키마"를 제공함
```
- NoSQL의 schema-less 특성을 이용하면, 데이터 파일을 하나의 테이블로 합칠 수 있음
![title](https://t1.daumcdn.net/cfile/tistory/2212784D5759B40F0A)

## Application Side Joins
- 대용량 데이터에 대해 빠른 응당 성능과 확장성, 가용성을 최우선 목적으로 하기 때문에,
  쿼리 타임 조인을 최대한 피하여 데이터 모델을 구성하는 것을 가정함
- JOIN 대상 데이터에 대해 비정규화, 어그리게이션을 수행할 때 문제가 발생하는 경우
    - JOIN 대산 데이터가 다대다(n:m) 관계를 가짐
    - JOIN 대상 데이터의 수시 변동
- 대상 데이터가 수시로 변경되는 경우, 비정규화와 어그리게이션을 통해서 많은 entity에 해당 데이터의 중복을 허용했기 때문에 
  해당 데이터 업데이트 시에 모두 찾아서 업데이트해야 하기 때문에 많은 비용이 발생함
- 이런 경우, 차라리 변경이 잦은 데이터만을 추려내어 쿼리 타임 조인을 수행하는 것이 대안
    - RDBMS로 구현
    - DB상에서 JOIN을 수행하는 것이 아니라 Application에서 JOIN을 수행하는 것이 그 대안

![title](https://postfiles.pstatic.net/MjAxNzAzMjlfMjc0/MDAxNDkwNzc0MzQxMzgw.Zq4l1HDGTRTky41VciEyddRWlmPKp94t8yUKwjjmz7gg.s88mkFF97FgNU9Udvsg0k5fAYiRccny0jbaL9NCIehMg.PNG.stmoonangel/0329-4.PNG?type=w2)

# 2. 주요 NoSQL 데이터 모델링 기법
## General Modeling Techniques
- Atomic Aggregates
- Enumerable Keys
- Dimensionality Reduction
- Index Table
- Composite Key Index
- Aggregation with Composite Keys
- Inerted Search - Direct Aggregation

## Composite Key
- 하나 이상의 필드를 deliminator를 이용하여 구분 지어 사용하는 방법
- Ordered KV Store의 경우에는 이를 이용하여 order by와 같은 sorting 기능이나 grouping을 구현 가능
- NoSQL N개의 서버로 구성된 클러스터로 동작하고 데이터는 Key를 기준으로 N개의 서버에 나눠서 저장이 됨
- Key를 선정할 때는 전체 서버에 걸쳐서 부하가 골고루 분산될 수 있는 Key를 선정해야 함
    - regular expression을 사용하면 손쉽게 값을 가져올 수 있음
 
![title](https://t1.daumcdn.net/cfile/tistory/2555B440575FEF5522)
 
## Inverted Search Index
- value의 내용을 Key로 하고, Key의 내용을 반대로 Value로 하는 패턴
- 검색엔진에서 많이 사용하는 방법
   - 검색엔진은 사이트의 모든 페이지를 검색 로봇이 검색해서 문서 내의 단어들을
     색인하여 URL(key)에 맵핑해서 저장해놓음
   - 검색은 단어를 Key로 검색하기 때문에, Value에 검색 키워드들이 들어가 있을 경우에는
     효과적인 검색이 불가함
   - 검색 키워드를 키로 해서 URL을 value로 하는 **테이블을 다시 만든 다음**,
     검색 키워드로 검색을 하면 신속하게 검색 키워드를 가지고 있는 URL을 찾아낼 수 있음
- 용도에 따라서 데이터를 재구성

![title](https://t1.daumcdn.net/cfile/tistory/211E6D35575FF7032D)
![title](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=http%3A%2F%2Fcfile9.uf.tistory.com%2Fimage%2F242EDE35575FF7041EADF2)

 
## 계층 데이터 구조 모델링 패턴
- NoSQL은 다양한 데이터 모델이 있지만, 기본적으로 row, column을 가지고 있는 테이블 구조의 저장 구조를 갖음
- 어플리케이션 개발중에는 이런 테이블 구조뿐만 아니라 Tree와 같은 계층형 데이터 구조를 저장해야 할 경우가 있는데,
  NoSQL은 이러한 계층형 구조를 저장하는 것이 쉽지 않음
- RDBMS의 경우에도 이런 계층형구조를 저장하기 위해서 많은 고민을 해왔기 때문에, 솔루션에서 기능적으로 자체 지우너하기도 하고 데이터 모델링을 통해서 계층형 구조를 저장할 수 있음
- NoSQL에서 계층형 구조를 저장하는 기법은 RDBMS에서 사용하는 기법들을 참고하여 구현함

## Tree Aggregation
- Tree 구조 자체를 하나의 Value에 저장하는 방식
- JSON이나 XML 등을 이용하여 트리 구조를 정의하고, Value에 저장함
- Tree 자체가 크지 않고, 변경이 많은 없는 경우에 적합함.
 
>### EX) 계층형 게시판의 답글 Tree 구조
 
![title](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=http%3A%2F%2Fcfile24.uf.tistory.com%2Fimage%2F216B2F37575FFB6B11D243)
 
## Materialized Path
- Tree 구조를 테이블에 저장할 때, root에서부터 현재 노드까지의 전체 경로를 Key로 저장하는 방법
- 구현에 드는 노력에 비해서 매우 효율적인 저장 방식
- Key에 대한 search를 할 때, Regular Expression을 사용할 수 있으면, 특정 노드의 하위 트리 등을 쿼리해 오는 기능 등 다양한 쿼리가 가능
 
![title](https://t1.daumcdn.net/cfile/tistory/2229B63C575FFCB720)
 
# 3. NoSQL 데이터 모델링 예시
## 도메인 모델 파악
- 어떤 데이터 개체가 있는지, 개체간의 관계 분석
- ERD를 그려 도식화
- RDBMS 모델링 접근 방법이긴 하지만, NoSQL에서도 저장할 데이터를 명확하게 이해하기 위해 필수
 
![title](https://t1.daumcdn.net/cfile/tistory/204A5036503724E833)
 
## 쿼리 결과(데이터 출력 형태) 디자인
- 도메인 모델 기반으로 어플리케이션에서 쿼리가 수행되는 결과값을 먼저 정함
- 출력 형식을 기반으로 필요한 쿼리 정의
- 출력 데이터를 기반으로 테이블 추출

![title](https://t1.daumcdn.net/cfile/tistory/12533634503725212F)

## 패턴을 이용한 데이터 모델링
- NoSQL은 Sorting, Grouping, Join 등의 연산을 제공하지 않기 때문에 Get/Put으로만 데이터를 처리할 수 있는 형태로 데이터 모델을 재정의
 
![title](https://t1.daumcdn.net/cfile/tistory/111FC63A5037253522)
 
## 기능 최적화
- 첨부 파일
   - 포스팅에 의존적이며 변경이 적고, 개수가 많지 않기 대문에 하나의 필드에 모아서 저장하는 것이 나음
- 분류에 따른 포스팅 출력
   - 현재는 포스팅 순서대로만 출력 가능
   - 포스팅에 분류 필드를 별도로 넣고, 필드에 따라 where문으로 select할 수 있어야 함(Secondary Index)

![title](https://t1.daumcdn.net/cfile/tistory/113D83395037254A3D)
 
## NoSQL 선정 및 테스트
- 모델링한 데이터 구조를 효과적으로 실행할 수 있는 NoSQL 검토
- NoSQL 특성 분석 및 부하테스트, 안정성/확장성 테스트 수행
- 경우에 따라서는 여러 개의 NoSQL을 복합하여 사용
```
  하나의 NoSQL만으로 모든 데이터를 처리하려고 하지 말고, RDBMS와 혼용하거나
  다른 NoSQL과 연동하여 최적의 시스템을 설계
```

## 선정된 NoSQL에 최적화 및 하드웨어 디자인
- 선정된 NoSQL에 적합하게 데이터 모델을 최적화
- 해당 NoSQL에 맞는 어플리케이션 인터페이스 설계
- **구동시킬 하드웨어 디자인**
```
  반드시 데이터 모델과 내부 아키텍처를 제대로 파악하고,
  그에 맞는 NoSQL을 선정
```

## 결론
- NoSQL은 시스템 개발 시, 데이터 모델링이 80% 이상을 차지함
- 선정한 NoSQL과 어플리케이션 특성에 맞는 데이터 모델링에 집중

# Conclusion
- NoSQL 데이터 모델링
    - 비정규화, 어그리게이션, 어플리케이션 사이드 조인
- 주요 NoSQL 데이터 모델링 기법
    - General Modeling Techniques : Composite Key Index, Index Table 등
    - Hierarchy Modeling Techniques : Tree Aggregation, Materialized Paths 등
- NoSQL 데이터 모델링 절차
    - 도메인 모델 파악, 쿼리 결과 디자인, 패턴을 이용한 데이터 모델링, 기능 최적화,
      NoSQL 선정 및 테스트, 선정된 NoSQL에 최적화 및 하드웨어 디자인
