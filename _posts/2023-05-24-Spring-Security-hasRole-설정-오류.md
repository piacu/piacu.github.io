---
  
layout: post
title: "[Spring] Spring Security .hasRole() 미적용 이슈"
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
                // REST API이므로 basic auth 및 csrf 보안을 사용하지 않는다는 설정
                .httpBasic().disable()
                .csrf().disable()
                // JWT를 사용하기 때문에 세션을 사용하지 않는다는 설정
                .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS)
                .and()
                .authorizeHttpRequests((authz) -> authz
                        // 모든 요청 허가
                        .requestMatchers("/user/login", "/user/id-check", "/user/sign-up").permitAll()
                        // ADMIN권한이 있어야 요청 허가
                        .requestMatchers("/admin/**").hasRole("ADMIN")
                        // 이밖에 모든 요청에 대해 인증 필요
                        .anyRequest().authenticated())
                // JWT 인증을 위하여 직접 구현한 필터를 UsernamePasswordAuthenticationFilter 전에 실행하겠다는 설정
                .addFilterBefore(new JwtAuthenticationFilter(jwtTokenProvider), UsernamePasswordAuthenticationFilter.class);

        return http.build();
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return PasswordEncoderFactories.createDelegatingPasswordEncoder();
    }
}
```

위 코드에서 `/admin` 이하로 들어오는 요청은 `ADMIN` 권한이 인가되어 있어야만 접근하게 해주고 싶었다.

DB에는 user_roles 테이블에

FK인 user_id와 roles가 컬럼으로 있고, roles의 value로는 `ADMIN`, `USER` 등이 저장되어 있었다.



# 오류 추정

UserDetails를 상속받는 곳에서 role을 리스트 형태로 선언해서 그런가 해서 String 형태로 바꿈 - 실패

.requestMatchers(“/admin/**”)이 아니라 .requestMatchers(“/admin”)로 해야하나? - 당연히 실패

곰곰히 생각해보다가 hasRole()에 대해 찾아보았다.



# 해결 방법

hasRole()이나 hasAuthority() 둘 다 특정 권한을 가지고 있는 사용자만 접근 가능한데, hasRole() 사용 시에 권한 앞에 자동으로  `ROLE_` 접두사를 붙여준다고 한다. 이게 문제가 되어 나는 DB에 role들을 `USER`, `ADMIN` 등의 형태로 저장해두었는데 `ROLE_USER`, `ROLE_ADMIN`이 되어버리니 찾을 수 없어 해당 엔드포인트에 접근할 수 없었던 것이다.

너무 직관적이지 않은 부분이라고 생각한다. 바뀌었으면 좋겠다.



# 출처 및 참고자료

https://whitepro.tistory.com/494
