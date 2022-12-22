# 프로젝트 구성

* setting.gradle

* build.gradle 



# 필요한 이유

웹사이트 보안

* 인증(누가 접근했는지)
  * UsernamePassword 인증
  * 소셜 로그인(인증 위임) 
* 권한(특정 리소스에 접근 권한여부)
  * Annotation
    * Secured
    * PrePostAuthorize
  * AOP



# Spring Security 실습

1. build.gradle

   dependencies 추가

   ```gradle
   dependencies {
   	implementation("$boot:spring-boot-starter-web")
   }
   ```

2. application.yml 

   main - resources에 생성

   서버포터, user정보 설정(1명만 추가가능)

   ```yml
   server:
   	port: 9050
   spring:
   	security
   		user:
   			name:user1
   			password: 1111
   			roles: USER
   ```

3. Security Message 생성

   ```java
   @Data
   @AllArgsConstructor
   @NoArgsConstrctor
   @Builder
   public class SecurityMessage {
       pritvatge Authentication auth;
       private String message;
   }
   ```

4. Controller 생성  

   권한 부여, mapping

   ```java
   @RestController
   public class Controller {
       // 홈페이지
       @RequestMapping("/")
       public String index() {
           return "홈페이지";
       }
       
       // 로그인 페이지
        @RequestMapping("/user")
       public SecurityMessage user() {
           return SecurityContextHolder.getContext().getAuthentiction();
       }
       
       // user만 사용가능한 곳
       @PreAuthorize("hasAnyAuthoirty('ROLE_USER')")
       @RequestMapping("/user")
       public SecurityMessage user() {
           return SecurityMessage.builder()
               .auth(SecurityContextHolder.getContext.getAuthentication())
               .message("User 정보")
               .build();
       }
       
       // admin만 사용가능한 곳
       @PreAuthorize("hasAnyAuthoirty('ROLE_ADMIN')")
       @RequestMapping("/admin")
       public SecurityMessage index() {
           return SecurityMessage.builder()
               .auth(SecurityContextHolder.getContext.getAuthentication())
               .message("Admin 정보")
               .build();
       }
   }
   ```

5. config 생성 

   권한 체크 설정, 사용자 정보 추가, 암호화, 접근허용 설정

   ```java
   @EnableWebSecurity(debug = true)
   @EnableGlobalMethodSecurity(prePostEnabled = true)
   public class SecurityConfig extends WebSecurityConfigurerAdapter {
       
       // 사용자 정보 추가
       @Override
       protected void configure(AuthenticationManagerBuilder auth) throws Exception {
           auth.inMemoryAuthentication()
               .withUser(User.builder()
               .username("user2")
                        .password(passwordEncoder.encode("2222"))
                        .roles("USER")
               ).withUser(User.builder()
               .username("user3")
                        .password(passwordEncoder.encode("3333"))
                        .roles("ADMIN")
               )
       }
       
       // 암호화
       @Bean
       PasswordEncoder passwordEncoder() {
           return new BCryptPasswordEncoder();
       }
       
       // 모두 접근 허용
       protected void configure(HttpSecurity http) throws Exception {
           http.authorizeRequests((requests) -> 
                                 request.antMatchers("/").permitAll()
                                 .anyREquest().authenticated()
                                 );
           http.formLogin();
           http httpBasic();    
       }
       
   }
   ```



# 전체 구조

filter -> servlet -> controller -> method

![img](https://gitlab.com/jongwons.choi/spring-boot-security-lecture/-/raw/master/images/fig-1-servlet-container.png)

filter에 Spring Security filter chain 작동

![img](https://gitlab.com/jongwons.choi/spring-boot-security-lecture/-/raw/master/images/fig-2-spring-big-picture.png)

Spring Security filter chain 종류

![img](https://gitlab.com/jongwons.choi/spring-boot-security-lecture/-/raw/master/images/fig-4-filters.png)

config에서 filter chain설정

```java
// filter chain 순서 설정 - 여러 체인 사용할 때 써야 됨
@Order(숫자)

// 필터 적용할 주소 설정
http.antMatcher("/**");

// 모두 허용
http.authorizeRequests((requests) -> 
                       request.antMatchers("/").permitAll()
                       .anyREquest().authenticated()
                      );

// 그 외 filter chain 설정
http.formLogin();
http httpBasic();  
http.headers().disable()	
    .csrf().disable()
    .logout().disable()
    .requestCache().disable();
```



# Authentication (인증)

## 로그인이란?

* 스프링 프레임워크에서 로그인을 한다는 것은 authenticated 가 true인 Authentication 객체를 SecurityContext 에 갖고 있는 상태를 말합니다. 단 Authentication이 AnonymousAuthenticationToken 만 아니면 됩니다.

  ```java
  로그인 == Authentication(authenticated = true) only if Authentication != AnonymousAuthenticationToken
  ```

  



## 기본 구조

- 필터들 중에 일부 필터는 인증 정보에 관여합니다. 이들 필터가 하는 일은 AuthenticationManager 를 통해 Authentication 을 인증하고 그 결과를 SecurityContextHolder 에 넣어주는 일입니다.

![img](https://gitlab.com/jongwons.choi/spring-boot-security-lecture/-/raw/master/images/fig-3-authentication.png)

### Security Filter

- UsernamePasswordAuthenticationFilter : 폼 로그인 -> UsernamePasswordAuthenticationToken
- RememberMeAuthenticationFilter : remember-me 쿠키 로그인 -> RememberMeAuthenticationToken
- AnonymousAuthenticationFilter : 로그인하지 않았다는 것을 인증함 -> AnonymousAuthenticationToken
- SecurityContextPersistenceFilter : 기존 로그인을 유지함(기본적으로 session 을 이용함)
- BearerTokenAuthenticationFilter : JWT 로그인
- BasicAuthenticationFilter : ajax 로그인 -> UsernamePasswordAuthenticationToken
- OAuth2LoginAuthenticationFilter : 소셜 로그인 -> OAuth2LoginAuthenticationToken, OAuth2AuthenticationToken
- OpenIDAuthenticationFilter : OpenID 로그인
- Saml2WebSsoAuthenticationFilter : SAML2 로그인
- ... 기타



### 인증 제공자

- Authentication 을 제공(Provide) 하는 AuthenticationProvider는 여러개가 동시에 존재할 수 있고, 인증 방식에 따라 ProviderManager 도 복수로 존재할 수 있습니다.



### 인증

- Authentication 은 인터페이스로 아래와 같은 정보들을 갖고 있습니다.
  - *Set<GrantedAuthority> authorities* : 인증된 권한 정보
  - *principal* : 인증 대상에 관한 정보. 주로 UserDetails 객체가 옴
  - *credentials* : 인증 확인을 위한 정보. 주로 비밀번호가 오지만, 인증 후에는 보안을 위해 삭제함.
  - *details* : 그 밖에 필요한 정보. IP, 세션정보, 기타 인증요청에서 사용했던 정보들.
  - *boolean authenticated* : 인증이 되었는지를 체크함.





## 폼로그인

### DefaultLoginPageGeneratingFilter

- GET /login 을 처리
- 별도의 로그인 페이지 설정을 하지 않으면 제공되는 필터
- 기본 로그인 폼을 제공
- OAuth2 / OpenID / Saml2 로그인과도 같이 사용할 수 있음.



### UsernamePasswordAuthenticationFilter

- POST /login 을 처리. processingUrl 을 변경하면 주소를 바꿀 수 있음.

- form 인증을 처리해주는 필터로 스프링 시큐리티에서 가장 일반적으로 쓰임.

- 주요 설정 정보

  - filterProcessingUrl : 로그인을 처리해 줄 URL (POST)
  - username parameter : POST에 username에 대한 값을 넘겨줄 인자의 이름
  - password parameter : POST에 password에 대한 값을 넘겨줄 인자의 이름
  - 로그인 성공시 처리 방법
    - defaultSuccessUrl : alwaysUse 옵션 설정이 중요
    - successHandler
  - 로그인 실패시 처리 방법
    - failureUrl
    - failureHandler
  - authenticationDetailSource : Authentication 객체의 details 에 들어갈 정보를 직접 만들어 줌.

  ```java
  @Override
  public Authentication attemptAuthentication(HttpServletRequest request, HttpServletResponse response)
  		throws AuthenticationException {
  	if (this.postOnly && !request.getMethod().equals("POST")) {
  		throw new AuthenticationServiceException("Authentication method not supported: " + request.getMethod());
  	}
  	String username = obtainUsername(request);
  	username = (username != null) ? username : "";
  	username = username.trim();
  	String password = obtainPassword(request);
  	password = (password != null) ? password : "";
  	UsernamePasswordAuthenticationToken authRequest = new UsernamePasswordAuthenticationToken(username, password);
  	// Allow subclasses to set the "details" property
  	setDetails(request, authRequest);
  	return this.getAuthenticationManager().authenticate(authRequest);
  }
  ```



### DefaultLogoutPageGeneratingFilter

- GET /logout 을 처리
- POST /logout 을 요청할 수 있는 UI 를 제공
- DefaultLoginPageGeneratingFilter 를 사용하는 경우에 같이 제공됨



### LogoutFilter

- POST /logout 을 처리. processiongUrl 을 변경하면 바꿀 수 있음.

- 로그 아웃을 처리

  - session, SecurityContext, csrf, 쿠키, remember-me 쿠키 등을 삭제처리 함.
  - (기본) 로그인 페이지로 redirect

  ```java
  private void doFilter(HttpServletRequest request, HttpServletResponse response, FilterChain chain)
  		throws IOException, ServletException {
  	if (requiresLogout(request, response)) {
  		Authentication auth = SecurityContextHolder.getContext().getAuthentication();
  		if (this.logger.isDebugEnabled()) {
  			this.logger.debug(LogMessage.format("Logging out [%s]", auth));
  		}
  		this.handler.logout(request, response, auth);
  		this.logoutSuccessHandler.onLogoutSuccess(request, response, auth);
  		return;
  	}
  	chain.doFilter(request, response);
  }
  ```

  

- LogoutHandler

  - void logout(HttpServletRequest request, HttpServletResponse response, Authentication authentication);
  - SecurityContextLogoutHandler : 세션과 SecurityContext 를 clear 함.
  - CookieClearingLogoutHandler : clear 대상이 된 쿠키들을 삭제함.
  - CsrfLogoutHandler : csrfTokenRepository 에서 csrf 토큰을 clear 함.
  - HeaderWriterLogoutHandler
  - RememberMeServices : remember-me 쿠키를 삭제함.
  - LogoutSuccessEventPublishingLogoutHandler : 로그아웃이 성공하면 이벤트를 발행함.

- LogoutSuccessHandler

  - void onLogoutSuccess(HttpServletRequest request, HttpServletResponse response, Authentication authentication) throws IOException, ServletException;
  - SimpleUrlLogoutSuccessHandler



## 기본 로그인 실습

1. build.gradle

   * thymeleaf 에 대한 의존성 추가

   ```gradle
   dependencies {
       implementation("$boot:spring-boot-starter-web")
       implementation("$boot:spring-boot-starter-thymeleaf")
       implementation("org.thymeleaf.extras:thymeleaf-extras-springsecurity5")
       implementation("nz.net.ultraq.thymeleaf:thymeleaf-layout-dialect")
   
   }
   ```

   

2. application.yml

   * 서버포트 설정

   ```yml
   server:
     port: 9051
   
   spring:
     devtools:
       livereload:
         enabled: true
       restart:
         enabled: true
   ```



3. controller

   * mapping
   * 접근제한

   ```java
   @Controller
   public class HomeController {
   
       @GetMapping("/")
       public String main(){
           return "index";
       }
   
       @GetMapping("/login")
       public String login(){
           return "loginForm";
       }
   
       @GetMapping("/login-error")
       public String loginError(Model model){
           model.addAttribute("loginError", true);
           return "loginForm";
       }
   
       @GetMapping("/access-denied")
       public String accessDenied(){
           return "AccessDenied";
       }
   
       @PreAuthorize("hasAnyAuthority('ROLE_USER')")	// 접근제한
       @GetMapping("/user-page")
       public String userPage(){
           return "UserPage";
       }
   
       @PreAuthorize("hasAnyAuthority('ROLE_ADMIN')")	// 접근제한
       @GetMapping("/admin-page")
       public String adminPage(){
           return "AdminPage";
       }
   
   
       @ResponseBody
       @GetMapping("/auth")
       public Authentication auth(){
           return SecurityContextHolder.getContext().getAuthentication();
       }
   
   }
   ```

   

4. config

   * 사용자 정보 추가
   * resource 가져오기
   * 권한 부여
   * 권한 상속
   * 성공시 페이지 이동
   * 실패시 페이지 이동

   ```java
   @EnableWebSecurity(debug = true)
   @EnableGlobalMethodSecurity(prePostEnabled = true)
   public class SecurityConfig extends WebSecurityConfigurerAdapter {
   	// CustomAuthDetail
       private final CustomAuthDetail customAuthDetail;
       public SecurityConfig(CustomAuthDetail customAuthDetail) {
           this.customAuthDetail = customAuthDetail;
       }
   	
       // 사용자 정보 추가
       @Override
       protected void configure(AuthenticationManagerBuilder auth) throws Exception {
           auth
                   .inMemoryAuthentication()
                   .withUser(
                           User.withDefaultPasswordEncoder()	// withDefaultPasswordEncoder는 테스트시만 사용(불안전)
                                   .username("user1")
                                   .password("1111")
                                   .roles("USER")
                   ).withUser(
                   User.withDefaultPasswordEncoder()
                           .username("admin")
                           .password("2222")
                           .roles("ADMIN")
           );
       }
   	
       // admin에 user 권한 상속
       @Bean
       RoleHierarchy roleHierarchy(){
           RoleHierarchyImpl roleHierarchy = new RoleHierarchyImpl();
           roleHierarchy.setHierarchy("ROLE_ADMIN > ROLE_USER");
           return roleHierarchy;
       }
   	
       // 권한 부여, 성공시, 실패시
       @Override
       protected void configure(HttpSecurity http) throws Exception {
           http	
                   .authorizeRequests(request->
                       request.antMatchers("/").permitAll()	// 모두 접근 가능한 홈페이지
                               .anyRequest().authenticated()	// 나머지는 인증이 필요
                   )
                   .formLogin(login->	
                           login.loginPage("/login")		// 로그인페이지 설정
                           .loginProcessingUrl("/loginprocess")
                           .permitAll()					// 로그인도 인증 필요설정 해놓으면 무한루프
                           .defaultSuccessUrl("/", false)	// 성공시 홈페이지가 아닌 눌렀던 페이지로 이동, 로그인 눌렀었으면 홈페이지로
                           .authenticationDetailsSource(customAuthDetail)
                           .failureUrl("/login-error")	// 실패시 login-error페이지로 이동
                   )
                   .logout(logout->	
                           logout.logoutSuccessUrl("/"))	// 로그아웃 성공하면 홈페이지로
                   .exceptionHandling(error->				// 접근 불가 페이지 설정
                           error.accessDeniedPage("/access-denied")	
                   )
                   ;
       }
   	
       // resource(css,js,images,jars)에 security 적용안하기 -> 권한 상관업이 사용가능
       @Override
       public void configure(WebSecurity web) throws Exception {
           web.ignoring()
                   .requestMatchers(
                           PathRequest.toStaticResources().atCommonLocations()
                   )
           ;
       }
   
   }
   ```

   

5. html

   * bootstrap 을 이용해 기본 페이지 제작

     ```html
     <html lang="ko" xmlns:th="http://www.thymeleaf.org" xmlns:sec="http://www.thymeleaf.org/extras/spring-security">
     <head>
         <meta charset="UTF-8">
         <title>Title</title>
         <link rel="stylesheet" th:href="@{/css/bootstrap.css}">
         <link rel="stylesheet" th:href="@{/css/login.css}">
     </head>
         
     <body>
     <script th:src="@{/js/bootstrap.js}" />
     </body>
     </html>
     ```

   * csrf 설정 

     * 사용자 정보 가져오기위해 필요함

     * th:action="@{경로}" 써서 csrf 토큰 설정

       ```html
       <form class="form-signin" method="post" th:action="@{/loginprocess}">
       ```

   * 로그인

     * 로그인 안했을 때만 보임

       sec:authorize="!isAuthenticated() 추가

     ```html
     <div class="link" sec:authorize="!isAuthenticated()">
         <a href="/login">  로그인 </a>
     </div>
     ```

   * 유저 페이지, 관리자 페이지

     * 로그인 했을 때만 보임

       sec:authorize="isAuthenticated() 추가

     ```html
     <div class="link" sec:authorize="authenticated">
         <a href="/user-page">  유저 페이지  </a>
     </div>
     <div class="link" sec:authorize="authenticated">
         <a href="/admin-page">  관리자 로그인 </a>
     </div>
     ```

   * 로그아웃 

     * 로그인 했을 때만 보임

       sec:authorize="isAuthenticated() 추가

     * csrf 설정

       사용자 정보 가져오기위해 필요함

       th:action="@{경로}" 써서 csrf 토큰 설정

     ```html
     <div class="link" sec:authorize="authenticated">
         <form th:action="@{/logout}" method="post">
             <button class="btn btn-info" type="submit">  로그아웃 </button>
         </form>
     </div>
     ```

     

6. UserDetailsSource 설정

   1. dto class 생성(detail 정보)

      ```java
      @Data
      @AllArgsConstructor
      @NoArgsConstructor
      @Builder
      public class RequestInfo {
          private LocalDateTime loginTime;
          private String remoteIp;
          private String sessionId;
      }
      ```

   2. CustomDetail class 생성

      ```java
      @Component
      public class CustomAuthDetail implements AuthenticationDetailsSource<HttpServletRequest, RequestInfo> {
      
          @Override
          public RequestInfo buildDetails(HttpServletRequest request) {
              return RequestInfo.builder()
                      .loginTime(LocalDateTime.now())
                      .remoteIp(request.getRemoteAddr())
                      .sessionId(request.getSession().getId())
                      .build();
          }
      }
      ```

   3. config class에 추가

      ```java
      // 주입
      private final CustomAuthDetail customAuthDetail;	
      
      public SecurityConfig(CustomAuthDetail customAuthDetail) {
          this.customAuthDetail = customAuthDetail;
      }
      ```

      

   

# Authentication 메커니즘

![img](https://gitlab.com/jongwons.choi/spring-boot-security-lecture/-/raw/master/images/fig-6-Authentication.png)

* 구성

  * Credentials : 인증을 받기 위해 필요한 정보, 비번등 (input)

  * Principal : 인증된 결과. 인증 대상 (output)

  * Details : 기타 정보, 인증에 관여된 된 주변 정보들

  * Authorities : 권한 정보들

* Token

  * Authentication 구현 객체

* SecurityContextHolder 

  * Authentication에 언제든 접근할 수 있도록 필터체인에서 보장

* 인증 제공자(AuthenticationProvider)

  ![img](https://gitlab.com/jongwons.choi/spring-boot-security-lecture/-/raw/master/images/fig-7-AuthenticationProvider.png)

  * 인증에 필요한 정보(credentials) 받아서 결과물(Principal을 줌
  * 어떤 인증에 대해서 도장을 찍어줄지 AuthenticationManager 에게 알려줘야 하기 때문에 support() 라는 메소드를 제공
  * 인증 대상과 방식이 다양할 수 있기 때문에 인증 제공자도 여러개 올 수 있음

* 인증 관리자(AuthenticationManager)

  ![img](https://gitlab.com/jongwons.choi/spring-boot-security-lecture/-/raw/master/images/fig-8-AuthenticationManager.png)

  * 인증 제공자들을 관리하는 인터페이스
  * 인증 관리자를 구현한 객체가 ProviderManager
  * 개발자가 직접 AuthenticationManager를 정의해서 제공하지 않는다면, AuthenticationManager 를 만드는 AuthenticationManagerFactoryBean 에서 DaoAuthenticationProvider 를 기본 인증제공자로 등록한 AuthenticationManage를 만든다.
  * DaoAuthenticationProvider 는 반드시 1개의 UserDetailsService 를 발견할 수 있어야 한다. 만약 없으면 InmemoryUserDetailsManager 에 [username=user, password=(서버가 생성한 패스워드)]인 사용자가 등록되어 제공됩니다.



## 실습

1. Authentication 

   1. token 구현 : Authentication 구현체()

      * Credentials : 인증을 받기 위해 필요한 정보, 비번등 (input)
      * Principal : 인증된 결과. 인증 대상 (output)
      * Details : 기타 정보, 인증에 관여된 된 주변 정보들
      * Authorities : 권한 정보들

      ```java
      @Data
      @AllArgsConstructor
      @NoArgsConstructor
      @Builder
      public class StudentAuthenticationToken implements Authentication {	// Authentication 구현
      
          private Student principal;
          private String credentials;
          private String details;
          private boolean authenticated;
          private Set<GrantedAuthority> authorities;
      
      
          @Override
          public String getName() {
              return principal == null ? "" : principal.getUsername();
          }
      
      }
      ```

   2. Principal(인증 결과물) dto 구현

      ```java
      @Data
      @AllArgsConstructor
      @NoArgsConstructor
      @Builder
      public class Student {	// Pricipal(인증 결과물)
      
          private String id;
          private String username;
          private Set<GrantedAuthority> role;
      
      }
      ```

      

2. AuthenticationProvider 

   * credentials가 있으면 -> Principal 제공
   * supports
   * 사용자 정보 입력

   ```java
   @Component
   public class StudentManager implements AuthenticationProvider, InitializingBean {
   
       private HashMap<String, Student> studentDB = new HashMap<>();
   	
       @Override
       public Authentication authenticate(Authentication authentication) throws AuthenticationException {
           StudentAuthenticationToken token = (StudentAuthenticationToken) authentication;
           if(studentDB.containsKey(token.getCredentials())){	// 인증 성공
               Student student = studentDB.get(token.getCredentials());
               return StudentAuthenticationToken.builder()
                       .principal(student)
                       .details(student.getUsername())
                       .authenticated(true)
                       .authorities(student.getRole())
                       .build();
           }
           return null;
       }
   
       @Override
       public boolean supports(Class<?> authentication) {
           return authentication == StudentAuthenticationToken.class;
       }
   	
       // 사용자 정보
       @Override
       public void afterPropertiesSet() throws Exception {
           Set.of(
                   new Student("hong", "홍길동", Set.of(new SimpleGrantedAuthority("ROLE_STUDENT"))),
                   new Student("kang", "강아지", Set.of(new SimpleGrantedAuthority("ROLE_STUDENT"))),
                   new Student("rang", "호랑이", Set.of(new SimpleGrantedAuthority("ROLE_STUDENT")))
           ).forEach(s->
               studentDB.put(s.getId(), s)
           );
       }
   }
   ```

   

3. AuthenticationManager : AuthenticationProvider 관리

   1. config에 추가

      ```java
      	// DI
      	private final StudentManager studentManager;
          private final TeacherManager teacherManager;
      
          public SecurityConfig(StudentManager studentManager, TeacherManager teacherManager) {
              this.studentManager = studentManager;
              this.teacherManager = teacherManager;
          }
      
      	// AuthenticationManager
          @Override
          protected void configure(AuthenticationManagerBuilder auth) throws Exception {
              auth.authenticationProvider(studentManager);
              auth.authenticationProvider(teacherManager);
          }
      ```

      

4. CustomLoginFilter 

   * UsernamePasswordAuthenticationFilter  custom함 

   ```java
   public class CustomLoginFilter extends UsernamePasswordAuthenticationFilter {
   	
       // 생성자
       public CustomLoginFilter(AuthenticationManager authenticationManager){
           super(authenticationManager);
       }
   	
       // UsernamePasswordAuthenticationFilter custom
       @Override
       public Authentication attemptAuthentication(HttpServletRequest request, HttpServletResponse response) throws AuthenticationException {
           String username = obtainUsername(request);
           username = (username != null) ? username : "";
           username = username.trim();
           String password = obtainPassword(request);
           password = (password != null) ? password : "";
           String type = request.getParameter("type");
           if(type == null || !type.equals("teacher")){	// 맞는 Authentication 선택
               // student
               StudentAuthenticationToken token = StudentAuthenticationToken.builder()	// credentials
                       .credentials(username).build();
               return this.getAuthenticationManager().authenticate(token);				// pricipal
           }else{
               // teacher
               TeacherAuthenticationToken token = TeacherAuthenticationToken.builder()	// credentials
                       .credentials(username).build();
               return this.getAuthenticationManager().authenticate(token);				// pricipal
           }
       }
   
   
   }
   ```

   

## 실제

* AuthenticationProvider 보통 안건들임
  * 보통 UserDetails 객체를 구현 -> UserDetailsService를 만들어서 Bean으로 제공 ->DaoAuthenticationProvider가 알아서함
  * 즉 UserDetails를 구현하고, UserDetailsService 구현해서 Bean으로 제공하면 됨



# Basic 토큰인증

## BasicAuthenticationFilter

* 로그인 페이지를 사용할 수 없는 상황에서 사용
  * SPA 페이지 (react, angular, vue ...)
  * 브라우저 기반의 모바일 앱(브라우저 개반의 앱, ex: inoic )

* 사용방법

  ```java
  public class SecurityConfig extends WebSecurityConfigurerAdapter {
  
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
                .httpBasic()
                ;
    }
  }
  ```

* SecurityContext 에 인증된 토큰이 없다면 아래와 같은 포멧의 토큰을 받아서 인증처리를 하고 갑니다.

  ![img](https://gitlab.com/jongwons.choi/spring-boot-security-lecture/-/raw/master/images/fig-10-basic-authentication-filter-hello.png)

* http 에서는 header 에 username:password 값이 묻어서 가기 때문에 보안에 매우 취약합니다. 그래서, 반드시 https 프로토콜에서 사용할 것을 권장

* 최초 로그인시에만 인증을 처리하고, 이후에는 session에 의존

* RememberMe 를 설정한 경우 session 만료가 되도 로그인 안해도 됨

* 에러가 나면 401 (UnAuthorized) 에러 보냄

* 로그인 페이지 처리

  ![img](https://gitlab.com/jongwons.choi/spring-boot-security-lecture/-/raw/master/images/fig-10-basic-filter-user.png)

## SecurityContextPersistenceFilter

* SecurityContext 를 저장하고 있는 저장소에서 만료되지 않은 인증이 있으면 SecurityContextHolder 에 넣어줌
* 저장소가 반드시 세션일 필요는 없기 때문에 HttpSessionContextIntegrationFilter가 추상화된 객체로 발전된 필터



## Bearer 토큰

* JWT 토큰

* Opaque 토큰