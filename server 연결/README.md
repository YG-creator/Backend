# Server로 연결하기

![image-20220907174533167](md-images/image-20220907174533167.png)



1. Client

   1. RestTemplateService 만들기

      1. uri 생성
         1. 공통 주소
         2. 특정 주소
         3. QueryParam
         4. 인코딩
         5. 빌드
         6. VariablePath
         7. uri로 변환
      2. object 생성 or json 생성
      3. uri로 보내서 응답 받기
         1. 보내기
            1. 함수
               1. GET
               2. POST
            2. 형태
               1. Object
               2. Json
         2. 응답 받기
            1. Object
            2. Json

   2. Controller 

      1. @RestController, @RequestMapping("경로")

      2. @Autowired RestTemplateService  -> 최신은 생성자로 처리함

      3. REST API 구현

   

2. Server 

   1. REST API 구현 

   

   

# RestTemplateService 실습

```java
@Slf4j	// log 사용
@Service	// 서비스 레이어
public class UserService {
	
    // GET, staticPath, QueryParam -> Object로 받기
    public void getForObject(){
        // 1. uri 생성
        URI uri = UriComponentsBuilder
                .fromUriString("http://localhost:9090")	// 공통주소
                .path("/api")							// 특정주소
                .queryParam("name","steve")				// QueryParam
                .queryParam("age",10)
                .encode()								// 인코딩
                .build()								// 빌드
                .toUri();								// uri로 변환
        log.info("uri : {}",uri);
		
        // 2. uri에서 응답 Object로 받기
        RestTemplate restTemplate = new RestTemplate();
        User user = restTemplate.getForObject(uri, User.class);	
        log.info("user : {}", user);
    }
    
    // GET, PathVariable, QueryParam -> json으로 받기
    public void getForEntity(){
        // 1. uri 생성
        URI uri = UriComponentsBuilder
                .fromUriString("http://localhost:9090")	// 공통 주소
                .path("/api/{path}")					// 특정 주소
                .queryParam("name","steve")				// QueryParam
                .queryParam("age",10)
                .encode()								// 인코딩
                .build()								// 빌드
                .expand("user")							// PathVariable
                .toUri();								// uri로 변환
        log.info("uri : {}", uri);
		
        // 2. uri에서 응답 json으로 받기
        RestTemplate restTemplate = new RestTemplate();
        ResponseEntity<User> response = restTemplate.getForEntity(uri, User.class);
        log.info("{}",response.getStatusCode());
        log.info("{}",response.getHeaders());
        log.info("{}",response.getBody());
    }
    
    // POST, PathVariable -> Object로 받기 
    public void postForObject(){
        // uri 생성
        URI uri = UriComponentsBuilder
                .fromUriString("http://localhost:9090")	// 공통주소
                .path("/api/{path}")					// 특정 주소
                .encode()								// 인코딩
                .build()								// 빌드
                .expand("user")							// PathVariable
                .toUri();								// uri로 변환
        log.info("uri : {}", uri);
		
        // Object 생성
        User user = new User();
        user.setName("홍길동");
        user.setAge(10);
        
        // 2. uri로 Object 보내기 -> 응답 Object로 받기
        RestTemplate restTemplate = new RestTemplate();
        User response = restTemplate.postForObject(uri, user, User.class);
        log.info("response : {}", response);
    }
	
    // POST, PathVariable -> json으로 받기
    public void postForEntity(){
        URI uri = UriComponentsBuilder
                .fromUriString("http://localhost:9090")	// 공통 주소
                .path("/api/{path}")					// 특정 주소
                .encode()								// 인코딩
                .build()								// 빌드
                .expand("user")							// PathVariable
                .toUri();								// uri로 변환
        log.info("uri : {}", uri);
		
        // Object 생성
        User user = new User();
        user.setName("홍길동");
        user.setAge(10);
		
        // 2. uri로 Object 보내기 -> 응답 json으로 받기
        RestTemplate restTemplate = new RestTemplate();
        ResponseEntity<User> response = restTemplate.postForEntity(uri, user, User.class);
        log.info("{}",response.getStatusCode());
        log.info("{}",response.getHeaders());
        log.info("{}",response.getBody());
    }
    
    // POST, PathVariable, header, body -> Json으로 받기
    public void exchange(){
        URI uri = UriComponentsBuilder
                .fromUriString("http://localhost:9090")	// 공통 주소
                .path("/api/{path}/header")				// 특정 주소
                .encode()								// 인코딩
                .build()								// 빌드
                .expand("user")							// PathVariable
                .toUri();								// uri로 변환
        log.info("uri : {}", uri);
		
        // Object 생성
        User user = new User();
        user.setName("홍길동");
        user.setAge(10);
		
        // json 만들기
        RequestEntity<User> req = RequestEntity				// Request, object mapping
                .post(uri)									// POST
                .contentType(MediaType.APPLICATION_JSON)	// JSON
                .header("x-authorization","my-header")		// Header
                .body(user);								// Body
		
        // uri로 JSON 보내기 -> 응답 Json으로 받기
        RestTemplate restTemplate = new RestTemplate();
        ResponseEntity<User> response = restTemplate.exchange(req, new ParameterizedTypeReference<>(){});	// User 형식
        log.info("{}",response.getStatusCode());
        log.info("{}",response.getHeaders());
        log.info("{}",response.getBody());
    }
    
    // open api 활용
    
    public void naver(){
        URI uri = UriComponentsBuilder
                .fromUriString("https://openapi.naver.com")	// 고정 주소
                .path("/v1/search/local.json")				// 특정 주소
                .queryParam("query","%EC%A3%BC%EC%8B%9D")	// QueryParam
                .queryParam("display","10")
                .queryParam("start","1")
                .queryParam("sort","random")
                .encode()									// 인코딩
                .build()									// 빌드
                .toUri();									// uri로 변환
        log.info("uri : {}", uri);
		
        // json 만들기
        RequestEntity<Void> req = RequestEntity
                .get(uri)											// GET
                .header("X-Naver-Client-Id","Zi3o1uQftp59zuIqEAz4")	// header
                .header("X-Naver-Client-Secret","iy6YKSWpLM")
                .build();											// 빌드
		
        // uri로 JSON 보내기 -> 응답 Json으로 받기
        RestTemplate restTemplate = new RestTemplate();
        ResponseEntity<String> response = restTemplate.exchange(req, new ParameterizedTypeReference<>(){}); // String 형식
        log.info("{}",response.getStatusCode());
        log.info("{}",response.getHeaders());
        log.info("{}",response.getBody());
    }
}
```