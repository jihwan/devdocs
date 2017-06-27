## Spring Security

### 보안 3요소
- Principal
  - 접근주체
  - 보호된 대상에 접근하는 사용자
  - **Authentication**
- Authenticate
  - 인증
  - 현재 사용자가 누구인지 확인
  - 일반적으로 아이디/패스워드 이용
  - **AuthenticationManager**
- Authorize
  - 인가
  - 현재 사용자가 특정 대상(URL,비즈니스)을 사용 혹은 접근할 권한이 있는가?
  - **AccessDecisionManager**

spring security는 바로 이용할 수 있는 기능들이 이미 상당수 구현되어 있음에 따라, 내부 코드를 확인해 볼 필요가 있음.

### Authentication
- 현재 접근 주체 정보를 담고있다.
- 인증 요청시 요청 정보를 담고있다.
- Principal(aka. username, 인증주체정보), Credential(aka. password, 증명값), details, authorities(GrantedAuthority Collection, 권한 컬렉션) 로 구성

> SecurityContext
- Authentication 보관
- Spring Security 여러 모듈에서는 Authentication 객체를 얻기위해 SecurityContext에서 구한다.

> SecurityContextHolder
- SecurityContext 보관
- 기본 보관기법은 ThreadLocal 이용

### AuthenticationManager
인증을 처리함

```java
public interface AuthenticationManager {
	Authentication authenticate(Authentication authentication)
			throws AuthenticationException;
}
```

### AccessDecisionManager
인가를 처리함

```java
public interface AccessDecisionManager {
  void decide(
    Authentication authentication, // 사용자정보
    Object object,                 // 접근자원
    Collection<ConfigAttribute> configAttributes // 접근자원의 보안설정
  ) throws AccessDeniedException, InsufficientAuthenticationException;
  boolean supports(ConfigAttribute attribute);
  boolean supports(Class<?> clazz);
}
```

### 웹 어플리케이션 보안 지원
- 필터 체인을 이용한 보안 처리

```xml
<filter>
  <filter-name>springSecurityFilterChain</filter-name>
  <filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class>
</filter>
<filter-mapping>
  <filter-name>springSecurityFilterChain</filter-name>
  <url-pattern>/*</url-pattern>
</filter-mapping>
```

FilterChainProxy 에서 등록된 여러 filter 들이 chaining 되어 Authentication 생성, 인증과 인가 처리를 한다.

DelegatingFilterProxy
  - FilterChainProxy
    - VirtualFilterChain

### 주요 보안 필터

Filter | 기능
--- | ---
**SecurityContextPersistenceFilter** | SecurityContext 관리
LogoutFilter | logout 요청 처리
**UsernamePasswordAuthenticationFilter** | 인증 요청 처리
RememberMeAuthenticationFilter | remember me 처리
**AnonymousAuthenticationFilter** | 익명 사용자 처리
ExceptionTranslationFilter | 익셉션 처리
**FilterSecurityInterceptor** | 접근 권한 처리

- 필터체인 상에 등록된 필터들은 등록된 순서대로 실행 되고, 등록 전후 관계를 고려한 순서가 있음
- 필터에 따라 특별히 사용하거나 관리되는 url 이 있음
- 필터에 따라 요청을 처리하고 실행을 끝내기도 함

### 보호된 웹 요청 아키텍쳐

### 인증 절차
AbstractAuthenticationProcessingFilter를 확장한 UsernamePasswordAuthenticationFilter에서 담당한다.

AuthenticationManager 와 협력하여 인증처리를 하고, 
인증 성공(successfulAuthentication)과 실패(unsuccessfulAuthentication)에 따른 처리를 수행한다.

인증처리를 하는 AuthenticationManater 는 AuthenticationProvider 와 협력하여 인증처리를 한다.

AuthenticationManater 구현체는 ProviderManager 이다.
AuthenticationProvider 대표 구현체로는 AnonymousAuthenticationProvider, RememberMeAuthenticationProvider, DaoAuthenticationProvider 있다.

대부분 인증처리는 database에 저장된 정보를 이용하여 하게 되는데, DaoAuthenticationProvider 가 그 역할을 담당한다.
DaoAuthenticationProvider 는 UserDetailsService 와 협력하게 된다.

최종적으로 Authentication 객체 (principal, credentials, authorities, details) 를 리턴한다.

결론, 개발자는 UserDetailsService 를 구현하면 된다. 

### 권한부여,확인 절차
FilterSecurityInterceptor (특정 요청을 받아들일지 거부할지를 결정하는 일을 담당)

권한부여는 이진결정이다. 즉, 접근 가능하거나 못하거나 둘 중 하나다. 

AccessDecisionManager 가 권한부여 결정을 책임진다. 접근 거부시 AccessDeniedException 이 발생한다.

AccessDecisionManager 는 AccessDecisionVoter 와 협력한다.

AccessDecisionVoter 구현체인 WebExpressionVoter 에서 수행한다.

### 로그아웃
LogoutFilter 가 담당. logout시 수행해야 하는 일련의 작업은 LogoutHandler 로 위임한다.
LogoutHandler 작업이 여러개 이므로 Composite 패턴을 이용한 CompositeLogoutHandler 에 여러 handler 를 등록하게 됨.

CookieClearingLogoutHandler, AbstractRememberMeServices, SecurityContextLogoutHandler 등이 사용된다.

LogoutHandler 구현체 들은 처리시 예외를 용납해서는 안된다. 모든 예외는 적절히 처리되어야 하고, 예외가 발생한다면 로그를 남기고 대응 처리를 해야 하겠다.

### remember me
암호화한 쿠키를 사용자 브라우저에 저장해 재방문하는 사용자를 기억하는 기능

RememberMeConfigurer 를 통해 remember me 기능을 위한 관련 빈들이 등록 된다. PersistentTokenRepository 등록 여부에 따라, 

등록되는 RememberMeServices 가 달라진다. PersistentTokenRepository 없다면, TokenBasedRememberMeServices 가 등록된다.

PersistentTokenRepository 가 등록되었다면, PersistentTokenBasedRememberMeServices 이 등록된다. 둘의 차이점은, 서버 재시작에 따라

동작을 하는가 아닌가에 대한 차이점이다.

remember key 설정

RememberMeAuthenticationFilter 에서 쿠키 값을 검사하여 remember me 쿠키일 경우 사용자 인증에 해당 쿠키 정보를 활용하여 인증한다.

**remember me 쿠키를 사용하여 인증받은 사용자와 username/password 를 사용하여 인증받은 사용자를 구분할 수 있다.**

remember me 를 이용한 사용자가 민감한 정보에 접근할 경우에 사용자 자신을 인증하도록 강제 할 필요가 있다.

```xml
<intercept-url pattern="/account/**" access="hasRole('ROLE_USER') and fullyAuthenticated" />
```

### 데이터베이스 기반 인증
UserDetailsService 를 구현한 org.springframework.security.core.userdetails.jdbc.JdbcDaoImpl 제공

[default schema](http://docs.spring.io/spring-security/site/docs/4.2.2.RELEASE/reference/htmlsingle/#user-schema) 참조

비밀번호 변경등 사용자 관리 기능을 위한 커스터마이징이 필요하다면, JdbcDaoImpl 을 확장하거나 참고하여 구현하면 되겠지만, JdbcUserDetailsManager (UserDetailsManager, GroupManager 구현)를 활용하는 것도 고려할 만 한다.

### remember me 기능 데이터베이스 확장
remember me 데이터베이스 영속화를 위한 스키마는 [여기](http://docs.spring.io/spring-security/site/docs/4.2.2.RELEASE/reference/htmlsingle/#persistent-login-remember-me-schema) 참조.

### SSL 고려

### 기존 인증 시스템 연동 ???

### 미세접근제어

- 어플리케이션 보안 및 사용자/그룹 지정
- 여어플리케이션 페이지 영역에 영향을 미치는 미세 권한부여
- 메소드 annotation, aop 이용 비즈니스 티어 보안 적용 기법
- 데이터 컬렉션에 대해 역할 기반 필터링 수행하는 어노테이션 기반 보안 기능

사용자 역할 기획

페이지 레벨 보안 기획

다양한 미세 권한 부여 방법

미세 권한 부여란? 특정 사용자의 요청 컨텍스트에 따라 어플리케이션 기능을 사용할 수 있게 하는 것을 말함.
즉, 페이지 전체에 대한 접근 제한을 하는게 아닌, 페이지의 특정 영역을 선택적으로 접근할 수 있게 한다.
권한에 따라 페이지 영역을 조건적 렌더링 함.


### 비즈니스티어 보호
- 사전권한부여 (Pre - Authorization)
  허용된 메소드가 실행되기 전에 특정 제약 조건들을 항상 충족시켜준다.
- 사후권한부여 (Post - Authorization)
  호출하는 주체가 메소드가 반환된 후에도 선언된 제약 조건을 만족시키도록 보장.

비즈니스 메소드 보안의 기본

PreAuthorize


MapBasedMethodSecurityMetadataSource


MethodSecurityExpressionHandler