---
  
title: "[Spring] Spring Security filter 2번 발생"
author: piacu
date: 2023-05-24 15:06:00 +0900
categories: [Trouble Shooting, Spring]
tags: [trouble shooting, spring, filter]

---

# 증상

![0524](.\assets\0524.png)토큰 관련 클래스인 JwtTokenProvider에 작성해둔 토큰 검증 메서드에서 적절하지 않은 토큰을 넣고 요청했을 때 Exception이 발생하게 되면, 넘어가는 Exception 메소드에서 상황에 따라 다른 내용이 출력되게 된다. 이 출력되는 내용이 2번씩 뜨는 증상이 있었다.



# 오류 추정

1. 개발 툴인 Intellij의 문제인 줄 알고 컴퓨터를 재부팅했다 -- 실패
2. 출력되는 내용을 지워버렸다 -> 내용이 아예 뜨지 않는다. -- 실패



# 해결 방법

나와 똑같은 증상을 겪고 있는 사람이 꽤 있었고, 결론적으론 아래 1번째 코드에서 2번째 코드로 변경하니 오류가 없어졌다.

filter가 두 번 적용되는 오류였는데, 스프링은 Bean으로 등록(Bean으로 자동 등록되는 것 중 하나가 @Component 어노테이션)되면 필터를 자동 적용해주는데, SecurityConfig 파일에서 .addFilterBefore()로 필터를 한번 더 적용시켜줘서 그랬던 것이었다.

1.

```java
public class JwtAuthenticationFilter extends GenericFilterBean {
    private final JwtTokenProvider jwtTokenProvider;

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain filterChain) throws IOException, ServletException {
        String token = resolveToken((HttpServletRequest) request);

        if (StringUtils.hasText(token) && jwtTokenProvider.validateToken(token)) {
            Authentication authentication = jwtTokenProvider.getAuthentication(token);
            SecurityContextHolder.getContext().setAuthentication(authentication);
        }

        filterChain.doFilter(request, response);
    }

		private String resolveToken(HttpServletRequest request) {
        String bearerToken = request.getHeader("Authorization");
        if (StringUtils.hasText(bearerToken) && bearerToken.startsWith("Bearer ")) {
            return bearerToken.substring(7);
        }
        return null;
    }
}
```

2.

```java
public class JwtAuthenticationFilter extends OncePerRequestFilter {
    private final JwtTokenProvider jwtTokenProvider;

    @Override
    public void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) throws IOException, ServletException {
        String token = resolveToken(request);

        if (StringUtils.hasText(token) && jwtTokenProvider.validateToken(token)) {
            // 토큰이 유효할 경우 토큰에서 Authentication 객체를 가지고 와서 SecurityContext 에 저장
            Authentication authentication = jwtTokenProvider.getAuthentication(token);
            SecurityContextHolder.getContext().setAuthentication(authentication);
        }

        filterChain.doFilter(request, response);
    }

		private String resolveToken(HttpServletRequest request) {
        String bearerToken = request.getHeader("Authorization");
        if (StringUtils.hasText(bearerToken) && bearerToken.startsWith("Bearer ")) {
            return bearerToken.substring(7);
        }
        return null;
    }
}
```

GenericFilterBean을 상속받었던 것을 OncePerRequestFilter를 상속받는 걸로 변경했는데, 다음과 같은 변경점이 있었다.

1. OncePerRequestFilter는 filter가 한 번만 적용되는 걸 보장한다.
2. 구현체가 다르다. GenericFilterBean은 doFilter를 구현체로 가지고 있고, OncePerRequestFilter는 doFilterInternal를 구현체로 가지고 있다. 이로 인해 매개변수도 `Servlet` → `HttpServlet`으로 바뀌었기 때문에 `(HttpServletRequest)`를 사용한 명시적 형변환을 할 필요도 사라졌다.



# 참고자료

https://velog.io/@dhtjdals77/Spring-filter%EA%B0%80-2%EB%B2%88-%EC%8B%A4%ED%96%89%EB%90%98%EB%8A%94-%EC%9D%B4%EC%9C%A0
