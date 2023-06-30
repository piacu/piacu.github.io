---
  
title: "[Spring] Spring Security .antMatchers() 메소드 없음"
author: piacu
date: 2023-05-23 12:00:00 +0900
categories: [Trouble Shooting, Spring]
tags: [trouble shooting, spring, spring security]

---

# 증상

```java
@Configuration
@EnableWebSecurity
@RequiredArgsConstructor
public class SecurityConfig {
 
    private final JwtTokenProvider jwtTokenProvider;
 
    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
                .httpBasic().disable()
                .csrf().disable()
                .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS)
                .and()
                .authorizeRequests()
                .antMatchers("/members/login").permitAll()
                .antMatchers("/members/test").hasRole("USER")
                .anyRequest().authenticated()
                .and()
                .addFilterBefore(new JwtAuthenticationFilter(jwtTokenProvider), UsernamePasswordAuthenticationFilter.class);
        return http.build();
    }
 
    @Bean
    public PasswordEncoder passwordEncoder() {
        return PasswordEncoderFactories.createDelegatingPasswordEncoder();
    }
}
```

스프링 시큐리티 적용을 위해 예제 코드를 보며 작성하다가 http 설정의 .antMatchers()가 나의 스프링부트 프로젝트에서는 만들어지지 않았다.



# 오류 추정

1. build.gradle 에서 import한 것에 문제가 있었나? -- 문제 없음
2. .antMatchers() 메소드가 삭제되었나? -- **삭제됨**



# 해결 방법

구글링 중 스프링 공식 문서 중 [Configuration Migrations](https://docs.spring.io/spring-security/reference/5.8/migration/servlet/config.html)에서 답변을 찾을 수 있었다!

읽어보니 스프링 시큐리티 5.8버전부터는 `antMatchers`, `mvcMatchers`, `regexMatchers` 는 구식(deprecated)이 되어버리고 `requestMatchers`로 변경되었다고 한다.

새로운 `requestMatchers` 메소드는…

- classpath에 Spring MVC가 있으면 MvcRequestMatcher 구현
- 없으면 antPathRequestMatcher 구현으로 동작

한다고 한다. 결론적으로는 기존 메소드들이 합쳐진 것 같다.



어쨌든 구식이 된 위 3개 메소드는 Spring Security 6버전 부터 삭제되었는데 6.0.3버전을 쓰고 있어서 매우 최근에 변경된 것 같았다. 최신버전의 설움...

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http
            .authorizeHttpRequests((authz) -> authz
                .requestMatchers("/api/admin/**").hasRole("ADMIN")
                .requestMatchers("/api/user/**").hasRole("USER")
                .anyRequest().authenticated()
            );
        return http.build();
    }

}
```

상기 코드로 변경하니 작동 성공!
