---
  
layout: post
title: "[Spring] MySQL Access denied for user 이슈"
tags: [trouble shooting, spring, mysql, gradle]

---

# 증상

![image](https://github.com/piacu/piacu.github.io/assets/26267376/a036d42b-71fe-4b38-bc91-6785eab81e99)

spring boot ↔ mysql 간 연결 문제가 있었다.

사용자가 가진 권한을 통한 데이터베이스 접근이 거부되었다고 나왔다.

# 오류 추정

해당 사용자가 없거나, 사용자의 접근 권한 설정이 잘못된거라고 생각했다.



# 해결 방법

해당 사용자의 접근 권한이 잘못되었다고 생각하여 MySQL에 접근하여 사용자의 권한을 수정하였고 해결했다.

사용자는 있었는데 db를 삭제하고 새로 만들었더니 접근 권한 부여가 안 되어 있었던 것 같다.
