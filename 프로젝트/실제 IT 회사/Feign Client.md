# Feign Client

* 사용처

  외부 요청

  rest template이랑 비슷

* 개념

  선언적으로 사용할 수 있는 Client



## Connections/Read Timeout

* 외부 서버와 통신 시 client 별로 Timeout 설정이 가능



## Feign Interceptor

* 외부로 요청이 나가기 전에 만약 공통적으로 처리해야하는 부분이 있다면 

  Interceptor를 재정의하여 처리 가능



## Feign CustomLogger

* 운영(Reqeust/Response)을 하기 위한 적절한 Log를 남길수 있음



## Feign ErrorDecoder

* 요청에 대해 정상 응답이 아닌 경우 핸들링 가능



## 실습

1. build.gradle

   1. dependency 추가

      1. lombok

      2. Spring Web

      3. feign

         ```java
         // Feign
         implementation 'org.springframework.cloud:spring-cloud-starter-openfeign'
         ```

   2. dependencyManagement로 spring cloud 버전관리 설정

      ```java
      imports {
          mavenBom "org.springframework.cloud:spring-cloud-dependencies:${springCloudVersion}"
      }
      ```

   3. ext로 springCloudVersion 등록

      ```java
      ext {
          /**
      	 * Spring Boot and springCloudVersion must be compatible.
      	 * 2.6.x, 2.7.x (Starting with 2021.0.3) = 2021.0.x
      	 * ref : https://spring.io/projects/spring-cloud
      	 */
      	set('springCloudVersion', '2021.0.3')	// 위 ref에서 springboot에 맞는 release 찾기
      }
      ```

2. common : dto

   1. reqeust
   2. response

3. controller

   1. client Controller
   2. server Controller

4. serivice  : 요청값 넣기

   1. feign-client DI
   2. response = feign-client.함수(값)

5. config : global한 설정

   logger bean 등록

6. feign

   1. cllient

      1. interface
      2. @FeignClient로 name, url, configuration 설정
      3. mapping

   2. interceptor

      RequestInterceptor implements해서 구현하기

      1. GET request
      2. POST request

   3. config : local적인 설정

      1. interceptor bean 등록
      2. decoder bean 등록

   4. logger

      1. extends Logger(feign Logger 임)

      2. log : logformat

      3. logRequest : request 출력

      4. logAndRebufferResponse : response 출력

         1. 여기에서도 logRequest  설정 가능

         2. feign logger 복붙하고 수정하기

            1. 예상 요청 처리 시간보다 오래 걸렸다면 "Slow API"라는 log를 출력해보기 -> 네트워크 문제 확인 가능

               ```java
               private static final int DEFAULT_SLOW_API_TIME = 3_000;	// 예상 요청 시간
               private static final String SLOW_API_NOTICE = "Slow API";	// 원하는 로그
               ```

   5. decoder

      1. implements ErrorDecoder
      2. 특정 error 발생시 로그 설정
      3. 그외는 기존거 사용

