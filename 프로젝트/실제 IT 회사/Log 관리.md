# Logback란?

* 개념
  * SLF4J 인터페이스 구현하는 구현체
  * Logging Framework

* 사용하는 이유
  * Log 관리



# Appender란?

* ConsoleAppender : 콘솔에 log를 출력(개발 환경) - 이거 해봄
* FileAppender : 파일 단위로 log 를 저장(운영서버 환경) 
* RollingFileAppender : (설정 옵션에 따라) log 를 여러 파일로 나누어 저장 - 이거해봄
* SMTPAppender : log 를 메일로 전송 하여 기록 
* DBAppender: log 를 DB에 저장



# Mdc란?

* 개념

  멀티쓰레드 환경에서 로그를 남길 때 사용하는 개념

* 사용하는 이유

  로그에서 저장되어있는 값을 동적으로 출력하기 위해서



# logback 설정

1. logback-spring.xml 생성

   * resource에 생성

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <configuration>
       <!-- 기본 logback property 가져오기 -->
       <include resource="org/springframework/boot/logging/logback/defaults.xml" />
       <!-- Profile한 설정 가져오기 -->
       <include resource="logback-spring-${spring.profiles.active}.xml"/>
   </configuration>
   ```

2. logback-spring-{profile이름}.xml 생성

   * resource에 생성

   * 여기에 appender 설정 추가하면 됨



# Appender 설정

## ConsoleAppender 설정

1. logback-spring-{profile이름}.xml에서 설정

   1. console-appender properties 가져오기
   2. appender 설정
      1. 이름
      2. class
   3. log level 설정(Debug < Info < Warn < Error)
   4. log 출력 형식

   ```xml
   <included>
       <!-- 1. console-appender properties 가져오기 -->
       <include resource="org/springframework/boot/logging/logback/console-appender.xml" />
       
       <!-- appender 설정 -->
     	<appender name="CONSOLE2" class="ch.qos.logback.core.ConsoleAppender">	<!-- 2. appender 설정 -->
       	<filter class="ch.qos.logback.classic.filter.ThresholdFilter">
               <!-- 3. log level 설정(Debug < Info < Warn < Error)-->
               <level>INFO</level> 
       	</filter>
       	<layout>
         		<pattern> <!-- 4. log 출력 형식 -->
           		[CONSOLE2] [%-5level] %d{yyyy-MM-dd HH:mm:ss} [%thread] [%logger{0}:%line] - %msg%n
         		</pattern>
       	</layout>
     	</appender>
   </included>
   ```



## RollingFileAppender 설정

1. logback-spring-{profile이름}.xml에서 설정

   1. appender 설정
      1. 이름
      2. class
   2. 파일 위치 및 이름 설정
   3. rollingPolicy 설정
      1. 로그파일 위치 및 이름 패턴
      2. 로그파일의 최대 크기
      3. 로그파일 최대 보관 주기(단위 : 일)
   4. encoder 설정
      1. 로그 출력 형식
      2. header에 로그 출력 형식 노출
   5. Filtering
      1. level 설정
      2. level 일치하는 것만
   
   
   ```xml
   <included>
       <!-- 1. RollingFileAppender 설정 -->
       <appender name="REQUEST1" class="ch.qos.logback.core.rolling.RollingFileAppender">
       	<file>log/request1.log</file> <!-- 2. 파일 위치 및 이름 설정 설정 -->
       	<rollingPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedRollingPolicy">	<!-- 3. rollingPolicy 설정 -->
         		<fileNamePattern>log/archive/request1.%d{yyyy-MM-dd}_%i.log</fileNamePattern>
         		<maxFileSize>1KB</maxFileSize> <!-- 로그파일의 최대 크기 -->
         		<maxHistory>30</maxHistory> <!-- 로그파일 최대 보관주기(단위 : 일) / 보관주기가 넘어가면 파일은 자동으로 삭제 -->
       	</rollingPolicy>
       	<encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder"> <!-- 4. encoder 설정 -->
         		<pattern>[REQUEST1] [%-5level] %d{yyyy-MM-dd HH:mm:ss} [%thread] [%logger{0}:%line] - %msg%n</pattern>
         		<outputPatternAsHeader>true</outputPatternAsHeader>
       	</encoder>
       	<filter class="ch.qos.logback.classic.filter.LevelFilter">  <!-- 5. Filtering -->
       		<level>error</level>	<!-- 레벨 설정 -->
       		<onMatch>ACCEPT</onMatch>	<!-- 레벨 일치하는 것만 -->
       		<onMismatch>DENY</onMismatch>
   		</filter>
     </appender>
   </included>
   ```
   



# 변수 설정

1. logback-variables.properties 생성

   1. key와 value 입력

      ```properties
      LOG_DIR=logs
      LOG_PATTERN=[%-5level] %d{yyyy-MM-dd HH:mm:ss} [%thread] [%logger{0}:%line] - %msg%n
      ```

2. logback-spring-{profile이름}.xml에서 사용해보기

   1. 변수 설정 불러오기

      ```xml
      <property resource="logback-variables.properties" />
      ```

   2. `${key이름}` 을 변수처럼 사용 가능
   
      ```properties
      <file>${LOG_DIR}/mdc.log</file>
      ```
   
      

# Mdc 설정

1. controller

   1. lombok
   2. mdc.put(key, value)
   3. log 출력
   4. mdc.clear

   ```java
   @Slf4j
   @RestController
   public class MdcController {
       @GetMapping("/mdc")
       public String mdc() {
           // key, value 등록
           MDC.put("job", "dev");
   		
           // log 출력
           log.trace("log --> TRACE");
           log.debug("log --> DEBUG");
           log.info("log --> INFO");
           log.warn("log --> WARN");
           log.error("log --> ERROR");
   		
           // mdc 삭제
           MDC.clear();
   
           return "mdc";
       }
   }
   ```

2. appender 설정

   `%X{key이름}`
   
   ```xml
   <appender name="MDC" class="ch.qos.logback.core.rolling.RollingFileAppender">
       <file>${LOG_DIR}/mdc.log</file>
       <rollingPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedRollingPolicy">
         <fileNamePattern>${LOG_DIR}/archive/mdc.%d{yyyy-MM-dd}_%i.log</fileNamePattern>
         <maxFileSize>1KB</maxFileSize>
         <maxHistory>30</maxHistory>
       </rollingPolicy>
       <encoder>
         <pattern>[MDC] %X{job}%n</pattern>	<!-- 사용 -->
         <outputPatternAsHeader>true</outputPatternAsHeader>
       </encoder>
   </appender>
   ```



# Appener 적용

## 모든Controller에 적용

1. 사용할 profile 설정

   우측상단에서 설정

2. 사용할 appender 설정

   logback-spring-{profile이름}.xml에 추가

   ```xml
   <root level="INFO">
       <appender-ref ref="REQUEST1" />
       <appender-ref ref="REQUEST2" />
       <appender-ref ref="MDC" />
       <appender-ref ref="ERROR" />
   </root>
   ```



## 특정 Controller에서만 적용

1. Profile 설정

   우측상단에서 설정

2. 사용할 appender 설정

   logback-spring-{profile이름}.xml에 추가

   1. logger 이름 설정

   2. level 설정
   
   3. 지정된 appender만 사용하기
   
      ```xml
      <logger name="SQL_LOG2" level="INFO" additivity="false">
          <appender-ref ref="QUERY"/>
      </logger>
      ```
   
3. controller에 사용하기(선택)

   * @Slf4j 사용하기

     ```java
     @Slf4j(topic = "SQL_LOG1")	// 사용
     @RestController
     public class QueryController1 {
     
         @GetMapping("/query1")
         public String query() {
     
             log.trace("log --> TRACE");
             log.debug("log --> DEBUG");
             log.info("log --> INFO");
             log.warn("log --> WARN");
             log.error("log --> ERROR");
     
             if (true) {
                 throw new RuntimeException();
             }
     
             return "Query";
         }
     }
     ```

   * LoggerFactory DI

     ```java
     @RestController
     public class QueryController2 {
     
         public static final Logger log = LoggerFactory.getLogger("SQL_LOG2");	// 사용
     
         @GetMapping("/query2")
         public String query() {
     
             log.trace("log --> TRACE");
             log.debug("log --> DEBUG");
             log.info("log --> INFO");
             log.warn("log --> WARN");
             log.error("log --> ERROR");
     
             if (true) {
                 throw new RuntimeException();
             }
     
             return "Query";
         }
     }
     ```






# 사용경험

1. log 관리를 위해 logback 사용

   1. log를 console로 남기기 위해서 ConsoleAppender 사용
      1. console-appender properties 가져오기
      2. appender 설정
         1. 이름
         2. class
      3. log level 설정(Debug < Info < Warn < Error)
      4. log 출력 형식
   2. log를 파일로 저장하기 위해서 RollingFileAppender 사용
      1. appender 설정
         1. 이름
         2. class
      2. 파일 위치 및 이름 설정
      3. rollingPolicy 설정
         1. 로그파일 위치 및 이름 패턴
         2. 로그파일의 최대 크기
         3. 로그파일 최대 보관 주기(단위 : 일)
      4. encoder 설정
         1. 로그 출력 형식
         2. header에 로그 출력 형식 노출
      5. Filtering
         1. level 설정
         2. level 일치하는 것만
   3. 멀티 쓰레드 환경에서 로그에 저장되어있는 값을 동적으로 출력하기 위해서 Mdc 사용
   4. 모든 Controller에 적용, 특정 Controller에 적용 

   