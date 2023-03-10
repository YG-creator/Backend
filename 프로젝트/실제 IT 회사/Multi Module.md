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

## Module n개 생성

1. API 모듈 생성

   1. root project에 Module 생성(module-api)
   2. dependencies 설정
   3. application.yml로 수정
   4. settings.gradle 삭제(의존성 설정 시 가장 먼저 확인하므로 삭제)

2. Common 모듈 생성

   1. root project에 Module 생성(module-common)
   2. dependencies 설정
   3. application.yml로 수정
   3. application 삭제(사용 안하므로)

3. 모듈 등록

   1. root project의 setting.gradle에 모듈 작성

      ```java
      include 'module-api'
      include 'module-common'
      ```

   2. mutlimodeul의 application 실행 확인



## 의존성 설정

1. API 모듈에 Common 모듈 의존성 설정

   1. module-api build.gradle에서 설정

      1. dependency에 의존성 추가

         ```gradle
         implementation project(':module-common')	// root project -> settings.gradle에 선언한 값과 동일해야함
         ```

      2. 'prepareNotKotlinBuildScriptModel 에러'를 방지 하기위해서 추가

         ```gradle
         tasks.register("prepareNotKotlinBuildScriptModel") {}
         ```

   2. build 확인

   3. application 실행 확인

2. component scan 설정

   multimodule application에서 설정

   * application 위치 이동(비추) 

     공통 패키지까지 이동

   * Annotaion(추천)

     SpringBootApplication(scanBasePackages = {"bean등록한 패키지 위치"})



## 의존성 테스트(안중요)

1. Spring java 객체에서 module 참조 확인

   1. model-common에 공통 entity 생성

      1. enum(@AllArgsConstructor, @Getter)

   2. model-api에 controller, service 구현

      1. controller

         1. lombok
            1. @RestController : controller bean
            2. @RequiredArgsConstructor : final or @NotNull이 붙은 필드의 생성자 자동 생성

         2. service 주입

         3. getMapping

         4. service 함수 사용

      2. service

         1. lombok 
            1. @Service : service bean
         2. model-common enum 값 출력해보기
   3. 웹에서 확인(PostMan)

2. Spring Bean에서 module 참조 확인 - component scan 설정 필요

   1. model-common에서 service 생성
      1. @Service : service bean 생성
      2. 아무 문자나 출력
   2. model-api service

      1. @RequiredArgsConstructor : common service bean 주입을 위해서

      2. module-common service bean주입

      3. module-common service 사용
   4. component scan 설정
      1. application 위치 이동
      2. Annotaion 사용
         1. SpringBootApplication
            1. scanBasePackages = {"bean등록할 패키지의 상위 패키지 위치"}

   4. application 실행 후 웹에서 확인



# Exception 핸들링

model-api 모듈에서 진행



## ResponseDto 작성

response 규격화

```java
@Getter
@Setter
@Builder
@NoArgsConstructor
@AllArgsConstructor
@JsonInclude
public class ResponseDTO<T> implements Serializable {	// 직력화 - 네트워크 전송을 위해 바이트 코드로 변환

  private ResultObject result;

  private T data;

  public ResponseDTO(ResultObject result) {
    this.result = result;
  }

  public ResponseDTO(T data) {
    this.data = data;
  }

  public static <T> ResponseDTO<T> ok() {
    return new ResponseDTO<>(ResultObject.getSuccess());
  }

  public static <T> ResponseDTO<T> ok(T data) {
    return new ResponseDTO<>(ResultObject.getSuccess(), data);
  }

  public static <T> ResponseDTO<T> response(T data) {
    return new ResponseDTO<>(ResultObject.getSuccess(), data);
  }

  public ResponseDTO(BaseException ex) {
    this.result = new ResultObject(ex);
  }
}
```



## ErrorCode 작성

custom

```java
public enum ErrorCode {	
	private final int status;
    private final String message;
    //400 BAD_REQUEST 잘못된 요청
    INVALID_PARAMETER(400, "파라미터 값을 확인해주세요."),

    //404 NOT_FOUND 잘못된 리소스 접근
    DISPLAY_NOT_FOUND(404, "존재하지 않는 전시회 ID 입니다."),
    FAIR_NOT_FOUND(404, "존재하지 않는 박람회 ID 박람회입니다."),
    FESTIVAL_NOT_FOUND(404, "존재하지 않는 페스티벌 ID 페스티벌입니다."),
    SAVED_DISPLAY_NOT_FOUND(404, "저장하지 않은 전시회입니다."),
    SAVED_FAIR_NOT_FOUND(404, "저장하지 않은 박람회입니다."),
    SAVED_FESTIVAL_NOT_FOUND(404, "저장하지 않은 페스티벌입니다."),

    //409 CONFLICT 중복된 리소스
    ALREADY_SAVED_DISPLAY(409, "이미 저장한 전시회입니다."),
    ALREADY_SAVED_FAIR(409, "이미 저장한 박람회입니다."),
    ALREADY_SAVED_FESTIVAL(409, "이미 저장한 페스티벌입니다."),

    //500 INTERNAL SERVER ERROR
    INTERNAL_SERVER_ERROR(500, "서버 에러입니다. 서버 팀에 연락주세요!");
}
```



## CustomException 작성

custom 포함시키기

```java
@AllArgsConstructor
@Getter
public class CustomException extends RuntimeException {	// RuntimeException 상속
    private final ErrorCode errorCode;	// ErrorCode 필드 추가
}
```



## GlobalExceptionHandler 작성

에러처리 + responseBody

* 예외 class

  * `MissingServletRequestParameterException.class`: request parameter가 없을 때 에러를 리턴한다.

  * `MissingRequestHeaderException.class`: request header가 없을 때 에러를 리턴한다.

  * `MethodArgumentNotValidException.class`: request body의 데이터가 유효하지 않을 때 에러를 리턴한다.

  * `NoHandlerFoundException.class`: 404 error를 리턴한다.

* protected  or private 사용

  * 외부에서 함수를 부르게 되면 그대로 에러 객체를 리턴

```java
@RestControllerAdvice	// 예외처리 + responseBody
public class GlobalExceptionHandler {	

    @ExceptionHandler(CustomException.class)	// custom 예외처리
    protected CommonResponse<?> handlerCustomException(CustomException e) {
        return getCommonResponse(e.getReturnCode(), e.getReturnMessage());
    }

    @ExceptionHandler(Exception.class)	// 그 외 예외처리
    protected CommonResponse<?> handleException(Exception e) {
        log.error("Unknown Exception", e);
        return getCommonResponse(CodeEnum.UNKNOWN_ERROR.getCode(),
                                 CodeEnum.UNKNOWN_ERROR.getMessage());
    }
}
```



# DB 연동



## 설정

1. module-common의 build.gradle

   1. dependency 추가

      jpa, mysql 추가

      ```java
      api 'org.apringframework.boot:spring-boot-starter-data-jpa'
      implementation 'mysql:mysql-connector-java'
      ```

   2. plugin 추가

      api라는 키워드를 사용하기 위해서 추가

      ```java
      id 'java-library'
      ```

2. modul-api 의 application.yml 설정

   JDBC, JPA 설정

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
         ddl-auto: create # table 없으면 자동 생성, 배포할때는 none 사용
       properties:
         hibernate:
           show_sql: true 
           format_sql: true
           use_sql_comments: true
           jdbc:
             time_zone: Asia/Seoul # @CreatedDate 필드의 Time Zone 값 설정
   ```



## 도메인 구현

module-common에서 진행

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



## repository 구현

module-common에서 진행

```java
public interface MemberRepository extends JpaRepository<Member, Long> {
}
```



## Service 구현

module-api에서 진행

1. Repository 주입
2. Repository 함수 사용



## Controller 구현

module-common에서 구현

1. Service 주입
2. Mapping
3. Service 사용



## Scan 추가

multimodule / application 에서 추가

1. EntityScan("패키지")
2. EnableJpaRepositories(backPackages = "패키지")



# Gradle로 배포

* 원하는 Module 배포



## jar 파일 설정

실행은 안하고 참조만 하는 module-common 에서 진행

1. build.gradl에 추가

   ```java
   // 실행시킬 필요 없으므로 jar 파일 생성 안함
   tasks.bootJar { enabled = false } 
   
   // 참조는 하므로 plain-jar 파일 생성
   tasks.jar { enabled = true }
   ```

   

## 빌드

1. root project에서 Gradle 빌드 명령어를 실행

   `./gradlew clean :module-api:buildNeeded --stacktrace --info --refresh-dependencies-x test`

   * `./gradlew clean` : 현재 경로의 gradlew를 사용해서 기존에 빌드되어있는 폴더 삭제
   * `:module-api:buildNeeded` : module-api를 빌드
   * `--stacktrace` : 로그 보여주기
   * `--info` : 로그 단계(degub > info > warn > error) 중 info 이상을 출력(info,warn,error)
   * `--refresh-dependencies` : dependency refresh
   * `-x test` : 테스트 코드 skip(실패하면 빌드가 안됨)

2. jar 파일 확인

   module/build/libs에 위치



## 빌드 테스트

3. jar 파일 실행

   1. Api module을 실행시킬 jar 파일이 생성된 경로로 이동

      `cd module-api/build/libs/`


   2. java -jar 명령어를 실행한다.

      `java -jar module-api-0.0.1-SNAPSHOT.jar`

3. 서버로 실행해보기

   ```java
   curl localhost:8080/save
   curl localhost:8080/find
   ```

4. 빌드 삭제해보기

​		pwd로 root project로 이동 후 `.gradlew clean`

 

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



## profile 생성

1. application.yaml 이름 변경

   ex) `application-{env}.yaml`

2. 변경한 application.yaml에서 이름 설정

   ex) `profile-name: "{env}"`



## profile 설정

* IntelliJ로 설정 방법

  1. 우측상단 Edit Configurations 

  2. Active profiles에 Profile 이름 입력

  

* Jar 실행 시 JVM 옵션으로 Profile 설정 방법

  1. root project에서 빌드 명령어 실행

     `./gradlew clean :module-api:buildNeeded --stacktrace --info --refresh-dependencies-x test`


  2. Api module을 실행시킬 jar 파일이 생성된 경로로 이동

     `cd module-api/build/libs/`

  3. java -jar 명령어를 실행 + profile 설정

     `java -jar -Dspring.profiles.active={profile-name} {jar파일 이름}`

  

