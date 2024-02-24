---
  
layout: post
title: "[NGINX] *1 conect() failed 이슈"
tags: [trouble shooting, nginx, ec2]

---

# 증상

![0508](.\assets\0508.png)2023/05/08 13:12:46 [error] 29#29: *1 connect() failed (111: Connection refused) while connecting to upstream, client: xxx.xxx.xxx.xxx, server: , request: "POST /api/user/login HTTP/1.1", upstream: "http://xxx.xxx.xxx.xxx:8000/user/login", host: "xxx.xxx.xxx.xxx"



# 오류 추정

도저히 무슨 오류인지 모르겠어서 구글링하였다.



# 해결 방법

서버에 접근할 때 요청을 무조건 NGINX가 받도록 했는데, Spring 서버로 보내라는 요청 받은 NGINX가 수신처인 Spring Boot로 라우팅하지 못했고, 그 이유는 도커 네트워크를 구성하지 않아서였다.

하지만 기존 docker-compose를 사용하여 NGINX, Spring Boot 등을 구성하였을 때는 됐었고, 이유를 파악하여보니 아래 이유 때문이었다.

1. docker-compose build를 할 시에 네트워크 설정을 해놓지 않으면 자동으로 **default 네트워크를 생성**해줌

2. 생성된 default 네트워크에 **자동으로 연결**까지 해줘서 컨테이너 간 통신이 가능했음



하지만 Jenkins를 사용하여 자동배포 플로우를 만들었고, 이는 docker-compose를 사용해서 빌드를 하지 않기 때문에(*빌드된 이미지를 docker run 명령어로 직접 빌드하기 때문에*) default 네트워크에 포함되지 않았던 것이 문제였다.



그래서, 새로운 네트워크를 생성하고, 자동배포 시에 docker run 명령어에 --network 옵션을 붙여 같은 네트워크를 통신할 수 있게 만들어주었더니 해결되었다!



# 출처 및 참고자료

https://velog.io/@skanddi/%EB%8F%84%EC%BB%A4-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC
