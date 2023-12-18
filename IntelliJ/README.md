# IntelliJ

* 자바 통합 개발환경
* Ultimate(유료), Community(무료)가 있음
  * Community + Spring Initializr(spring2.4.5+Gradle +jar + java) 사용하면 비슷함




# IntelliJ 사용하는 이유

* 단축키, 디버깅, 자동완성이 편리
* Plug in을 간편하게 설치하여 사용 가능(shift2번)

* git 연동 가능

* 자동 Indexing 처리로 인해 검색(ctrl + f(문자 검색) , ctrl + n(파일 검색))이 빠르고 IDE에러가 적어짐



## vs Eclipse

| 전제조건              | Eclipse                        | Intellij IDEA                                          | 비고                                                         |
| --------------------- | ------------------------------ | ------------------------------------------------------ | ------------------------------------------------------------ |
| 비용                  | **승** (무료)                  | 패 (무료 및 유료)                                      | Intellij IDEA는 무료 버전도 있다. 안드로이드 스튜디오가 그 기반이다. |
| 개발 디렉터리 구조    | **승** (워크스페이스)          | 패 (프로젝트)                                          | 단일 프로젝트라면 IntelliJ가 유리하며, 프로젝트 개수가 많으면 Eclipse 가 유리하나, 공통적으로 프로젝트 개수가 많을 수록 더 많은 메모리와 함께 더 갉아먹는 성능으로 안쓰는 프로젝트는 반드시 닫고 진행하는 걸 추천한다. |
| 자바 코드 제안        | 패                             | **승**                                                 | 써보면 안다. 무료 버전 포함.                                 |
| 자바 자동 완성        | 패                             | **승**                                                 | 써보면 안다. 무료 버전 포함.                                 |
| JS/CSS/HTML 개발 기능 | 패                             | **승** (유료)                                          | Eclipse는 웹 관련 자동완성이 지원되나 기능이 매우 처참한 수준이며 특히 ECMA 6 이후 보조 기능 미지원. |
| Spring 개발 기능      | 패                             | **승** (유료)                                          | IntelliJ 는 config 자동완성에 빈 자동완성까지 지원 (무료 버전 제외) 스프링 공식 이클립스 개발툴인 STS도 이클립스가 매력적일 정도로 강력한 기능을 지원하도록 개선했으나 이클립스의 한계를 넘지는 못했다. |
| DB 연동 기능          | 무승부 (DBeaver 플러그인 섪치) | 무승부 (유료 기능 자체 지원)                           | 둘 다 일단 멀티 데이터베이스 기반에 기능도 강력하다.         |
| 전자정부 프레임워크   | **승**                         | 패                                                     | 전자정부 프레임워크는 이클립스 개발 환경을 공식 지원한다. IntelliJ 로도 개발 가능하나 한정적. |
| 코드 테마             | 패                             | **승**                                                 | Eclipse 의 다크 테마는 DevStyle 외에 찾아보기 힘들지만, IntelliJ는 사용자가 다양한 테마를 만들어 배포 중이다. |
| 개인 유료 구독        | 패 (MyEclipse)                 | **승**                                                 | MyEclipse 의 구독 상품은 년 $200 이며 구독 죵료 시 사용 불가. IntelliJ IDEA 는 연 $149 이며 지속 구독 시 할인 혜택과 함께, 구독 종료시에도 구독 시작한 당시 버전을 영구 사용 가능. 프리랜서는 어느 회사에 투입해도 사용 가능할 정도로 유연한 라이선스를 제공한다. |
| 기업 유료 구독        | **승**                         | 패                                                     | IntelliJ IDEA 기업용은 연간 비용이 $499 로 매우 비싸 사용처는 대부분 월간 $49 로 계약한다. 지속 구독 할인 혜택은 없으나 1년 이상 구독 후 종료 시 구독 시작 당시 버전은 계속 제공한다. 그냥 아니면 포기하고 이클립스 강제해도 된다. |
| 플러그인              | 패                             | **승**                                                 | Eclipse 플러그인은 검증하지 않으나, IntelliJ 는 검증 후 마켓에 올라간다. |
| Lombok                | 패 (수동설치)                  | **승** (2020.3 이후 자체지원, 그 이전은 플러그인 설치) | Lombok Eclipse 설치는 매우 번거롭다. 특히 맥에서는 더욱 더.  |
| 커뮤니티 지원         | **승**                         | 패                                                     | 제 3자 지원 기준 (예: 자바 커뮤니티 QnA 게시판 등)           |
| 공식 지원             | 패                             | **승**                                                 | Eclipse 는 이슈 해결을 10년째 안하는 경우도 있을 정도로 최악. IntelliJ는 무료 버전까지 공식 커뮤니티 지원으로 받아줌. |
| 업데이트              | 패                             | **승**                                                 | Eclipse 는 업데이트가 매우 어려운 편이며 오히려 구관이 명관이라 말할 정도로 업데이트가 불안정. |
| **결과**              | 5승 1무 9패                    | **9승 1무 5패**                                        | IntelliJ 승리!                                               |



# 설치방법

https://goddaehee.tistory.com/195



# 단축키

 [단축키 기본 I.pdf]([무료-제공]-intellij-가이드\단축키 기본 I.pdf) 

 [단축키 기본 II.pdf]([무료-제공]-intellij-가이드\단축키 기본 II.pdf) 

 [단축키 응용.pdf]([무료-제공]-intellij-가이드\단축키 응용.pdf) 



Top 15

* Alt + Enter : 오류 위에 커서를 놓고  Alt+Enter를 누르면 문제에 대한 수정 제안
* F2 : 다음 오류, 경고 또는 제안으로 점프
* Alt + 1 : 도구창 열기
* Esc : 에디터에 다시 포커스 위치 시키기
* Cntrl + E : 최근 실행했던 파일 확인
* Cntrl + B : 해당 필드의 선언으로 이동 ,  Cntrl + Alt + B : 구현으로 이동
* Alt + F7 : 항목이 사용된 위치를 검색
* Cntrl + Cntrl : 실행
* Cntrl + F2 : 현재파일 실행
* Cntrl + W + 화살표 : 선택영역 확대/축소
* Cntrl + / : 주석    ,     Shift + Cntrl + / : 영역 주석
* Shift + Cntrl + Enter : 현재구문 자동완성
* Cntrl + Alt + L : 표준에 맞게 파일의 서식 지정
* Shift+Ctrl+A : 단축키 검색



# Gradle

## 개념

* 빌드 배포 도구

* Ant Builder + Groovy 스크립트



## 기능

* 어플리케이션 버전 설정
* 라이브러리 관리
  * 설정된 서버를 통해 라이브러리 다운로드 -> 동일한 의존성을 가진 환경을 수정
  * 자신이 추가한 라이브러리도 repository server에 올릴 수 있음
* 프로젝트 관리
  * 모든 프로젝트가 일관된 구조를 가지고 빌드 프로세스를 유지
* 의존성관리(junit 등을 사용하기 위해서 명시)





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

  



# 실습

## 요구사항 정리

* 기능명세
* API 스펙
  * 기능
  * endpoint
  * method
  * request
  * response
    * Json
    * 상태코드

https://docs.google.com/spreadsheets/d/1ZlRtUQMEgmA5E00QxJLWAGeUo4GqxtP3mMc3tPY2boA/edit#gid=0



## 환경 설정 및 프로젝트 세팅

1. 프로젝트 생성
   * 언어
   * 빌드 배포 도구
2. dependency 설정 (maven spring boot 치면 다 나옴)
   * build.gradle로 설정
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



## Model 구현

1. model 패키지 생성

   * Entity 클래스 생성 및 구현

     * Annotation

       * @Data 

         @Getter + @Setter + @ToString + @EqualsHashCode + @RequiredConstructor(nonull이나 final 변수 파라미터 생성자)

       * @Entity

       * 생성자

         * @NoArgsConstructor

         * @AllArgsConstructor

     * JPA

       * @Id
       * @GeneratedValue
       * @ Column

         * name

         * nullable

       

   * Request 클래스 생성 및 구현

     * Annotation

       * @Data
   
       * 생성자
         * @NoArgsConstructor
         * @AllArgsConstructor

     * 필드 선언

   

   * Response 클래스 생성 및 구현

     * Annotation

       * @Data
   
       * 생성자
         * @NoArgsConstructor
         * @AllArgsConstructor
   
     * 필드 선언
       * 생성자
         * Entity를 통해 가져올거



## Repository 구현

1. 개념

   * 데이터를 주고 받는 interface

   * 실제 저장소 ㄴㄴ

2. 순서

   1. repository 패키지 생성

   2. jpa 구현하는 클래스 생성 및 구현

      1. annotation
         1. @Repository
      2. jpaRepository interface 구현
         1. extends JpaRepository<Entity클래스,id자료형>



## 서비스 코드 구현

1. service 패키지 생성
2. 실제로 동작하는 로직 구현
   1. repository 가져오기
   2. 요구사항 설계문서 보고 함수 작성



## 컨트롤러 구현

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



## 테스트 코드 작성

1. 빌드 라이브러리 추가

   1. build gradle dependency에 테스트 라이브러리 추가 -> reload

      `testImplementation('org.springframework.boot:spring-boot-starter-test')`

   2. create test -> 테스트할 메소드 선택

   3. Service 단위 테스트

      1. Mock 설정

         1. 클래스 위에 @ExtendWith(MockitoExtension.class)
         2. Repository에 @Mock
            * 외부시스템(Network, DB)에 의존하지 않고 자체 테스트를 실행하기 위해서
            * 실제 DB 값이 변경되면 안되므로
         3. Service에 @InjectMocks

      2. Test 코드 작성

         @Test

         1. given
         2. when
         3. then
   
   4. Controller 단위 테스트
   
      1. class 위에 @WebMvcTest(Controller클래스이름.class)
      2. MockMvc mvc에 @Autowired 
      3. Service에 @MockBean
      4. @BeforeEach : 테스트하기전 초기화
      5. 테스트코드 작성
   
   5. Web test
   
      PostMan
   
      * Method
      * URL
      * Body



## 리펙토링

코드 수정하기

1. class 위치 옮기기 -> import 부분 바는 기능 제공
2. 표준출력 대신 로그 사용하기
   1. @Slf4j
   2. System.out.println -> log.info
3. 클래스명 바꾸기 -> 사용한 곳까지 바꾸기 기능 제공



## 디버깅

내가 작서한 코드에 문제가 발생했을 때 그 문제를 찾는 과정

* 줄표시된 부분에 누르면 빨간 break point 생김

* 디버깅 실행

* Step Over : 함수 내부로 들어가지 않고 바로 실행시키고 다음 라인으로 이동시키는 방법

* Step Into : 메소드 안으로 들어가서 메소드를 한줄씩 진행 시키는 방법

* False Step Into : 강제로 메소드 내부로 들어감, Third Party Library 같은 경우는 Step Into로 내부 메소드에 못들어가므로 사용

* Step Out : 함수의 나머지 부분을 실행시킨 곳까지 되돌아가는 것, return으로 바로 되돌아가고 싶을 때 사용

* Drop Frame : Step Into로 들어오기 전 프레임으로 나가기

* 원하는 조건 디버깅

  빨간점 클릭 -> condition에 조건 작성



