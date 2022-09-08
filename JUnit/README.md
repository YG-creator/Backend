# 0. JUnit이란?

TDD(단위 별로 검증하는 테스트 프레임워크)

Assert를 통해 (예상, 실제) 를 통해 검증



# 1. Java

## 결과값 확인

1. dependency 에 Junit 추가
2. test에 작성
   1. @test
   2. 함수 구현
   3. Assert.equals(원하는 값, 연산값)



## 함수값 변환

1. dependency에 mokito 추가

2. test 작성

   1. @ExtendsWith(MokitoExtension.class)

   2. @Mock

   3. @BeforeEach

   4. 함수 결과값 바꾸기

      Mockito.lenient().when(함수()).thenReturn(값);



# 2. Spring



## 통합 테스트

1. 환경 설정
   1. dependency에 webMVC, lombok 추가 -> JUnit, mokito 들어있음
2. 사용할 class에 @Component 
3. DI 있는 class에 @RequireArgsConstructor
4. test에 Annotation
   1. @SpringBootTest : 전체 Bean 가져오기
   2. @Autowired : 자동 주입
   3. @MockBean : Return값 바꿀 class 설정
   4. @Test
      1. Mockito.when(원하는class.함수()).thenReturn(값)  : Return 값 변경
      2. Assertions.assertEquals(예상값, 결과값) : 결과 값 확인



## Controller 테스트

1. 환경 설정
   1. dependency에 webMVC, lombok 추가 -> JUnit, mokito 들어있음
2. 사용할 class에 @Component 
3. DI 있는 class에 @RequireArgsConstructor
4. test에 Annotation
   1. @WebMvcTest({테스트할 controller이름.class}) : 테스트할 controller불러오기
   2. @AutoConfigureMockMvc : Mock
   3. @Import(DollarCalculator.class) : bean 객체 불러오기
5. MockMvc @Autowired
6. MarketServer @MockBean 
7. @Test
   1. MockMvcRequestBuilders.함수("주소")
   2. QueryParam
   3. RequestBody
   4. 예상결과 확인
      1. 상태
      2. 내용
      3. ResponseBody 값

```java
@WebMvcTest({CalculatorApiController.class})	// test할 Controller 
@AutoConfigureMockMvc							// Mock
@Import(DollarCalculator.class)					// Bean
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

1. plugins에 id 'jacoco' 추가
2. jacoco 실행 : Gradle -> Tasks -> verification -> test 더블클릭으로 실행
3. 보고서 작성 : Gradle -> Tasks -> verification -> jacocoTestReport 
4. 보고서 위치 : build -> reports -> jacoco -> test -> html -> index.html에 보고서 위치