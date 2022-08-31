# IntelliJ

* 자바 통합 개발환경
* Ultimate(유료), Community(무료)가 있음
  * Community + Spring Initializr(spring2.4.5+Gradle +jar + java) 사용하면 비슷함




# IntelliJ 사용하는 이유

* 단축키, 디버깅, 자동완성이 편리
* Plug in을 간편하게 설치하여 사용 가능(shift2번)

* git 연동 가능

* 자동 Indexing 처리로 인해 검색(ctrl + f(문자 검색) , ctrl + n(파일 검색))이 빠르고 IDE에러가 적어짐



# 설치방법

https://goddaehee.tistory.com/195



# 단축키

 [단축키 기본 I.pdf]([무료-제공]-intellij-가이드\단축키 기본 I.pdf) 

 [단축키 기본 II.pdf]([무료-제공]-intellij-가이드\단축키 기본 II.pdf) 

 [단축키 응용.pdf]([무료-제공]-intellij-가이드\단축키 응용.pdf) 



# 요구사항 정리

* 기능명세
* API 스펙
  * method
  * endpoint
  * 기능
  * request
  * response

https://docs.google.com/spreadsheets/d/1ZlRtUQMEgmA5E00QxJLWAGeUo4GqxtP3mMc3tPY2boA/edit#gid=0



# Gradle vs Maven

## 개념

* 빌드 배포 도구

* Ant Builder + Groovy 스크립트



## 기능

* 어플리케이션 버전 설정
* 라이브러리 항목 설정및 관리
  * 설정된 서버를 통해 라이브러리 다운로드 -> 동일한 의존성을 가진 환경을 수정
  * 자신이 추가한 라이브러리도 repository server에 올릴 수 있음
* 프로젝트 관리
  * 모든 프로젝트가 일관된 구조를 가지고 빌드 프로세스를 유지
* 단위 테스트 시 의존성관리(junit 등을 사용하기 위해서 명시)





## Gradle이 Maven보다 좋은점

Ant의 유연한 구조적 장점 + 편리한 의존성 관리 +빌드 타임이 빠름

* 비교 문서가 있음
* XML 대신 Groovy 사용함
  * XML 단점
    * 설정 내용이 길어지고 가독성 떨어짐(if else, for, 변수 선언 없음)
    * 의존관계가 복잡한 프로젝트 설정하기에는 부적절
    * 상속구조를 이용한 멀티 모듈 구현
    * 특정 설정을 소수의 모듈에서 공유하기 위해서는 부모 프로젝트를 생성하여 상속하게 해야함 (상속의 단점 생김)

* 동적인 빌드는 플러그인을 호출하거나 직접 코드를 짤 수 있음
  * 프로젝트별로 주입되는 설정을 다르게 할 수 있음

* 자동 Indexing으로 인해 검색처리가 빠르고 IDE 오류가 적다

  



# 환경 설정 및 프로젝트 세팅

1. 프로젝트 생성
   * 언어
   * 빌드 배포 도구
2. dependency 설정 (maven spring boot 치면 다 나옴)
   * build gradle로 설정
     * plugins
       * springframework boot
       * java
     * dependencies
       * rest API
       * jpa
       * database
       * lombok

2. plugin 설정 (단축키 shift 2번)
   * plugins
     * lombok
   * Build Execution
     * Compiler
       * Annotation Processor
         * Enable anotation Processing 체크후 적용

3. 패키지 생성

4. 클래스 생성





# 모델 구현

1. model 패키지 생성

   * TodoEntity 클래스 생성 및 구현

     * Annotation

       * @Data

       * @Entity

       * 생성자

         * @NoArgsConstructor

         * @AllArgsConstructor

     * JPA

       * @Id, @GeneratedValue

       * @ Column

         * name

         * 빈칸 여부

       

   * TodoRequest 클래스 생성 및 구현

     * Annotation

       * @Data

       * 생성자
         * @NoArgsConstructor
         * @AllArgsConstructor

     * 필드 선언

   

   * TodoResponse 클래스 생성 및 구현

     * Annotation

       * @Data

       * 생성자
         * @NoArgsConstructor
         * @AllArgsConstructor

     * 필드 선언
       * 생성자
         * Entity를 통해 가져올거



# Repository 구현

1. 개념

   * 데이터를 주고 받는 interface

   * 실제 저장소 ㄴㄴ

2. 순서

   1. repository 패키지 생성

   2. jpa 구현하는 클래스 생성 및 구현

      1. annotation
         1. @Repository

      2. jpaRepository interface 구현



# 서비스 코드 구현

1. service 패키지 생성
2. 실제로 동작하는 로직 구현
   1. repository 가져오기
   2. 요구사항 설계문서 보고 함수 작성



# 컨트롤러 구현

1. Controller 패키지 생성
2. API 설계문서 보고 class 껍데기만 구현
   1. annotation
      * @RestController : 컨트롤러임을 명시
      * @CrossOrigin : 웹 페이지의 제한된 자원을 외부 도메인에서 접근을 허용
      * 생성자
        * @AllArgsConstructor
      * RequestMapping
   2. service 가져오기
   3. 엔드포인트 mapping 하기
      * @PostMapping
      * @GetMapping
      * @PatchMapping
      * @DeleteMapping

3. 서버 실행할 메인 클래스 생성 및 구현
   1. annotation
      * @SpringBootApplication
   2. 실행시키기
      1. SpringBootApplication.run(클래스이름.class, args);

4. 서버 실행
   * 성공하면 8080 실행이 됐다고 뜸
5. Postman에서 테스트 해보기
   * Postman에서 함수, 주소 설정하고  send 클릭
   * IntelliJ에서 console보고 작동하는지 확인

6. class 껍데기만 구현했던거 세부 구현
7. Postman에서 다시 테스트 해보기

8. 백엔드 모두 구현했는지 확인하기
   1. https://www.todobackend.com/  -> implements링크 클릭 -> test링크 클릭 -> 서버 입력해보기 -> run

9. 프론트 붙여보기

   https://www.todobackend.com/  -> Example Implementation클릭





# 테스트 코드 작성

1. 빌드 라이브러리 추가

   1. build gradle dependency에 테스트 라이브러리 추가 -> reload

   2. create test

   3. TodoService 테스트

      1. Mock 설정

         1. 클래스 위에 @ExtendWith(MockitoExtension.class)

         2. @Mock : 외부시스템(Unit, DB)에 의존하지 않고 자체 테스트가 가능해야 됨, DB에는 민감한 정보가 있을 수 있으므로

            TocoRepository에 Mock 설정

         3. @InjectMocks : TodoService에 Mock 주입

      2. Test 코드 작성

   4. TodoController 테스트

      1. 클래스 @WebMvcTest(TodoController.class)
      2. @Autowired MockMvc mvc
      3. @MockBean : 
      4. @BeforeEach : 테스트하기전 초기화
      5. 테스트코드 작성
