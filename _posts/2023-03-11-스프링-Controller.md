---
  
title: "[Spring] 스프링 컨트롤러"
author: piacu
date: 2023-03-11 03:22:00 +0900
categories: [Spring, Controller]
tags: [study, spring, web, java]

---

# Intro

스프링 프레임워크의 전통적인 Controller는 HTTP 요청을 받아 파라미터에 검증하고 비즈니스 로직을 수행한 후 결과 데이터를 View에 리턴하는 MVC(Model-View-Controller) 패턴을 적용하고 있습니다.

스프링 MVC 패턴에 대해서는 다음에 소개하며 간단한 사진을 첨부하여 설명을 대체하겠습니다.

<img src="https://i.imgur.com/blr7x6q.png" alt="img" style="zoom:67%;" />

JSP 같은 서버 사이드 스크립트 언어와 함께 사용할 때 Controller에서 model과 함께 view 이름을 리턴하게 되고 ViewResolver는 이를 받아 뷰를 결정하고 사용자에게 보여집니다.

하지만 서버에서 화면 작업을 하는 관계로 좋아요를 하나 누를 때에도 페이지가 새로고침되는 현상이 생기기도 하며 이는 오버헤드를 발생시켜 서버 리소스에 불필요한 낭비가 될 수도 있습니다.



결국 브라우저(클라이언트)에서 실행하는 부분과 서버에서 실행하는 부분으로 나누는 시도가 있어왔고 이는 Vue, React, Angular 같은 SPA(Single Page Application)가 등장하면서 완성되게 됩니다.





# String 반환





# ModelAndView



# ResponseBody



# ResponseEntity



ResponseEntity는 HttpEntity를 상속받으며 HttpHeader와 body를 가질 수 있습니다.

이는

![image-20230311060614617](C:\Users\jszna\AppData\Roaming\Typora\typora-user-images\image-20230311060614617.png)



## 출처 및 참고자료

https://a1010100z.tistory.com/106

https://doublesprogramming.tistory.com/105
