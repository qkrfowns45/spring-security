# spring-security
스프링 security 학습하기

## spring Security란?
> Spring Security는 Spring 기반의 애플리케이션의 보안(인증과 권한, 인가 등)을 담당하는 스프링 하위 프레임워크이다. Spring Security는 '인증'과 '권한'에 대한 부분을 Filter 흐름에 따라 처리하고 있다. Filter는 Dispatcher Servlet으로 가기 전에 적용되므로 가장 먼저 URL 요청을 받지만, Interceptor는 Dispatcher와 Controller사이에 위치한다는 점에서 적용 시기의 차이가 있다. Spring Security는 보안과 관련해서 체계적으로 많은 옵션을 제공해주기 때문에 개발자 입장에서는 일일이 보안관련 로직을 작성하지 않아도 된다는 장점이 있다.

![initial](https://user-images.githubusercontent.com/71062404/205061523-8f5c41bf-91ce-4002-8791-fcad4016123c.png)

## Authorization과 Authentication
+ Authorization : 해당 사용자가 본인이 맞는지를 확인하는 절차
+ Authentication : 인증된 사용자가 요청한 자원에 접근 가능한지를 결정하는 절차
> 기본적으로 인증을 성공 후 인가를 받는다. 인가 과정에서 해당 리소스에 대한 접근 권한이 있는지 확인을 하게 된다. Spring Security에서는 이러한 인증과 인가를 위해 Principal을 아이디로, Credential을 비밀번호로 사용하는 Credential 기반의 인증 방식을 사용한다. 
+ principal : 보호받는 Resource에 접근하는 대상
+ Credential : Resource에 접근하는 대상의 비밀번호

## Spring Security 모듈

+ SpringContextHolder
> 보안 주체의 세부 정보를 포함하여 응용프로그램의 현재 보안 컨텍스트에 대한 세부 정보가 저장된다. SecurityContextHolder는 기본적으로 SecurityContextHolder.MODE_INHERITABLETHREDLOCAL 방법과 SecurityContextxHolder.MODE_THREADLOCAL 방법을 제공한다.

+ SecurityContext
> Authentication을 보관하는 역할을 하며, SecurityContext를 통해 Authentication 객체를 가져올 수 있다.

+ Authentication
> 현재 접근하는 주체의 정보와 권한을 담은 인터페이스이다. Authentication 객체는 Security Context에 저장되며, SecurityContextHolder를 통해 SecurityContext에 접근하고, SecurityContext를 통해 Authentication에 접근할 수 있다.

+ UsernamePasswordAuthenticationToken
> Authentication을 implements한 AbstractAuthenticationToken의 하위 클래스로 User의 ID가 Principal 역할을 하고, Password가 Credential의 역할을 한다. UsernamePasswordAuthenticationToken의 첫 번째 생성자는 인증 전의 객체를 생성하고, 두 번째 생성자는 인증이 완료된 객체를 생성한다.

+ AuthenticationProvider
> 실제 인증에 대한 부분을 처리하는데, 인증 전의 Authentication 객체를 받아서 인증이 완료된 객체를 반환하는 역할을 한다. 아래와 같은 AuthenticationProvider 인터페이스를 구현해서 Custom한 AuthenticationProvider을 작성해서 AuthenticationManager에 등록하면 된다.

+ AuthenticationManager
> 인증에 대한 부분은 AuthenticationManager를 통해 처리하는데 실질적으로는 AuthenticationManager에 등록된 AuthenticationProvider에 의해 처리된다. 인증에 성공하면 2번째 생성자를 이용해 인증이 성공한 (isAuthenticated = true) 객체를 생성하여 SecurityContext에 저장한다. 인증 상태를 유지하기 위해 세션에 보관하며, 인증이 실패한 경우에는 AuthenticationException을 발생시킨다. AuthenticationManager를 implements한 ProviderManager는 실제 인증 과정에 대한 로직을 가지고 있는 AuthenticationProvider를 List로 가지고 있으며, ProviderManager는 loop를 통해 모든 provider를 조회하면서 authenticate 처리를 한다.

+ UserDetails
> 인증에 성공하여 생성된 UserDetails는 Authentication 객체를 구현한 UsernamePasswordAuthenticationToken을 생성하기 위해 사용된다. UserDetails 인터페이스를 살펴보면 아래와 같이 정보를 반환하는 메서드를 가지고 있다. UserDetails 를 implements 하여 처리할 수 있다.

+ UserDetailsService
> UserDetails 객체를 반환하는 단 하나의 메서드를 가지고 있는데 일반적으로 UserRepository에서 주입하여 DB와 연결하여 처리한다.

+ PasswordEncoding
> AuthenticationManagerBuilder.userDetailsService().passwordEncoder()를 통해 패스워드 암호화에 사용될 PasswordEncoder 구현체를 지정할 수 있다.

+ GrantedAuthority
> GrantAuthority는 현재 사용자(principal)가 가지고 있는 권한을 의미한다. ROLE_ADMIN이나 ROLE_USER와 같이 ROLE_*의 형태로 사용하며, 보통 "roles"라고 한다. GrantedAuthority 객체는 UserDetailsService에 의해 불러올 수 있고, 특정 자원에 대한 권한이 있는지를 검사하여 접근 허용 여부를 결정한다.

