---
  
title: "[AWS] Spring HikariPool-1 - Thread starvation or clock leap detected 이슈"
author: piacu
date: 2023-04-21 19:00:00 +0900
categories: [Trouble Shooting, AWS]
tags: [trouble shooting, aws, ec2, hikaripool]

---

# 증상

```
2023-04-24T05:56:00.913Z ERROR 1 --- [io-8080-exec-10] c.k.k.controller.ExceptionController     : Controller ERROR > org.springframework.web.HttpRequestMethodNotSupportedException

org.springframework.web.HttpRequestMethodNotSupportedException: Request method 'GET' is not supported
        at org.springframework.web.servlet.mvc.method.RequestMappingInfoHandlerMapping.handleNoMatch(RequestMappingInfoHandlerMapping.java:265) ~[spring-webmvc-6.0.6.jar!/:6.0.6]
        at org.springframework.web.servlet.handler.AbstractHandlerMethodMapping.lookupHandlerMethod(AbstractHandlerMethodMapping.java:441) ~[spring-webmvc-6.0.6.jar!/:6.0.6]
        at org.springframework.web.servlet.handler.AbstractHandlerMethodMapping.getHandlerInternal(AbstractHandlerMethodMapping.java:382) ~[spring-webmvc-6.0.6.jar!/:6.0.6]
        at org.springframework.web.servlet.mvc.method.RequestMappingInfoHandlerMapping.getHandlerInternal(RequestMappingInfoHandlerMapping.java:126) ~[spring-webmvc-6.0.6.jar!/:6.0.6]
        at org.springframework.web.servlet.mvc.method.RequestMappingInfoHandlerMapping.getHandlerInternal(RequestMappingInfoHandlerMapping.java:68) ~[spring-webmvc-6.0.6.jar!/:6.0.6]
        at org.springframework.web.servlet.handler.AbstractHandlerMapping.getHandler(AbstractHandlerMapping.java:505) ~[spring-webmvc-6.0.6.jar!/:6.0.6]
        at org.springframework.web.servlet.DispatcherServlet.getHandler(DispatcherServlet.java:1275) ~[spring-webmvc-6.0.6.jar!/:6.0.6]
        at org.springframework.web.servlet.DispatcherServlet.doDispatch(DispatcherServlet.java:1057) ~[spring-webmvc-6.0.6.jar!/:6.0.6]
        at org.springframework.web.servlet.DispatcherServlet.doService(DispatcherServlet.java:974) ~[spring-webmvc-6.0.6.jar!/:6.0.6]
        at org.springframework.web.servlet.FrameworkServlet.processRequest(FrameworkServlet.java:1011) ~[spring-webmvc-6.0.6.jar!/:6.0.6]
        at org.springframework.web.servlet.FrameworkServlet.doGet(FrameworkServlet.java:903) ~[spring-webmvc-6.0.6.jar!/:6.0.6]
        at jakarta.servlet.http.HttpServlet.service(HttpServlet.java:705) ~[tomcat-embed-core-10.1.5.jar!/:na]
        at org.springframework.web.servlet.FrameworkServlet.service(FrameworkServlet.java:885) ~[spring-webmvc-6.0.6.jar!/:6.0.6]
        at jakarta.servlet.http.HttpServlet.service(HttpServlet.java:814) ~[tomcat-embed-core-10.1.5.jar!/:na]
        at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:223) ~[tomcat-embed-core-10.1.5.jar!/:na]
        at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:158) ~[tomcat-embed-core-10.1.5.jar!/:na]
        at org.apache.tomcat.websocket.server.WsFilter.doFilter(WsFilter.java:53) ~[tomcat-embed-websocket-10.1.5.jar!/:na]
        at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:185) ~[tomcat-embed-core-10.1.5.jar!/:na]
        at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:158) ~[tomcat-embed-core-10.1.5.jar!/:na]
        at org.springframework.web.filter.RequestContextFilter.doFilterInternal(RequestContextFilter.java:100) ~[spring-web-6.0.6.jar!/:6.0.6]
        at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:116) ~[spring-web-6.0.6.jar!/:6.0.6]
        at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:185) ~[tomcat-embed-core-10.1.5.jar!/:na]
        at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:158) ~[tomcat-embed-core-10.1.5.jar!/:na]
        at org.springframework.web.filter.FormContentFilter.doFilterInternal(FormContentFilter.java:93) ~[spring-web-6.0.6.jar!/:6.0.6]
        at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:116) ~[spring-web-6.0.6.jar!/:6.0.6]
        at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:185) ~[tomcat-embed-core-10.1.5.jar!/:na]
        at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:158) ~[tomcat-embed-core-10.1.5.jar!/:na]
        at org.springframework.web.filter.CharacterEncodingFilter.doFilterInternal(CharacterEncodingFilter.java:201) ~[spring-web-6.0.6.jar!/:6.0.6]
        at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:116) ~[spring-web-6.0.6.jar!/:6.0.6]
        at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:185) ~[tomcat-embed-core-10.1.5.jar!/:na]
        at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:158) ~[tomcat-embed-core-10.1.5.jar!/:na]
        at org.apache.catalina.core.StandardWrapperValve.invoke(StandardWrapperValve.java:177) ~[tomcat-embed-core-10.1.5.jar!/:na]
        at org.apache.catalina.core.StandardContextValve.invoke(StandardContextValve.java:97) ~[tomcat-embed-core-10.1.5.jar!/:na]
        at org.apache.catalina.authenticator.AuthenticatorBase.invoke(AuthenticatorBase.java:542) ~[tomcat-embed-core-10.1.5.jar!/:na]
        at org.apache.catalina.core.StandardHostValve.invoke(StandardHostValve.java:119) ~[tomcat-embed-core-10.1.5.jar!/:na]
        at org.apache.catalina.valves.ErrorReportValve.invoke(ErrorReportValve.java:92) ~[tomcat-embed-core-10.1.5.jar!/:na]
        at org.apache.catalina.core.StandardEngineValve.invoke(StandardEngineValve.java:78) ~[tomcat-embed-core-10.1.5.jar!/:na]
        at org.apache.catalina.connector.CoyoteAdapter.service(CoyoteAdapter.java:357) ~[tomcat-embed-core-10.1.5.jar!/:na]
        at org.apache.coyote.http11.Http11Processor.service(Http11Processor.java:400) ~[tomcat-embed-core-10.1.5.jar!/:na]
        at org.apache.coyote.AbstractProcessorLight.process(AbstractProcessorLight.java:65) ~[tomcat-embed-core-10.1.5.jar!/:na]
        at org.apache.coyote.AbstractProtocol$ConnectionHandler.process(AbstractProtocol.java:859) ~[tomcat-embed-core-10.1.5.jar!/:na]
        at org.apache.tomcat.util.net.NioEndpoint$SocketProcessor.doRun(NioEndpoint.java:1734) ~[tomcat-embed-core-10.1.5.jar!/:na]
        at org.apache.tomcat.util.net.SocketProcessorBase.run(SocketProcessorBase.java:52) ~[tomcat-embed-core-10.1.5.jar!/:na]
        at org.apache.tomcat.util.threads.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1191) ~[tomcat-embed-core-10.1.5.jar!/:na]
        at org.apache.tomcat.util.threads.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:659) ~[tomcat-embed-core-10.1.5.jar!/:na]
        at org.apache.tomcat.util.threads.TaskThread$WrappingRunnable.run(TaskThread.java:61) ~[tomcat-embed-core-10.1.5.jar!/:na]
        at java.base/java.lang.Thread.run(Thread.java:833) ~[na:na]

2023-04-24T06:43:53.881Z  WARN 1 --- [l-1 housekeeper] com.zaxxer.hikari.pool.HikariPool        : HikariPool-1 - Thread starvation or clock leap detected (housekeeper delta=46s400ms933µs342ns).
2023-04-24T06:50:29.683Z  WARN 1 --- [l-1 housekeeper] com.zaxxer.hikari.pool.HikariPool        : HikariPool-1 - Thread starvation or clock leap detected (housekeeper delta=9m28s941ms676µs786ns).
2023-04-24T07:18:51.204Z  WARN 1 --- [l-1 housekeeper] com.zaxxer.hikari.pool.HikariPool        : HikariPool-1 - Thread starvation or clock leap detected (housekeeper delta=28m24s672ms951µs819ns).
2023-04-24T07:19:53.190Z  WARN 1 --- [l-1 housekeeper] com.zaxxer.hikari.pool.HikariPool        : HikariPool-1 - Thread starvation or clock leap detected (housekeeper delta=1m3s598ms678µs960ns).
2023-04-24T07:23:08.882Z  INFO 1 --- [nio-8080-exec-2] o.apache.coyote.http11.Http11Processor   : Error parsing HTTP request header
 Note: further occurrences of HTTP request parsing errors will be logged at DEBUG level.

java.lang.IllegalArgumentException: Invalid character found in method name [MGLNDD_15.164.251.76_80800x0a...]. HTTP method names must be tokens
        at org.apache.coyote.http11.Http11InputBuffer.parseRequestLine(Http11InputBuffer.java:419) ~[tomcat-embed-core-10.1.5.jar!/:na]
        at org.apache.coyote.http11.Http11Processor.service(Http11Processor.java:272) ~[tomcat-embed-core-10.1.5.jar!/:na]
        at org.apache.coyote.AbstractProcessorLight.process(AbstractProcessorLight.java:65) ~[tomcat-embed-core-10.1.5.jar!/:na]
        at org.apache.coyote.AbstractProtocol$ConnectionHandler.process(AbstractProtocol.java:859) ~[tomcat-embed-core-10.1.5.jar!/:na]
        at org.apache.tomcat.util.net.NioEndpoint$SocketProcessor.doRun(NioEndpoint.java:1734) ~[tomcat-embed-core-10.1.5.jar!/:na]
        at org.apache.tomcat.util.net.SocketProcessorBase.run(SocketProcessorBase.java:52) ~[tomcat-embed-core-10.1.5.jar!/:na]
        at org.apache.tomcat.util.threads.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1191) ~[tomcat-embed-core-10.1.5.jar!/:na]
        at org.apache.tomcat.util.threads.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:659) ~[tomcat-embed-core-10.1.5.jar!/:na]
        at org.apache.tomcat.util.threads.TaskThread$WrappingRunnable.run(TaskThread.java:61) ~[tomcat-embed-core-10.1.5.jar!/:na]
        at java.base/java.lang.Thread.run(Thread.java:833) ~[na:na]
```

HikariPool-1 - Thread starvation or clock leap detected 이 EC2 Spring 서버 로그에서 지속적으로 발생하며 도커로 올린 스프링 컨테이너가 내려가는 현상이 발생하고, EC2 서버 자체가 다운되는(멈춰버리는) 현상도 가끔 생겼다.



# 오류 추정

1. Spring Boot와 MySQL 연결(hikaripool) 오류
2. Spring Boot의 원인미상 오류

두 가지 오류 발생 원인을 생각하며 구글링을 하였고 아래 방법들을 시도해보았다 :

Connection Pool 부족으로 발생할 수 있다고 하여 maxium pool size를 10으로 `application.yml`에 설정해주었다. -- **실패**

VM으로 가동시에 문제가 생길수도 있다고 한다. or 절전모드로 쓰레드가 돌고 있다. -- 클라우드 서버라 해당 안 됨. **실패**



# 해결 방법

원인은 생각지도 못한 곳에 있었다. 지인에게 이런 오류가 있는데 해결 못하고 있다며 도움을 요청하니 EC2 프리티어의 기본 메모리가 1GB여서 늘려주면 해결될 수도 있다고 하였다. 그래서 아래와 같은 방법으로 시도하였다.

1. memory swap을 위해 기존 8GiB이던 서버 스토리지를 30GiB로 확장했다.(프리티어 무료로 가능한 최대 스토리지)

2. 가상메모리를 2GB 할당하여 memory swap이 가능하도록 했다.

   

결과적으로 해결되었다. 컨테이너 두 개(springboot, mysql)가 swap 메모리까지 포함된 용량에서 거진 50%를 잡아먹고 있었던 것이고, 스왑 메모리를 설정하였더니 서버가 뻗지 않고 정상 작동한다!!!

스왑 메모리가 하드디스크 용량을 메모리로 할당하는 거라 느리다고 알고있었는데 생각보다 답답할 정도는 아니었다. ㅎㅎ



# 출처 및 참고자료

https://velog.io/@kmw10693/EC2-프리티어-용량-늘리기

https://kth990303.tistory.com/361
