---
  
title: "[Jenkins] Spring 프로젝트 Gradle 빌드 이슈"
author: piacu
date: 2023-05-04 21:00:00 +0900
categories: [Trouble Shooting, Jenkins]
tags: [trouble shooting, spring, jenkins, gradle]

---

# 증상

![0504](.\assets\0504.png)gradle plugin 버전이 맞지 않다는 오류 발생

→ 스프링부트3을 사용하고 있는데 이는 자바 17버전 이상을 사용해야 한다. 하지만 자바 17과 gradle 3.0.4버전과는 맞지 않다는 오류 발생

# 오류 추정

Jenkins에서 gradle build 설정이 있길래 gradle 3.0.4(Spring 프로젝트와 똑같은 버전)로 설정하고 실행했는데 문제가 생겨 의아했다.

나는 Spring Boot 3 그리고 Java 17버전을 사용하고 있기 때문이다!!!

정말 이해할 수 없어 여러 번의 삽질을 했다...



1. gradle 버전을 올리기 위해 jenkins의 환경설정 - Tools 에 진입해 gradle version을 7.6.1로 추가

   → 같은 결과. **실패**

2. JDK 버전을 올려보자

   Jenkins의 환경설정 - Tools의 JDK installations에 JDK 추가(JAVA_HOME 설정을 위해 우분투에 jdk17 설치)

   참고 사이트: [Jenkins 자바 버전 오류](https://velog.io/@qf9ar8nv/jenkins-설정), [JAVA_HOME 설정](https://blog.naver.com/seek316/222034123157)

   → 같은 결과. **실패**

3. [ERROR: JAVA_HOME is set to an invalid directory](https://stackoverflow.com/questions/73159428/error-java-home-is-set-to-an-invalid-directory-please-set-the-java-home-variab)를 보고 똑같이 설정 후 jenkins 의 JDK 설정도 완료

![0504-2](.\assets\0504-2.png)

​	→ 같은 결과. **실패**

4. 캐시 삭제

​	참고 사이트: [Jenkins + Gradle build시 오류 발생 해결](https://blog.naver.com/slimcdp/221131858173)

​	→ 같은 결과. **실패**



# 해결 방법

미친듯이 찾다가 얻은 답은 Jenkins의 도커 이미지를 변경하는 것이었다.

Jenkins의 디폴트 버전이 Java8 이라 17버전을 지원하는 도커 이미지인 jenkins/jenkins-jdk17를 도커허브에서 찾아 설치했더니 바로 됐다...

참고 사이트: [젠킨스 jdk 버전 11로 올리는 방법](https://www.blog.ecsimsw.com/entry/젠킨스-jdk-버전-11로-올리는-방법)



드디어... **해결!!!** 오예.
