---

layout: post
title: "[JPA] 영속성 컨텍스트(Persistence Context)"
tags: [study, cs, java]

---

## 영속성 컨텍스트란?

**엔티티를 영구 저장하는 환경**을 뜻한다. 쉽게 생각하면 영속화(DB에 저장)하기 전에 잠시 맡아두고 있는 친구라고 할 수 있겠다.

>  EntityManager.persist(); // 영속성 컨텍스트에 저장



영속성 컨텍스트는 Java 어플리케이션과 DB 사이에 존재하는 환경으로 **엔티티 객체의 생명주기를 관리**한다. persist()를 통해 엔티티는 **1차 캐시**에 저장되게 되고, 트랜잭션이 끝나기 전까지는 영속성 컨텍스트 내부의  정보를 유지한다. 트랜잭션이 커밋되면 영속성 컨텍스트가 flush되고 변경 사항이 데이터베이스에 반영된다.



#### ☝ `1차 캐시`란?

- 영속성 컨텍스트 내부에는 엔티티 인스턴스를 캐싱하는 **1차 캐시**가 있다.
- 일반적으로 **트랜잭션 내에서 유효한 생명주기**를 갖는다.
- 같은 트랜잭션 내에서 1차 캐시에 존재하는 엔티티를 조회할 때 데이터베이스에 접근 하지 않아도 된다.
- **트랜잭션이 커밋되면 소멸**한다.

#### ✌`1차 캐시` 덕분에 `동일성 보장`도 된다!!

- 앞서 살펴본 1차 캐시에서는 엔티티 인스턴스를 `Map`의 구조로 캐싱한다.
- 따라서 **같은 식별자**를 통해 1차캐시에서 얻은 엔티티 인스턴스는 `==`연산을 통해 동일성을 보장한다.



이 **1차 캐시** 덕분에 아래와 같은 코드를 사용하더라도 쿼리가 발생하지 않는다.

```java
// EntityManagerFactory emf가 선언되고 적절한 값이 할당됐다고 가정
EntityManager em = emf.createEntityManager();
EntityTransaction tx = em.getTransaction();

tx.begin(); // 트랜잭션 시작
Member member = new Member(1L, "Kim"); //  아이디(식별자):1L 이름:Kim

em.persist(member); // 영속화

Member findMember = em.find(Member.class, 1L); // 아이디가 1L 유저는 영속성 컨택스트에 있기 때문에 select query가 나가지 않음
```

하지만, 영속성 컨텍스트 내에 없는 엔티티를 불러오려 하면 query가 발생하게 된다.

```java
Member findMember2 = em.find(Member.class, 2L); // 영속성 컨택스트에 없는 아이디이기 때문에 select query가 나감
```

![jpa_persistence_context](https://github.com/algogorithm/algorithm/assets/26267376/22354a04-f241-4bb1-a330-ddb31aa398bd)

참고로 **쓰기 지연 SQL 저장소**는 엔티티들이 DB에 영속화되기 위해 필요한 SQL query들을 저장해놓고, `commit` 혹은 `flush`될 때 시점에 데이터베이스로 요청한다.

![a](https://github.com/algogorithm/algorithm/assets/26267376/e3c325f4-3f9d-448e-8115-95ada8e909b8)

#### 💡 `commit`과 `flush`의 차이

* flush는 영속성 컨텍스트의 변경 내용을 DB에 동기화하는 과정을 말한다.
* commit은 flush + 트랜잭션의 마침을 말한다.

> flush만 날리면 트랜잭션이 끝나지 않아 ACID 특성을 준수하지 않게 되어 DB에 저장되지 않는다.



## 엔티티 객체의 생명주기

엔티티 객체의 생명주기는 **비영속**, **영속**, **준영속**, **삭제** 4가지의 상태로 표현할 수 있다.

![lifecycle](https://github.com/algogorithm/algorithm/assets/26267376/bfbe288a-5146-4edd-b329-b1aeb0cfbd11)



- New / Transient
  - 비영속 상태
  - 엔티티가 생성은 되었지만 영속성 컨텍스트에 저장되지 않은 상태

```java
Member member = new Member(1L, "Jeon"); //member는 비영속 상태
```

- Managed
  - 영속 상태
  - 엔티티 매니저로 영속성 컨텍스트에 저장된 상태
  - DB에 반영되지 않고 영속성 컨텍스트에 의해 관리됨

```java
Member member = new Member(1L, "Jeon"); //member는 비영속 상태
em.persist(member); //member는 영속 상태가 됨
```

- Detached
  - 준영속 상태
  - 영속성 컨텍스트에 의해 관리되다가 분리된 상태

```java
Member member = new Member(1L, "Jeon"); //member는 비영속 상태
em.persist(member); //member는 영속 상태가 됨

em.detach(member); // member엔티티만 준영속 상태로 전환
```

- Removed
  - 삭제된 상태
  - 영속성 컨텍스트와 DB에서 제거된 상태

```
em.remove(member);
```



## 비영속과 준영속

비영속과 준영속에 대해 알기 전에 식별자 값에 대해 먼저 알아야 한다.



#### 💡 `식별자 값`

- 영속성 컨텍스트는 `식별자값(PK)`으로 엔티티를 구분한다.
- 모든 영속상태의 엔티티는 식별자 값을 가지고 있어야한다. **없다면 예외**를 뱉어낸다.



영속 상태의 모든 엔티티는 식별자 값을 가지고 있다.

준영속 상태는 영속 상태에서 분리되어 영속성 컨텍스트가 관리하지 않는 상태이다.

= **준영속 상태는 식별자 값을 가지고 있는다.**



준영속 상태를 영속 상태로 바꾸려면 두 가지 전략이 있다.

### **변경 감지 기능**

DB에서 다시 조회해서 영속화 한다.

```java
@Transactional
void update(Member member) { //itemParam: 파리미터로 넘어온 준영속 상태의 엔티티
	Member findMember = em.find(Member.class, member.getId()); //같은 엔티티를 조회한다.
    findMember.setId(member.getId()); //데이터를 수정한다.
}
```



### 병합 사용

병합은 준영속 상태의 엔티티를 영속 상태로 변경할 때 사용하는 기능이다.

```java
@Transactional
void update(Member member) { //itemParam: 파리미터로 넘어온 준영속 상태의 엔티티
	Member findMember = em.merge(member);
}
```





## 영속성 컨텍스트 내 값 수정

영속성 컨텍스트 내 값을 수정하게 되면 **변경 감지(더티체크)**를 하게 된다.

* 엔티티 인스턴스를 영속성 컨텍스트에 보관 할 때, 최초 상태에 대한 **스냅샷**을 저장해둔다. 

- 커밋(또는 플러시) 시점에 각각의 엔티티의 상태를 **스냅샷**과 비교하는데 변경된 엔티티가 있으면 쓰기 지연 SQL 버퍼에 `update` 쿼리를 추가한다.

![jpa_persistence_context_06](https://github.com/algogorithm/algorithm/assets/26267376/59c5499a-3753-4ff9-9bf0-52f81f7eec46)



## 정리

영속성 컨텍스트는 데이터베이스의 일관성을 유지하고 성능 최적화를 위해 존재한다.



## Reference

https://github.com/devSquad-study/2023-CS-Study/blob/main/JPA/jpa_persistence_context.md

https://sjiwon-dev.tistory.com/14

https://ttl-blog.tistory.com/109#%EC%A4%80%EC%98%81%EC%86%8D%EA%B3%BC%20%EB%B9%84%EC%98%81%EC%86%8D%EC%9D%98%20%EC%B0%A8%EC%9D%B4%EC%A0%90-1

https://ppomelo.tistory.com/147