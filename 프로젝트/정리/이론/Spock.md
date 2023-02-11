# 개념

* 사용처 

  테스트 코드 작성

* 사용 언어 

  Groovy(동적 타입 프로그래밍 언어, python과 유사)

* 장점 

  * 함수이름 한글로도 가능

  * 코드블록으로 구분되어있음(given,when,then)

  * Junit 요소들 모두 제공

  * 중복코드 제거 가능(where)

* 사용법
  * `def + "함수이름" + ()` 로 함수 정의
    * given : 테스트에 필요한 값 준비
    * when : 테스트 코드 실행
    * then : 결과 값 검증
    * expect : then과 같으나, when이 필요하지 않음
    * where : 데이터가 다르고, 로직 동일한 경우 중복 코드 제거





# TestContainers

* 사용하는 이유
  * 운영환경과 유사한 DB 스펙으로 독립적인 환경에서 테스트 가능
  * docker로도 가능하나 관리가 번거러워짐

* 사용방법

  1. dependency 추가

     ```
     // spock
     testImplementation('org.spockframework:spock-core:2.1-groovy-3.0')
     testImplementation('org.spockframework:spock-spring:2.1-groovy-3.0')
     ```

  2. application.yml 작성

     1. /test/recources에 application.yml 생성

     2. application.yml 작성

        * Spring JDBC
        * Spring JPA
        * 그 외 

        ```yaml
        spring:
          datasource:
            driver-class-name: org.testcontainers.jdbc.ContainerDatabaseDriver
            url: jdbc:tc:mariadb:10:///pharmacy-recommendation
          jpa:
            hibernate:
              ddl-auto: create
            show-sql: true
        kakao:
          rest:
            api:
              key: ${KAKAO_REST_API_KEY}
        ```

  3. 테스트 코드 작성

     1. Genric container class 작성
        1. Specification 상속
        2. Genric container 생성자 주입
           1. docker image 이름
           2. port 번호
        3. container 시작
        4. 시스템에 property 설정
           1. host
           2. port
     2. CRUD test class 작성
        1. base class 상속
        2. CRUD test 코드

     

* ref) 공식문서

  [test containers 실습](https://www.testcontainers.org/quickstart/spock_quickstart/)

  [mariaDB](https://www.testcontainers.org/modules/databases/mariadb/)

  [싱글톤 containers 실습](https://www.testcontainers.org/test_framework_integration/manual_lifecycle_control/)

  [Generic container 생성](https://www.testcontainers.org/features/creating_container/)

  [JDBC](https://www.testcontainers.org/modules/databases/jdbc/)

