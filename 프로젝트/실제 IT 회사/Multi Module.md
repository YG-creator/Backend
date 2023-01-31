# 사전 지식

* Spring Bean : Spring IoC 컨데이너가 관리하는 객체

* Spring Component scan : 빈으로 등록 될 준비를 마친 클래스들을 스캔하여, 빈으로 등록해주는 것

* @RestControllerAdvice : @ControllerAdvice(예외 처리) + @ResponseBody(응답으로 객체 반환)

​	

# Multi Module

* 개념

  * 필요한 기능별로 Module 생성

  * Multi Module = N개의 모듈 조립 

* 사용이유

  * 중복된 Entity를 Module 화 시켜 ->  중복관리 risk를 줄임

* 사용법

  1. API 모듈 생성

     1. root project에 생성
     2. dependencies
        * lombok
        * Sprint Web
     3. application.yml로 수정
     4. 모듈 실행해보기

  2. Common 모듈 생성

     1. root project에 생성
     2. dependencies
        * lombok
     3. application.yml로 수정

  3. 모듈 등록

     1. root project의 setting.gradle에 모듈 작성

        ```java
        include 'module-api'
        include 'module-common'
        ```

     2. application 실행해보기

  4. API 모듈에 Common 모듈 의존성 설정

     1. dependency 추가

        ```java
        implementation project(':modul-common')	// root project -> settings.gradle에 선언한 값과 동일해야함
        ```

     2. module-api의 setting.gradle 삭제

     3. gradle 새로고침

     4. application 실행

  5. component scan 설정

     * application 위치 이동(비추) 

       공통 패키지까지 이동

     * Annotaion(추천)

       SpringBootApplication(scanBasePackages = {"bean등록한 패키지 위치"})

  6. test

     1. Spring java 객체에서 module 참조 확인

        1. model-common에 공통 entity 생성

           1. enum(@AllArgsConstructor, @Getter)

        2. model-api에 controller, service 구현

           1. controller

              1. lombok
                 1. @RestController : controller bean
                 2. @RequiredArgsConstructor : service bean 주입

              2. service 주입

              3. getMapping

              4. service 함수 사용

           2. service

              1. lombok 
                 1. @Service : service bean

              2. model-common enum 값 출력해보기

     2. Spring Bean에서 module 참조 확인 - component scan 설정 필요

        1. model-common에서 service 생성
           1. @Service : service bean 생성
           2. 아무 문자나 출력

        2. model-api service

           1. @RequiredArgsConstructor : common service bean 주입을 위해서

           2. module-common service bean주입

           3. module-common service 사용

        3. application 실행 -> 에러 - component scan은 application 하위 패키지만 읽어옴

        4. component scan 설정
           1. application 위치 이동
           2. Annotaion 사용
              1. SpringBootApplication
                 1. scanBasePackages = {"bean등록할 패키지의 상위 패키지 위치"}

  

  

# Exception 핸들링

* 사용하는 이유

  예외처리 custom + responseBody

* 사용법

  model-api 모듈에서 진행

  1. commonResponse class 생성(code,message,info)

     ```java
     @Getter
     @Setter
     @Builder	// 객체 만들기
     @JsonInclude(Include.NON_NULL)	// json에 not null 인것만 담기
     @NoArgsConstructor
     @AllArgsConstructor
     public class CommonResponse<T> {	// 통일 규격
         private String returnCode;
         private String returnMessage;
         private T info;
     	
         // 생성자
         public CommonResponse(CodeEnum codeEnum) {
             this(codeEnum, null);
         }
     
         public CommonResponse(T info) {
             this(CodeEnum.SUCCESS, info);
         }
     
         public CommonResponse(CodeEnum codeEnum, T info) {
             setReturnCode(codeEnum);
             setInfo(info);
         }
     }
     ```

  2. controller mapping

     ```java
     @GetMapping("/exception")	// GET Mapping
     public String exception() {
         return demoService.handleCustomException();	// service 사용
     }
     ```

  3. service 예외처리

     ```java
     public String handleCustomException() {
         if (true) {
             System.out.println("Throw CustomException");
             throw new CustomException(CodeEnum.NOT_IDENTITY_VERIFIED_USER);	// CustomException 사용
         }
     
         return "exception";
     }
     ```

  4. 예외처리 custom + responseBody

     ```java
     @RestControllerAdvice	// 예외처리 + responseBody
     public class GlobalExceptionHandler {	// Exception handler
     
         @ExceptionHandler(CustomException.class)	// custom 예외처리
         public CommonResponse<?> handlerCustomException(CustomException e) {
             return getCommonResponse(e.getReturnCode(), e.getReturnMessage());
         }
     
         @ExceptionHandler(Exception.class)	// 그 외 예외처리
         public CommonResponse<?> handleException(Exception e) {
             log.error("Unknown Exception", e);
             return getCommonResponse(CodeEnum.UNKNOWN_ERROR.getCode(),
                                      CodeEnum.UNKNOWN_ERROR.getMessage());
         }
     }
     ```

  

# DB 연동

* 사용법

  module-common 모듈에서 진행

  1. dependency 추가

     ```java
     api 'org.apringframework.boot:spring-boot-starter-data-jpa'
     implementation 'mysql:mysql-connector-java'
     ```

  2. plugin 추가

     ```java
     id 'java-library'
     ```

  3. applicaiont.yml 설정

     ```yaml
     spring:
       main:
         allow-bean-definition-overriding: true
       datasource:
         url: jdbc:mysql://{{HOST}}:{{PORT}}/{{DATABASE_NAME}}
         username: {{USER_NAME}}
         password: {{PASSWORD}}
         driver-class-name: com.mysql.cj.jdbc.Driver
       jpa:
         database: mysql
         database-platform: org.hibernate.dialect.MySQL5InnoDBDialect
         hibernate:
           ddl-auto: none # or `create`
         properties:
           hibernate:
             show_sql: true # to System OutDataSourceConfiguration
             format_sql: true
             use_sql_comments: true
             jdbc:
               time_zone: Asia/Seoul # @CreatedDate 필드의 Time Zone 값 설정
     ```

  4. 도메인, repository 구현, bean 등록

     1. domain

        ```java
        @Getter
        @Entity	// entity bean 등록
        @Builder
        @AllArgsConstructor(access = AccessLevel.PRIVATE)
        @NoArgsConstructor
        public class Member {
        
            @Id
            @GeneratedValue(strategy = GenerationType.IDENTITY)
            private Long id;
        
            @Column
            private String name;
        
        }
        ```

     2. repository

        ```java
        public interface MemberRepository extends JpaRepository<Member, Long> {
        }
        ```

        

  5. service에서 사용해보기
     1. builder로 도메인 entity 생성
     2. respository.save(생성한 Entity)
  6. application에서 도메인, repository bean scan
     * EntityScan("패키지")
     * EnableJpaRepositories(backPackages = "패키지")





# Gradle을 사용한 배포

* Gradle로 Module 배포 가능

* 사용법

  1. jar 파일 설정

     module-common의 build.gradle에서 jar 파일 설정

     1. module-common에서 jar 파일 생성 안함

        참조만 하는 module-common은 실행시킬 필요 없으므로 jar 파일 생성 안함

        ```java
        tasks.bootJar { enabled = false } 
        ```

     2. module-common에서 -plain.jar 파일 생성

        module-common에서는 Api Module에서 사용할 [classes / resources] 가 필요하므로 생성

        ```java
        tasks.jar { enabled = true }
        ```

  2. 빌드

     1. root project에서 Gradle 빌드 명령어를 실행

        `./gradlew clean :module-api:buildNeeded --stacktrace --info --refresh-dependencies-x test`

        * `./gradlew clean` : 현재 경로의 gradlew를 사용해서 기존에 빌드되어있는 폴더 삭제
        * `:module-api:buildNeeded` : module-api를 빌드
        * `--stacktrace` : 로그 보여주기
        * `--info` : 로그 단계(degub > info > warn > error) 중 info 이상을 출력(info,warn,error)
        * `--refresh-dependencies` : dependency refresh
        * `-x test` : 테스트 코드 skip(실패하면 빌드가 안됨)

  3. jar 파일 실행

     1. Api module을 실행시킬 jar 파일이 생성된 경로로 이동

        `cd module-api/build/libs/`

     2. java -jar 명령어를 실행한다.

        `java -jar module-api-0.0.1-SNAPSHOT.jar`

  4. 서버로 실행해보기

     ```java
     curl localhost:8080/save
     curl localhost:8080/find
     ```

  5. 빌드 삭제해보기

     pwd로 root project로 이동 후 `.gradlew clean`

      

     ```java
     /*
     
     - profile 지정 X
       -> java -jar module-api-0.0.1-SNAPSHOT.jar
     - profile 지정 O
       -> java -jar -Dspring.profiles.active=local module-api-0.0.1-SNAPSHOT.jar
      */
     ```

     

# Profile

* 필요한 이유

  환경별로 설정해야 하는 Property값들이 다르기 때문

* 사용 방법

  1. module-api의 application.yaml 이름 변경

     ex) `application-{env}.yaml`

  2. application.yaml에서 이름 설정

     ex) `profile-name: "{env}"`

  3. 사용

     1. 환경별 Property 값 사용

        service에서 profile-name 값 사용해보기

        ```java
        @Value("${profile-name}")
        private String name;
        
        public void test(name) {
            System.out.println(name);
        }
        ```

     2. Intellij 실행 시 Profile 적용

        1. 우측상단 Edit Configurations 

        2. Active profiles에 이름 입력

     3. Jar 실행 시 JVM 옵션으로 Profile 적용

        1. root project에서 빌드 명령어 실행

           `./gradlew clean :module-api:buildNeeded --stacktrace --info --refresh-dependencies-x test`

        2. Api module을 실행시킬 jar 파일이 생성된 경로로 이동

           `cd module-api/build/libs/`

        3. java -jar 명령어를 실행

           `java -jar -Dspring.profiles.active={profile-name} {jar파일 이름}`

           

     