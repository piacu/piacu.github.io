---
  
layout: post
title: "[Spring] 스프링 컨트롤러"
tags: [study, spring, web, java]

---

# Intro

Controller란 직역하면 제어장치이다. 이는 프론트엔드(사용자에게 보여지는 부분)에서 받은 요청을 어떻게 처리할 것인가?를 Controller에서 한다고 보면 된다.

Controller는 Front Controller와 Controller로 나누어진다.



# Front Controller? 그게 뭔데?

### Front Controller

- 디자인 패턴 중 하나로, 애플리케이션의 모든 요청을 처리하는 중앙 집중형 컨트롤러이다.
- 모든 클라이언트 요청이 Front Controller로 라우팅되고, 이후에 해당 요청을 처리하기 위해 다양한 컴포넌트들을 호출하거나 조정한다.



이와 비슷한 개념으로 Spring으로 사용되는 것이 있다. 바로 DispatcherServlet이다.

### DispatcherServlet

* Spring framework에서 사용되는 컴포넌트로, 웹 애플리케이션의 HTTP 요청을 받고 HandlerMapping을 통해 적절한 컨트롤러로 라우팅하는 역할
* Spring MVC 애플리케이션에서 클라이언트의 요청을 받아서 처리할 컨트롤러를 찾아주고, 그 결과를 사용자에게 반환



***Front Controller(FC)는 Design Pattern의 개념, DispatcherServlet은 FC가 Spring에서 사용되는 구현체라고 할 수 있겠다.***

아래의 그림에서 더욱 자세히 볼 수 있다. 그림은 컨트롤러단 까지의 작동만 설명하고 있고, Controller 이전의 Filter가 배제되어 있다.

<img src="https://i.imgur.com/blr7x6q.png" alt="img" style="zoom:67%;" />

간단하게 말하자면 클라이언트(프론트)와 Service-Repository(DB) 사이를 연결하는 통로이다.

그럼 왜 사용할까?



# Controller의 필요성 - HTTP req, resp 생략가능

다양한 정보를 찾아보고, 생각해 봤을 때 HTTP request, response 중복 코드 생략 가능하다는 점이 가장 큰 것 같다.

HTTP 패킷을 받는 것은 Servlet을 받는 것이다. 이를 사용하려고 하면 역직렬화를 해야 하며 코드가 길어지게 된다. api는 한 두개가 아닐 것이고, 그 중 GET메소드 뿐만 아니라 POST,DELETE 등도 사용할 것이니 엄청난 중복 코드를 통해 역직렬화 및 직렬화를 해야 할 것이다.

```java
@WebServlet(urlPatterns="/api/user")
public class UserServlet extends HttpServlet {
	@Override
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws Exception {
		String id = request.getParameter("id");
		String name = request.getParameter("name");
		String phoneNum = request.getParameter("phoneNum");
		...
        getUserInfo(id, phoneNum);
        response.setContentType("application/json");
        response.setCharacterEncoding("UTF-8");
        ...
	}
}
```

이를 단순 @Controller 어노테이션을 붙이기만 한다면 아래처럼 줄일 수 있다.

```java
@Controller
public class UserController {
	@GetMapping
	public ResponseEntity<?> getUser(String id, String name, String phoneNum) {
        return ResponseEntity.ok(getUserInfo(id, phoneNum));
    }
}
```



## 출처 및 참고자료

https://a1010100z.tistory.com/106

https://doublesprogramming.tistory.com/105
