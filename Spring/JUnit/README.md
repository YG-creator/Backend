# 0. JUnit이란?

* Java 기반 단위 테스트를 위한 프레임워크

* Annotation 기반으로 테스트
* Assert로 예상값, 실제값 비교



# 1. Java

## 함수값 변환 후 결과값 확인

1. 설정

   1. dependency 에 junit-jupiter 확인(예상값, 실제값 비교)

      1.  junit jupiter api 확인
      2.  junit jupiter engine 확인

   2. dependency에 mokito 추가(함수값 변환)

      1. mokito core 추가

      2. mokito junit jupiter 추가

2. 함수값 변환

   1. @ExtendsWith(MokitoExtension.class) : mokito 사용

   2. @Mock : mock 객체 생성(함수값 바꾸는 용도)

   3. @BeforeEach : 모든 단위 테스트 실행 전

   4. 함수 결과값 바꾸기

      Mockito.lenient().when(함수()).thenReturn(값);

3. 단위 테스트

   1. @test

   2. 테스트 함수 구현

   3. 예상값, 실제값 비교

      Assert.equals(원하는 값, 연산값)

   

```java
@ExtendWith(MockitoExtension.class)	// mock 사용
public class Test {
    @Mock	// mock 객체 생성(함수값 바꿀 때 사용)
    public MarketServer marketServer;

    @BeforeEach	// 단위 테스트 전
    public void init(){	
        Mockito.lenient().when(marketServer.price()).thenReturn(200);	// 함수 값 바꾸기
    }

    @test	// 단위 테스트
    public void hello(){
        System.out.println("hello");
    }

    @test	// 단위 테스트
    public void dollar(){
        DollarCalculator dollarCalculator = new DollarCalculator(null);
        Calculator calculator = new Calculator(dollarCalculator);
        int sum = calculator.sum(100, 100);
        Assertions.assertEquals(20000, sum);	// 예상값, 실제값 비교
    }

    @test	// 단위 테스트
    public void mock(){
        DollarCalculator dollarCalculator = new DollarCalculator(marketServer);
        dollarCalculator.init();

        Calculator calculator = new Calculator(dollarCalculator);
        int sum = calculator.sum(100, 100);
        Assertions.assertEquals(40000, sum);	// 예상값, 실제값 비교
    }
}
```



# 2. Spring

* 위치

  test 폴더 -> 패키지 생성 -> java class 생성

* 주의점

  test 패키지 이름 = main 패키지 이름



## 통합 테스트

1. 환경 설정
   1. Spring Initializr로 프로젝트 생성
      1. 이름 설정, gradle
      2. Developer Tools - lombok 체크
      3. Web - webMVC 체크
   1. dependency에 webMVC, lombok 추가 -> JUnit, mokito 들어있음

2. Bean 등록할 class에 @Component

3. lombok 사용(선택)

   1. @Data : getter + setter + 생성자
   2. 생성자 
      1. @RequireArgsConstructor : 몇몇 변수 사용한 생성자
      2. @NoArgsConstructor : default 생성자
      3. @AllArgsConstructor : 모든 변수 사용한 생성자

4. 설정 Annotation 
   1. @SpringBootTest : 전체 Bean 가져오기
   2. @Autowired : 자동 주입
   3. @MockBean : Return값 바꿀 class 설정

5. 단위 테스트 함수 구현

   1. @Test

   2. 함수 Return 값 변경

      Mockito.when(원하는class.함수()).thenReturn(값)  : Return 값 변경

   3. 값 확인

      Assertions.assertEquals(예상값, 결과값) : 결과 값 확인



```java
@SpringBootTest	// 모든 Bean 가져오기
class AbstractCalculatorReqApplicationTests {
	
    @MockBean	// Bean 껍데기만 가져와서 리턴 값 조작가능
    private MarketServer marketServer;
    
    @Autowired	// 필요한 의존 객체의 “타입"에 해당하는 빈을 찾아 주입한다.
    private DollarCalculator dollarCalculator;

    @Test		// 테스트
    public void dollarCalculatorTest() {
        Mockito.when(marketServer.price()).thenReturn(1100);	// 함수갑 변환
        
        int sum = dollarCalculator.sum(10,10);
        int minus = dollarCalculator.minus(10,10);
        
        Assertions.assertEquals(22000,sum);	// 예상값, 실제값 비교
        Assertions.assertEquals(0,minus);
    }
}
```



## Controller 테스트

1. 환경 설정
   1. Spring Initializr로 프로젝트 생성
      1. 이름 설정, gradle
      2. Developer Tools - lombok 체크
      3. Web - webMVC 체크
   2. dependency에 webMVC, lombok 추가 -> JUnit, mokito 들어있음
   
2. Bean 등록할 class에 @Component 

3. DI 있는 class에 @RequireArgsConstructor

4. 설정 Annotation
   1. @WebMvcTest({테스트할 controller이름.class}) : 테스트할 controller불러오기
   2. @AutoConfigureMockMvc : Mock Spring
   3. Bean 가져오기 선택
      1. @Import(DollarCalculator.class) : 사용할 bean 객체 불러오기
      2. @SpringBootTest : 모든 Bean 가져오기
   4. @Autowired : 필요한 의존 객체의 “타입"에 해당하는 빈을 찾아 주입
   5. @MockBean : Bean 껍데기만 가져와서 함수 리턴 값 조작가능
   
5. @Test : 단위 테스트
   1. MockMvcRequestBuilders.함수("주소")

   2. QueryParam

   3. RequestBody

   4. 예상결과 확인(.andExpect())
      1. 상태

         MockMvcResultMatchers.status().isOk()

      2. 내용 선택

         1. content().string("hello") : 함수 결과값
         2. MockMvcResultMatchers.jsonPath("$.result").value("22000") : json 결과값
         3. MockMvcResultMatchers.jsonPath("$.body.responseResult").value("22000") : : json 결과값

   5. 출력

      .andDo(MockMvcResultHandlers.print())

      

```java
@WebMvcTest({CalculatorApiController.class})	// test할 Controller 
@AutoConfigureMockMvc							// Mock Spring
@Import(DollarCalculator.class)					// 원하는 Bean 가져오기
public class CalculatorApiControllerTest {

    @Autowired	// 필요한 의존 객체의 “타입"에 해당하는 빈을 찾아 주입한다.
    private MockMvc mockMvc;

    @MockBean	// Bean 껍데기만 가져와서 리턴 값 조작가능
    private MarketServer marketServer;
	
    // GET
    @Test	// 테스트
    public void helloTest() throws Exception {
        mockMvc.perform(	
                MockMvcRequestBuilders.get("/api/calculator/hello")	// Request GET API
        ).andExpect(
                MockMvcResultMatchers.status().isOk()				// 예상 결과 상태 확인
        ).andExpect(												
                content().string("hello")							// 예상 결과 내용 확인
        ).andDo(MockMvcResultHandlers.print());						// 출력
    }
	
    // return값 변경, GET, QueryParameter -> 상태,내용 확인
    @Test
    public void getTest() throws Exception {
        when(marketServer.price()).thenReturn(1100);				// Return값 변경

        LinkedMultiValueMap<String, String> map = new LinkedMultiValueMap();// QueryParam 값 넣기
        map.add("x","10");
        map.add("y","10");

        mockMvc.perform(
                MockMvcRequestBuilders.get("/api/calculator/sum").queryParams(map)	// Request GET API
        ).andExpect(
                MockMvcResultMatchers.status().isOk()								// 예상 결과 상태 확인
        ).andExpect(
                content().string("22000")											// 예상 결과 내용 확인
        ).andDo(MockMvcResultHandlers.print());										// 출력
    }
	
    // return값 변경, POST, RequestBody -> 상태, 내용, ResponseBody 확인
    @Test
    public void postTest() throws Exception {
        when(marketServer.price()).thenReturn(1100);	// Return값 변경

        CalculatorReq req = new CalculatorReq();		// RequestBody값 넣기
        req.setX(10);
        req.setY(10);

        String json = new ObjectMapper().writeValueAsString(req);	// json으로 변환
        System.out.println(json);

        mockMvc.perform(
                MockMvcRequestBuilders.post("/api/calculator/sum")	// POST API
                .content(json)										// RequestBody
                .contentType("application/json")					// json
                .accept("application/json")							// json
        ).andExpect(
                MockMvcResultMatchers.status().isOk()				// 상태 확인
        ).andExpect(
                MockMvcResultMatchers.jsonPath("$.result").value("22000")	// 결과 확인
        ).andExpect(
                MockMvcResultMatchers.jsonPath("$.body.responseResult").value("22000")	// ResponseBody 값 확인
        ).andDo(MockMvcResultHandlers.print());		// 출력
    }

}
```



# 3. 테스트 커버리지

테스트 보고서 자동 작성해줌

1. plugin 추가 : 왼쪽 build.gradle -> plugins에 id 'jacoco' 추가
2. jacoco 실행 : 오른쪽 Gradle -> Tasks -> verification -> test 더블클릭으로 실행
3. 보고서 작성 : 오른쪽 Gradle -> Tasks -> verification -> jacocoTestReport 
4. 보고서 위치 : 왼쪽 build -> reports -> jacoco -> test -> html -> index.html에 보고서 위치