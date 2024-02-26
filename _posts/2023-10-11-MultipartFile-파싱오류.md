---
  
title: "[Spring] Failed to parse multipart servlet request"
author: piacu
date: 2023-05-08 12:00:00 +0900
categories: [Trouble Shooting, Spring]
tags: [trouble shooting, spring, multipartfile]

---

# 증상

사진 올리기 기능을 postman을 이용하여 테스트하려는데 분명 어제까진 없던 문제인 `Failed to parse multipart servlet request` 가 발생하여 당황스러웠다.

![image](https://github.com/piacu/piacu/assets/26267376/766bf58d-3401-4464-a9fa-e4ff1ea4669a)

```
org.springframework.web.multipart.MultipartException: Failed to parse multipart servlet request
	at org.springframework.web.multipart.support.StandardMultipartHttpServletRequest.handleParseFailure(StandardMultipartHttpServletRequest.java:122) ~[spring-web-6.0.6.jar:6.0.6]
	at org.springframework.web.multipart.support.StandardMultipartHttpServletRequest.parseRequest(StandardMultipartHttpServletRequest.java:110) ~[spring-web-6.0.6.jar:6.0.6]
	at org.springframework.web.multipart.support.StandardMultipartHttpServletRequest.<init>(StandardMultipartHttpServletRequest.java:86) ~[spring-web-6.0.6.jar:6.0.6]
	at org.springframework.web.multipart.support.StandardServletMultipartResolver.resolveMultipart(StandardServletMultipartResolver.java:112) ~[spring-web-6.0.6.jar:6.0.6]
	at org.springframework.web.servlet.DispatcherServlet.checkMultipart(DispatcherServlet.java:1219) ~[spring-webmvc-6.0.6.jar:6.0.6]
	at org.springframework.web.servlet.DispatcherServlet.doDispatch(DispatcherServlet.java:1053) ~[spring-webmvc-6.0.6.jar:6.0.6]
	at org.springframework.web.servlet.DispatcherServlet.doService(DispatcherServlet.java:974) ~[spring-webmvc-6.0.6.jar:6.0.6]
	at org.springframework.web.servlet.FrameworkServlet.processRequest(FrameworkServlet.java:1011) ~[spring-webmvc-6.0.6.jar:6.0.6]
	at org.springframework.web.servlet.FrameworkServlet.doPost(FrameworkServlet.java:914) ~[spring-webmvc-6.0.6.jar:6.0.6]
	at jakarta.servlet.http.HttpServlet.service(HttpServlet.java:731) ~[tomcat-embed-core-10.1.5.jar:6.0]
	at org.springframework.web.servlet.FrameworkServlet.service(FrameworkServlet.java:885) ~[spring-webmvc-6.0.6.jar:6.0.6]
	at jakarta.servlet.http.HttpServlet.service(HttpServlet.java:814) ~[tomcat-embed-core-10.1.5.jar:6.0]
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:223) ~[tomcat-embed-core-10.1.5.jar:10.1.5]
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:158) ~[tomcat-embed-core-10.1.5.jar:10.1.5]
	at org.apache.tomcat.websocket.server.WsFilter.doFilter(WsFilter.java:53) ~[tomcat-embed-websocket-10.1.5.jar:10.1.5]
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:185) ~[tomcat-embed-core-10.1.5.jar:10.1.5]
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:158) ~[tomcat-embed-core-10.1.5.jar:10.1.5]
	at com.koinsect.koinsectback.auth.JwtAuthenticationFilter.doFilterInternal(JwtAuthenticationFilter.java:55) ~[main/:na]
	at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:116) ~[spring-web-6.0.6.jar:6.0.6]
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:185) ~[tomcat-embed-core-10.1.5.jar:10.1.5]
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:158) ~[tomcat-embed-core-10.1.5.jar:10.1.5]
```



# 오류 추정

> `StandardMultipartHttpServletRequest.java의 탐색

디버깅을 하며  `request.getParts()`에 오류가 있는 줄 알고 구글링하며 찾아봤다. 하지만  에러메세지가 `Failed to parse multipart servlet request`만 나오는 사람은 없었다.

```java
private void parseRequest(HttpServletRequest request) {
    try {
        Collection<Part> parts = request.getParts();
        this.multipartParameterNames = new LinkedHashSet<>(parts.size());
        MultiValueMap<String, MultipartFile> files = new LinkedMultiValueMap<>(parts.size());
        for (Part part : parts) {
            String headerValue = part.getHeader(HttpHeaders.CONTENT_DISPOSITION);
            ContentDisposition disposition = ContentDisposition.parse(headerValue);
            String filename = disposition.getFilename();
            if (filename != null) {
                files.add(part.getName(), new StandardMultipartFile(part, filename));
            }
            else {
                this.multipartParameterNames.add(part.getName());
            }
        }
        setMultipartFiles(files);
    }
    catch (Throwable ex) {
        handleParseFailure(ex);
    }
}
```



# 해결 방법

정말 어이가 없었던 문제...

postman 으로 요청할 때 내 컴퓨터-사진 폴더에 있는 사진으로 요청하는데, 이 폴더는 Microsoft OneDrive 온라인 전용 폴더였다... 하지만 OneDrive는 켜져 있지 않았고, 이 때문에 컴퓨터에서 사진 파일을 불러오지 못했던 문제였다.

OneDrive가 꺼져 있다면 사진 미리보기에서 좌하단에 X표가 보일 테니 이걸 보면 OneDrive에 연결이 안되어있구나! 하고 떠올리면 되겠다.

정말 어처구니가 없어서 화도 안나는 문제였다.



# 출처 및 참고자료

내 머릿속
