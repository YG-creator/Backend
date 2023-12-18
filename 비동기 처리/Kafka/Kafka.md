# Kafka

1. 메시징 플랫폼

   Pub/Sub 형태의 메시징 플랫폼은 스트리밍해야 할 이벤트가 여러 가지가 될 때 유용하다. 

   * 중앙의 pub/sub 서버가 모아서 뿌려주므로 커넥션 수가 줄어듦

     emit7 x sub4 = 28 커넥션

     pub7 + sub4 = 11 커넥션 

2. 메시징 플랫폼 종류

   1. 메시지 브로커(RabbitMQ)

      Publisher가 생산한 메시지를 저장했다가, Consumer가 가져갈 수 있도록 하고 이후에는 데이터가 빠르게 삭제된다.

   2. 이벤트 브로커(Kafka)

      Message Broker와 Publisher가 생산한 이벤트를 저장해서 가져가고 난 이후에도 Data & Event를 계속 hold하고 있다. -> 장애 상황이 발생하였을 때 장애 발생시점부터 다시 처리를 시작할 수 있다는 장점이 있다.

2. 장점

   1. Topic 하나를 여러 가지로 나누어 병렬 처리가 가능하다
   2. 브로커의 스케일 아웃이 용이하다는 점에서 확장성에 유리
   3. 전송받은 데이터를 메모리가 아니라 파일 시스템에 반영구적으로 저장한다.

3. 용어

   1. Zookeeper,  Kraft(RAFT)

      메타데이터를 관리하는 코디네이터의 역할을 하는 애플리케이션

   2. Producer

      메시지를 보내는 역할의 클라이언트

      메시지에 key를 추가해서 Topic 안의 여러 Partition에 값을 넣을 때 사용 -> 메시지 순서 설정

   3. Consumer

      메시지를 받는 역할의 클라이언트

   4. Topic

      메시지를 구분하는 주제

      RDBMS의 테이블과 비슷

   5. Broker

      Partition -> Consumer로 데이터 전달해주는 객체

      각각 고유 ID가 있음

   6. Cluster

      Broker들의 모임



# Topic

메시지를 구분하는 주제

1. 특징
   1. 이벤트를 유지해야 하는 기간을 정의해야 함
   2. Kafka의 성능은 데이터 크기와 관련없이 실질적으로 일정하기 때문에 장기간 데이터를 저장하더라도 지장없다.
   3. 여러 데이터 포맷을 지원하므로 JSON, Avro, TXT 모두 보내도 상관없다. 

2. Data Stream

   Topic 내부에 있는 메시지들의 순서

3. Message Serializer 

   객체를 Byte로 데이터 직렬화하는 과정 -> 어떤 데이터 포맷을 가진 값이 오더라도 토픽에 보낼 수 있게 된다.



# Partition

DB의 샤딩과 같은 개념

각각 ID가 있어서 구분 가능

1. 개념
   * DB의 샤딩과 같은 개념
   * 브로커에서 컨슈머로 전달되는 데이터 조각의 단위
   * 

2. 특징
   * FIFO
   * 파티션은 리더 파티션을 지속적으로 복제하여 장애 시에도 지속적인 데이터 처리가 가능함.

3.  Consumer Lag

   Producer Write - Consumer Read

4. 데이터 복제와 ISR (In-Sync Replicas)

   - 파티션 단위로 데이터 복제함

   - Replica는 리더 파티션의 offset과 자신의 offset을 비교하여 차이가 나면 복제

   - ISR 형태를 지향 

     ISR에 속한 리더와 팔로워 파티션이 모두 싱크된 상태

   - OSR (Out-of-sync Replicas) 

     ISR에 포함되지 않은, 동기화되지 않은 복제본



파티션이 리더 파티션을 계속 복제하고 있기 때문에 장애가 일어나더라도 지속적인 처리가 가능하다.

Consumer Lag : Producer Write - Consumer Read



# Producer



# Consumer



# Acknowledgements



# 자소서

1. 기간
   1. 기간이 길다면 왜 그렇게 길었는지
   2. 그 기간동안 뭘 개발했는지?
   3. 어떻게 하면 더 빨리 할 수 있었을까?
2. 주최
   1. 회사
   2. 사이드
3. 기술 스택
4. 프로젝트 설명
5. 어필하고 싶은 부분



# 책

- effective java
- kotlin in action
- modern java in action
- 토비의 스프링
- ddd start
- clean code
- 객체지향의 사실과 오해
- netty in action
- micro service pattern
- 모던 리눅스 관리
- Redis 운영 관리
- 쿠버네티스 입문
- 쿠버네티스 인 액션



- Domain Modeling Made Functional
- 데이터 중심 애플리케이션 설계 (Designing Data-Intensive Application)
- 마스터링 분산 추적 (Mastering Distributed Tracing)