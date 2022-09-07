# Spring Boot Validation

![image-20220905091517280](C:\Users\YG\AppData\Roaming\Typora\typora-user-images\image-20220905091517280.png)

https://beanvalidation.org/2.0-jsr380/

RESP API 함수매개변수에 @Valid

dto의 변수에 Annotation





# Spring Boot Custom Validation

1. AssertTrue / False 와 같은 메소드 지정을 통해서 Custom Logic 적용 가능 -> 재사용 불가
2. ConstraintValidator를 적용하여 재사용이 가능한 Custom Logic 적용 가능 -> 재사용 가능



# Spring Boot Exception 처리

1. @ControllerAdvice : Global 예외 처리 및 특정 Package / Controller 예외 처리

   ```java
   @RestControllerAdvice(basePackageClasses = ApiController.class)
   public class ApiControllerAdvice {
       @ExceptionHandler(value = Exception.class)
       public ResponseEntity exception(Exception e){
           System.out.println(e.getClass().getName());
           return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body("");
       }
   }
   ```

   

2. @ExceptionHandler : 특정 Controller 예외 처리

   우선순위 : @ExceptionHandler > @ControllerAdvice

   ```java
   public class ApiController {
       @ExceptionHandler(value = Exception.class)
       public ResponseEntity exception(Exception e){
           System.out.println(e.getClass().getName());
           return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body("");
       }
   }
   ```

   

# Spring Boot Validation 모범 사례

1. Controller Validation

   ```java
   public class ApiController {
   
       @GetMapping("") 
       public User get(
               @Size(min = 2)	
               @RequestParam String name,
   
               @NotNull
               @Min(1)
               @RequestParam Integer age){
           User user = new User();
           user.setName(name);
           user.setAge(age);
   
           return user;
       }
   
   
       @PostMapping("")
       public User post(@Valid @RequestBody User user){
           System.out.println(user);
           return user;
       }
   
   }
   ```

2. advice package 생성 -> class 생성

3. @ControllerAdvice 선언

   ```java
   @RestControllerAdvice(basePackageClasses = ApiController.class) -> 특정 컨트롤러에서만 advice 함
   public class ApiControllerAdvice {    
   ```

4. Exeption 처리 할 거 설정 

   ```java
   	@ExceptionHandler(value = Exception.class)
   	public ResponseEntity exception(Exception e){
   ```

5. dto(Error, ErrorResponse) 구현

   ```java
   public class Error {
   
       private String field;
       private String message;
       private String invalidValue;
   
       public String getField() {
           return field;
       }
   
       public void setField(String field) {
           this.field = field;
       }
   
       public String getMessage() {
           return message;
       }
   
       public void setMessage(String message) {
           this.message = message;
       }
   
       public String getInvalidValue() {
           return invalidValue;
       }
   
       public void setInvalidValue(String invalidValue) {
           this.invalidValue = invalidValue;
       }
   }
   
   public class ErrorResponse {
   
       String statusCode;
       String requestUrl;
       String code;
       String message;
       String resultCode;
   
       List<Error> errorList;
   
       public String getStatusCode() {
           return statusCode;
       }
   
       public void setStatusCode(String statusCode) {
           this.statusCode = statusCode;
       }
   
       public String getRequestUrl() {
           return requestUrl;
       }
   
       public void setRequestUrl(String requestUrl) {
           this.requestUrl = requestUrl;
       }
   
       public String getCode() {
           return code;
       }
   
       public void setCode(String code) {
           this.code = code;
       }
   
       public String getMessage() {
           return message;
       }
   
       public void setMessage(String message) {
           this.message = message;
       }
   
       public String getResultCode() {
           return resultCode;
       }
   
       public void setResultCode(String resultCode) {
           this.resultCode = resultCode;
       }
   
       public List<Error> getErrorList() {
           return errorList;
       }
   
       public void setErrorList(List<Error> errorList) {
           this.errorList = errorList;
       }
   }
   ```

6. response(status, resonseBody) 구현

   ```java
   	@ExceptionHandler(value = Exception.class)
       public ResponseEntity exception(Exception e){	// 그 외 error
           System.out.println(e.getClass().getName());
           return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body("");
       }
   
       @ExceptionHandler(value = MethodArgumentNotValidException.class)	// RequestBody로 받은 파라미터 에러
       public ResponseEntity methodArgumentNotValidException(MethodArgumentNotValidException e, HttpServletRequest httpServletRequest){
   
           List<Error> errorList = new ArrayList<>();
   
           BindingResult bindingResult = e.getBindingResult();
           bindingResult.getAllErrors().forEach(error -> {
               FieldError field = (FieldError) error;
   
               String fieldName = field.getField();
               String message = field.getDefaultMessage();
               String value = field.getRejectedValue().toString();
   
               Error errorMessage = new Error();
               errorMessage.setField(fieldName);
               errorMessage.setMessage(message);
               errorMessage.setInvalidValue(value);
   
               errorList.add(errorMessage);
           });
   
           ErrorResponse errorResponse = new ErrorResponse();
           errorResponse.setErrorList(errorList);
           errorResponse.setMessage("");
           errorResponse.setRequestUrl(httpServletRequest.getRequestURI());
           errorResponse.setStatusCode(HttpStatus.BAD_REQUEST.toString());
           errorResponse.setResultCode("FAIL");
   
           return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(errorResponse);
       }
   
       @ExceptionHandler(value = ConstraintViolationException.class)	// 제약 조건 위배시 에러
       public ResponseEntity constraintViolationException(ConstraintViolationException e, HttpServletRequest httpServletRequest){
   
           List<Error> errorList = new ArrayList<>();
   
           e.getConstraintViolations().forEach(error ->{
               Stream<Path.Node> stream = StreamSupport.stream(error.getPropertyPath().spliterator(), false);
               List<Path.Node> list = stream.collect(Collectors.toList());
   
               String field = list.get(list.size() -1).getName();
               String message = error.getMessage();
               String invalidValue = error.getInvalidValue().toString();
   
               Error errorMessage = new Error();
               errorMessage.setField(field);
               errorMessage.setMessage(message);
               errorMessage.setInvalidValue(invalidValue);
   
               errorList.add(errorMessage);
   
           });
   
           ErrorResponse errorResponse = new ErrorResponse();
           errorResponse.setErrorList(errorList);
           errorResponse.setMessage("");
           errorResponse.setRequestUrl(httpServletRequest.getRequestURI());
           errorResponse.setStatusCode(HttpStatus.BAD_REQUEST.toString());
           errorResponse.setResultCode("FAIL");
   
           return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(errorResponse);
       }
   
       @ExceptionHandler(value = MissingServletRequestParameterException.class)	// 필수 파라미터 누락 시 에러
       public ResponseEntity missingServletRequestParameterException(MissingServletRequestParameterException e, HttpServletRequest httpServletRequest){
   
           List<Error> errorList = new ArrayList<>();
   
           String fieldName = e.getParameterName();
           String invalidValue = e.getMessage();
   
           Error errorMessage = new Error();
           errorMessage.setField(fieldName);
           errorMessage.setMessage(e.getMessage());
   
   
           ErrorResponse errorResponse = new ErrorResponse();
           errorResponse.setErrorList(errorList);
           errorResponse.setMessage("");
           errorResponse.setRequestUrl(httpServletRequest.getRequestURI());
           errorResponse.setStatusCode(HttpStatus.BAD_REQUEST.toString());
           errorResponse.setResultCode("FAIL");
   
           return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(errorResponse);
       }
   
   }
   ```

   



# Filter???

* 특징
  * 유일하게 ServletRequest, ServletResponse의 객체를 변환할 수 있음
  * Web application에 등록됨

* 용도

  * 요청,응답 정보를 변경하거나 값 확인(Logging)

  * 인증과 관련된 Logic들을 선/후 처리(Service와 business logic 분리)

* 동작 순서

  Filter -> Interceptor -> AOP

![image-20220906183856348](md-images/image-20220906183856348.png)

* 코드

  ```java
  ```

  

# Interceptor???

* 특징

  * Spring Context에 등록되어 AOP와 유사한 기능 제공

* 용도

  * 주로 인증 단계 처리
  * Logging
  * 인증과 관련된 Logic들을 선/후 처리(Service와 business logic 분리)

* 동작 순서

  ![image-20220906201506939](md-images/image-20220906201506939.png)

* 코드

  ```java
  ```

  



# 비동기 처리

* 별도의 쓰레드를 통해 처리

* @Async를 통해 비동기 처리

* 쓰레드 직접 설정 가능

  
