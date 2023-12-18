# 작성한 이유

아래와 같은 경험으로 인해 글을 작성해 본다.

1. 필드 주입 방법으로 DI를 하니 테스트에서 NullPointError 발생해서 고생 좀 했다.

2. Controller에서 @Autowired없이 @RequiredArgsConstructor으로 Service DI 하는 코드를 봤다.

 

# 다양한 DI 방법 

1. 필드 주입 (비추)

   필드 변수에 @Autowired 선언

   * 외부에서 수정이 불가능

   * 의존성 주입을 남발 가능성 있음(주입하는 class 생성자가 많을 경우) -> 의존성, 결합에 대한 문제가 발생 -> 단일 책임 원칙에 위배

   * final 키워드를 통한 선언이 불가능하므로 객체가 mutable

   * 테스트 시 불편함

     반드시 DI 프레임워크의 도움을 받아야 함, 테스트에서는 DI 프레임워크 사용 못해서 NPE 발생함

   ```java
   @RestController
   public class HelloController {
   
       @Autowired
       private HelloRepository helloRepository;
       
       @Autowired
       private HelloService helloSerivce;
   }
   ```

   

2. 수정자 주입 (비추)

   set 함수에 @Autowired 선언

   * 외부에서 변경가능해서 위험

   ```
   @RestController
   public class HelloController {
   
       private HelloRepository helloRepository;
       private HelloService helloService;
   
       @Autowired
       public void setHelloRepository(HelloRepository helloRepository) {
           this.helloRepository = helloRepository;
       }
   
       @Autowired
       public void setHelloService(HelloService helloService) {
           this.helloService = helloService;
       }
   }
   ```

   

3. 생성자 주입 (권장)

   * 생성자에 @Autowired 주입

     * 객체의 불변성 확보

     * 테스트 편리

       DI 프레임워크의 도움 없이도 의존관계 주입이 가능

     * 순환 참조시 예외가 발생 -> 실행 전 미리 에러 확인 가능

   * final

     DI 선언에 final을 선언 -> 실행 전에 생성자 실수를 미리 확인 가능함
   
     ```
     @RestController
     public class HelloController {
     
         private final HelloRepository helloRepository;
         private final HelloService helloService;
     
         @Autowired
         public HelloController(HelloRepository helloRepository, HelloService, helloService) {
         	this.helloRepository = helloRepository;
             this.helloService = helloService;
         }
     }
     ```

     ![img](https://blog.kakaocdn.net/dn/YqMz2/btrsOh8eYxp/pcxuskWOoWKZREmcN3hoW0/img.png)

   

   * 생성자 선언 안하고 DI 하는 방법

     * final + @RequiredArgsConstructor 

       DI 하고 싶은 변수에 final을 선언 + class 위에 @RequiredArgsConstructor 선언 -> 생성자 작성안해도 됨
   
     ```
     @RestController
     @RequiredArgsConstructor	// final or NonNull인 변수를 매개변수로 한 생성자 생성해줌
     public class AController {
     
         private final AService aService;	// DI
         private final BService bService;	// DI
     
     }
     ```
   
     

[참고](https://programforlife.tistory.com/111)
