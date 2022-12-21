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
- DefaultLoginPageGeneratingFilter 를 사용하는 경우에 같이 제공됨.



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