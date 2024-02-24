---

layout: post
title: "[Blog] Github Page 개설 및 후기"
tags: [blog, jekyll, ruby]

---

안녕하세요. Github Page 를 사용한 블로그를 개설하였습니다.

여기에는 여러 가지 개발 일지나 해결한 문제 등을 적는 기술블로그로 사용하고자 합니다.

첫번째 게시물은 개설을 자축하며 이 블로그를 만들기까지 헤매었던 것들을 적어보려 합니다.

🎉 개설을 축하합니다~  🎉

(만들어놓고 방치하는 게으름뱅이가 되지 않기를...  😀)



## Github Page를 만들기까지의 과정

나만의 블로그를 만들어서 운영하고 싶었는데 피차 미루다가 오늘은 무조건 마무리해야겠다고 마음먹고 시작!!

개발자들이 많이 사용한다는 Tistory나 Velog 와 고민하다가 github page의 장점인 자유로운 커스터마이징의 매력을 보고 깃허브 페이지를 선택했습니다.

하지만 자유롭다는 것은 그만큼 큰 책임(일)이 따른다는 진리는 역시 틀린 말 하나 없는 것 같군요.



jekyll를 사용한 정적 페이지를 만들기로 결정하고 Ruby, jekyll 를 설치하고 테마는 동료분께 추천받은 [chirpy](https://github.com/cotes2020/jekyll-theme-chirpy)를 사용했습니다.

chirpy 를 fork 해서 저장소로 가져온 뒤 clone 해서 local 에서 작업하였습니다.



![image-20220628041346620](https://user-images.githubusercontent.com/26267376/176025174-ca24be46-e656-4ced-8e36-7280ebc80cd8.png)

clone 한 다음 chirpy 공식문서의 Option 2를 따라하면 쉽게 됩니다.

(실수로 처음에 chirpy starter를 fork해서 작업했더니 tools 폴더에 아무것도 없어서 여러번 지웠다가 만들었습니다...)



위 명령어를 실행 후에 _config.yml 파일을 내 정보에 맞게 열심히 세팅해주고 모든 세팅이 끝난 후 origin 으로 push 를 하면 완료됩니다:exclamation: 였으면 좋겠지만 개발자의 숙명은 역시 버그잡기 아니겠습니까.



## 많은 시간을 소비하게 만든 인증 오류

![image-20220628035815133](https://user-images.githubusercontent.com/26267376/176025107-383ac77e-f3d9-4e07-99f7-8707fd80d18b.png)

>  <span style="color:red">! [remote rejected]</span> master -> master (refusing to allow an OAuth App to create or update workflow `.github/workflows/pages-deploy.yml` without `workflow` scope)   
>
> <span style="color:red">error: failed to push some refs to 'https://github.com/piacu/piacu.github.io.git'</span>



해석하면 `OAuth 앱`이 `workflow` 범위 없이 `~~/pages-deploy.yml` 워크플로를 생성하거나 업데이트 걸 거부한다. 라는데...

구글링의 결과는 다 깃허브의 `Personal access tokens`를 수정해보라고 하지만 에러 메세지는 달라지지 않았습니다.



그러다가 깃허브 커뮤에서 찾은 단비같은 개발자의 답변... 

https://github.community/t/refusing-to-allow-an-integration-to-create-or-update/16326

![112](https://user-images.githubusercontent.com/26267376/176024881-15adee86-b01e-4731-a76f-4fab6493ccbd.PNG)

Github Desktop과 같은 도구를 업데이트하면 애플리케이션 토큰을 요청할 수 있다!!!

바로 깃허브 데스크탑 설치~~ 결과는 푸시 성공!!



정말 몇시간을 삽질했는 지 모르겠지만 성공했으니 기분이 그만큼 더 좋군요 🤩

저같이 삽질하는 사람에게 이 링크를 보내줘야 겠습니다 ~

포스팅 끝!