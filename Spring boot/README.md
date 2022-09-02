# Spring Boot란?

프로덕션 수준의 스프링 기반 어플리케이션을 쉬게 만들 수 있다.

Spring 굿성이 거의 필요하지 않다.

java 어플리케이션 만들 수 있다.



목표

Spring 개발에 대해 빠르고 광범위하게 적용 가능

기본설정이 있지만 바꿀 수 있음

대규모 프로젝트에 공통적인 비기능 제공

XML 구성 요구사항 없음



Build Tool

Maven

Gradle v



Servlet Contatiners

Tomcat v

Jetty

undertow

Netty



IntellilJ로 실행하는 법

spring initializr로 프로젝트 다운받아서도 가능

IntelliJ에서 plugin 다운 받아서도 가능



장점

어플리케이션 개발에 필수 요소들만 모아두었다.

간단한 설정으로 개발 및 커스텀 가능(어노테이션)

간단하고 빠르게 어플리케이션 실행 및 배포가 가능(jar)

대규모프로젝트에 필요한 비 기능적 기능도 제공

오래되서 안정적인 운영 가능

Spring 불편한거 없앰(XML 설정)



# REST Client 설치

## REST Client 용도

웹 어플리케이션 테스트 용도

## 방법

Chrome  웹 스토어 -> Talend API Tester 추가



# 설정

## 포트 변경

src -> main -> resorces -> application properties -> server.port = 원하는포트 작성



# Controller

## 공통

1. Annotation
   1. @RestController : 컨트롤러임을 명시
   2. @RequestMapping("주소") : 공통 주소



## GET API

Resource Read, parameter header에 작성

1. 고정된 주소

   @GetMapping("주소")

2. PathVariable

   1. GetMapping에 {변수이름}

   2. 함수 매개변수에 @PathVariable

      1. 매개변수이름 = 변수이름 인 경우 : @PathVariable 타입 변수이름

      2. 다른경우

         @PathVariable(name="변수이름") 타입 매개변수이름

3. QueryParam 

   1. 함수 매개변수에 @RequestParam

      1. Map
      2. 각 변수마다 

   2. 함수 매개변수에 dto 객체

      dto class 구현 ->함수 매개변수에 구현한 class 넣기

      

## POST API

Resourse Create,  parameter DataBody에 데이터 작성

1. 변수 명칭법
   1. 스네이크 : var_name -> JSON에 사용
   2. 카넬 : varName -> Java에 사용



2. JSON

   1. 자료형

      * int
      * String
      * boolean
      * Object : {}
      * array : []

   2. 형태(key : value)

      

3. 고정된 주소

   @PostMapping("주소")

   

4. PathVariable 가능 : {}

   

5. QueryParameter 안씀 : DataBody 있어서

   

6. Data Body

   1. request에서 Json body 가져옴

   2. @PostMapping("주소")

   3. 함수 매개변수에 @RequestBody 타입 매개변수이름

      1. map

         한계 : 자료형

      2. dto

         * 주의점 : 카넬(Java)이나 스네이크(Json) 이름 다를 때
         * 해결
           1. 변수마다 JsonProperty("json에서 작성한 key이름")로 설정
           2. class 위에 JsonNaming(value = PropertyNamingsStrategy.SnakeCaseStrategy.class) : 반환도 JSON형태로 해줌, 변수이름을 스네이크식으로 이해함

 

## PUT API

Resource Read/Update, parameter DataBody에 작성



1. 고정된 주소

   @PutMapping("주소")

   

2. PathVariable 가능 : {}

   

3. QueryParameter 안씀 : DataBody 있어서

   

4. Data Body

   위 POST API와 동일



## DELETE API

Resource Delete, Query Parameter(데이터가 작아서)

1. 고정된 주소

   @DeleteMapping("주소")



2. PathVariable 가능 : {}



3. QueryParameter 가능



## Response 내려주기

### 1. ResponseEntity로 JSON으로 응답 -> 요걸 주로씀

1. JSON으로 응답

   return dto

2. JSON 커스터마이징

   1. 함수 자료형 ResponseEntity<class이름>로 하기
   2. 함수 매개변수는 dto
   3. 함수 return 은 ResponseEntity.status().body
      1. status(HttpStatus.선택) : 응답 상태메시지
      2. body() : body에 적을거



### 2.PageController로 응답하기

클래스 위에 @Controller

1.  html로 응답
   1. RequestMapping("/경로")
   2. 함수 return 이름.html
   3. resource -> static 에 이름.html작성
2. JSON Body로 응답 -> 보통 안함
   1. @ResponseBocy
   2. 매핑 Annotation
   3. 함수안에서 인스턴스 생성 후 -> 값 set -> return instance



## 모범 사례

1. ObjectMapper 클래스

   1. 함수 

      1. wirteValueAsString(dto 인스턴스 이름) : object -> text
      2. readValue(text이름, dto클래스이름.class) : text -> object

   2. 주의점

      1. object -> text

         get메소드 있어댜 됨

      2. text -> object 

         디폴트 생성자 있어야 됨

2. dto 함수 만들 때 get들어가면 안 됨

3. 변수이름 matching(카넬, 스네이크)

   매칭 시킬 변수마다 JsonProperty("json에서 작성한 key이름")로 설정

   