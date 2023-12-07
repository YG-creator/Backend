# Monolithic

## 개념

모든 종류의 서비스가 하나의 어플리케이션으로 구성되어 있는 아키텍처



## 종류

1. 싱글 모듈
2. 멀티 모듈



## 강점

1. 쉽다, 간단하다 
   1. 배포도 간단하다 
   2. 어디서 패키징해도 상관 없고, 어떻게든 배포만 하면 된다. 
   3. 유지보수가 비교적 쉽다. 
   4. 공통 모듈 등을 활용하기가 매우 쉽다.
2. 비싼 서버 리소스를 최적화해서 사용이 가능하다 



## 사용하는 경우

1. 창업 혹은 새로운 서비스를 시작하는 경우
2. 소수의 팀원으로 빠르게 오픈해야하는 경우 (협업이 적을 수 있는 경우)
3. 클라우드 환경을 사용하기 어려운 경우
4. 금융 등 보안과 안정성이 최고로 중요한 경우 
5. 개발자들의 역량이 비교적 낮을 경우 
6. Devops 등 전문 인력이 부족할 경우 
7. CI/CD 파이프라인 등의 구축이 어려울 경우



## 단점

1. 수평 확장이 너무 어렵다. (Scale Out). 
2. 단일 DB에 대한 의존성이 너무 크다. 
3. 모놀리식 환경이 보편화되고, 시스템이 커질 수록 커뮤니케이션 코스트가 기하급수적으로 늘어난다. 
4. 규모가 커질 수록, 복잡성이 증가하여 수정에 대한 부담 증가 
5. 대규모 어플리케이션 환경에서는 단순한 수정 사항일지라도, 배포까지 시간이 오래걸릴 수 있다. 
6. 장애 시, 전체 어플리케이션에 영향을 크게 받게된다.



## 안사용하는 경우

1. MSA, Cloud 환경에 충분히 숙련된 개발자들이 많은 경우 
2. 많은 서비스가 식별이 되어 동시 다발적으로 개발을 진행하는 경우 
3. 보안과 안정성보다는 빠른 기능 개발과 배포가 중요한 서비스 
4. Devops 등 전문 인력이 충분하고, 개발자들이 CI/CD 파이프라인에 대해서 어느정도 이해가 있는 경우



## 결론

현실적인 문제들이 없다면 무조건 MSA 

1. 안정성, 편의성
2. 확장성, 애자일, 유연성



# MSA

## 등장 배경

Cloud + 컨테이서 가상화 + 오케스트레이션 기술들의 발전으로 MSA가 일반적일 수 있는 상황이 만들어짐



## Monolithic, SOA, MSA 비교

모놀리식 SOA - 하나의 어플리케이션이 하나의 서버에 배포 - 단일 코드베이스 - 싱글/멀티 모듈 방식으로 개발은 가능하 지만, 근본적으로 하나의 프로세스 - "서비스" 단위로 개발하고, 서비스 간 규격화된 프로토콜 (인터페이스) 를 사용하여 통신 - 대개 동일한 기술 스택들을 가지고 서비스들을 개발하며, 각 서비스들간의 재사용이 목적. - ESB(Enterprise Service Bus) 라는 개념을 통해, 요청에 대해 어떤 서비스들을 호출할 지 캡슐화 된 Layer 존재 - 서비스 간 통합을 강조



공통점 - Shared DB 사용 - 대개 원 코드베이스 - 모듈/서비스 간 규격화된 호출 방식 사용 (서비스 간 통합 관점) - > 모듈/서비스 간 강한 의존성, 결합도 SOA 만의 특징, 차이점 - 독립적으로 배포 가능 - 비즈니스 로직에 따라, 어떤 서비스를 호출 할 지 결정하는 Layer 존재 - 트랜잭션 구현은 별개로 해야 함. - ESB의 관리 (Integration of Monolithic) -> MSA 의 원칙을 정면으로 반박 - 동일 플랫폼(e.g. JVM 환경) 에서 서비스 간 호출을 하다보니, 성능 이슈 존재



공통점 - 개발의 단위를 "서비스"로 인지 - 다른 서비스와 독립적으로 개발, 배포 가능 MSA 만의 특징, 차이점 - 비즈니스 로직(서비스)의 재사용 지양 - 서비스 간 결합도를 낮추는 것이 목표 - 낮은 결합도로 변화에 애자일한 대응 가능 - 각 서비스는 각 서비스의 특성에 맞는 최적 의 기술스택을 독립적으로 선택 가능 - 서비스 간 자유로운 방식으로 통신 가능 - HTTP, gRPC, ...



## 사용 이유

요구사항 변화에 따른 빠른 대처 가능해짐(협업, 커뮤니케이션, 확장성, 유연성, 결합도, 응집도, 빌드와 배포가 쉬워지고 간단해짐)





## 핵심

1. Business Capabilities 

   조직이 얼마나 빠르고 유연하게 변화에 대응할 수 있는지에 대한 능력

2. Product, Not Project

   회사 한 곳에서 개발 + 유지보수 다함

   1. Smart endpoints and dumb pipes

      파이프는 가장 단순하게 라우팅만

   3. Decentralized Data Management / Governance

      비즈니스 필요성에 따라 최적의 기술스텍 사용

   4. Infrastructure Automation

      CI/CD 같은 인프라 자동화

3. Design for Failure

   실패를 고려한 설계

   1. 감지(Circuit break)

   2. 복구(K8S)

   3. 의도치 않은 결과 방지(Transaction, Event Driven)

   4. 서비스 간의 영향도(Chaos Test)

   5. Evolutionary Design 

      비즈니스에 따라 빠른 출시와 지속적인 피드백을 가능하게 하는 설계, 개발



## 단점

1. 서비스간 통신(IPC)

   1. 개발/운영

      호출, 모니터링, 실패시 전략, 타서비스간 통신

   2. 빌드, 배포

      다른 서비스에서 사용중이면 배포 안됨

2. 트랜잭션

   1. roll back

      실패시 rollback이 안되서 보상 트랜잭션을 해줘야 됨

   2. Data Query

      DB 나눠져 있어서 join 연산이 번거러움

3. 모니터링
   1. 인프라 상태(CPU, Memory)
   2. 네트워크 상태(Latency)
   3. 애플리케이션 상태(Error, Warning, JVM 상태)



# MSA로 전환

필요성과 가능여부를 따져서 전환해라. 무조건 좋은게 아님



## 진단

### 필요성



### 가능여부 진단



## MSA 전환 패턴

### 분해

1. business capabilities
2. DDD

### 통신

1. 동기(HTTP, gRPC)
2. 비동기(MessageQueue)

### 트랜잭션

1. 2PC

   2번 commit 함

2. 보상 트랜잭션

   실패한 서비스에서 보상 트랜잭션 날려서 원상 복귀하기

3. Saga

   2PC + 보상 트랜잭션

   처음 commit 때 saga 생성 -> Good 신호가 안들어오면 보상 트랜잭션 자동 실행

### 데이터 쿼리

필요한 데이터 얻기 (join)

1. API Aggregation

   필요한 데이터를 얻어오기 위해서, 분리된 서비스들 각각에 각 도메인에 대한 데이터를 요청 후 필요에 맞게 Aggregation

2. CQRS

   1. Command(Write, Update, Delete) 작업과, Query(Read) 작업의 Endpoint 를 분리
   2. Command 에서 발생된 데이터의 변경을 이벤트 발행을 통해 원하는 포맷대로 Query 를 위한 전용 데이터 구조를 만들어 이곳에서 복잡한 Query를 담당

### 가시성

모니터링

1. 가시성

   로깅 및 메트릭의 중앙집중 및 필터링 등을 통한 한곳에서의 모니터링

   어떻게 로깅, 메트릭을 저장하고 인덱싱하여 검색할 지에 집중하는 패턴

   1. 로깅 : 트러블 슈팅이 목적, 누락되면 안됨
   2. 메트릭 : 시간에 따라서 증감 alert 목적, 누락되어도 됨

### 신뢰성

장애 복구, 자가 치유, 무정지 배포

1. 서킷 브레이커 (장애 복구 + 자가 치유)

   분산 시스템에서 장애 전파를 막고 피해를 최소화 하기 위한 패턴

   * C 서버에 장애가 있다면 -> C서버 요청 경로만 미리 차단 + 나머지 요청 응답 변경

   * C 서버가 복구 된다면 다시 복구 

2. Graceful Shutdown (무정지 배포)

   배포할 때 트래픽 안오게 함 -> 다 배포하면 Warm up으로 확인 -> 다시 트래픽 오게 함

### 그외

1. 테스트 페턴

   단위테스트, 통합테스트, E2E 테스트(종단간 테스트)

2. 외부 API 패턴

   서비스간 종속성을 해결하기 위한 패턴

   리버스 프록시 역할을 하는 인터페이스를 사용하여 우연성 제공

3. 디스커버리 패턴

   수 만은 서비스들이 정상적으로 작동하는지 판단하고, 상황에 따라 적절한 동작을 하는 패턴

   K8S의 Rediness Probe(주기적으로 down 됐는지 확인 -> 다운되면 강제 재시작), Liveness Probe(주기적으로 작동하는지 확인 -> 안되면 강제 재시작)



# 소프트웨어 아키텍처

## MSA와 소프트웨어 아키텍처 관계

MSA -> Micro service x n -> 소프트웨어 아키텍처 x m x n 

소프트웨어 아키텍처 집합임



## 종류

### Layered Architecture

모놀리스 구조에 적합한 아키텍처

* 계층화 아키텍처

* 구현에 대한 로직 의존성은 단방향, 접근은 양방향

* 적합한 곳

  * 모놀리스 구조와 계층별로 구분된 일반적인 프로젝트 조직구조
  * 큰 비즈니스 로직의 변화가 존재하지 않는 곳

  

### Hexagonal Architecture

MSA 구조에 적합한 아키텍처

* Adapter + Port
  * Adapter : 서비스 입장에서 이 서비스가 사용하는 외부 시스템과의 상호작용을 처리
  * Port : 비즈니스 로직입장에서 어댑터와 통신하기 위한 인터페이스
* 단방향 접근만 가능
* 높은 확장성과 유연성





# Hexagonal Architecture 실습

1. 프로젝트 생성
2. 패키지 설계
3. Contoller 개발
4. 도메인 개발