---
  
layout: post
title: "[Spring] contextLoads () FAILED 오류"
tags: [trouble shooting, spring, build, gradle]

---

## 증상

![image](https://github.com/piacu/piacu.github.io/assets/26267376/e7a25657-9721-4387-8a50-174c295004f7)`./gradlew build` 명령어를 실행하여 스프링 프로젝트를 빌드하려고 했는데 위와 같은 오류가 발생하였다.



## 오류 추정

```
KoinsectBackApplicationTests > contextLoads () FAILED
```

로그를 잘 살펴보니 ApplicationTest에서 문제가 있다고 추정할 수 있었다.



## 해결 방법

@SpringBootTest 어노테이션 주석처리 후 다시 `./gradlew build` 실행했더니 고쳐짐!



## 참고자료

https://velog.io/@gnoesnooj/contextLoads-failed-%EC%98%A4%EB%A5%98
