---
layout: post
title: 2020-4-23 스프링 Data common (7) Query
comments: true
---

### 쿼리

JPQL(HQL)

- Java Persistence Query Language(Hibernate Query Language)
- 데이터베이스 테이블이 아닌 엔티티 객체 모델 기반으로 쿼리 작성
- JPA 또는 하이버네이트가 해당 쿼리를 SQL로 변환해서 실행한다.



```java
TypedQuery<Post> query = entityManager.createQuery("SELECT p FROM post as p", Post.class);
        List<Post> posts = query.getResultList();
        posts.forEach(System.out::println);
```

- 위의 방법은 TypeSafe하지 않다.

#### Criteria

- 타입세이프쿼리

```java
@Override
    public void run(ApplicationArguments args) throws Exception {
        CriteriaBuilder builder = entityManager.getCriteriaBuilder();
        CriteriaQuery<Post> query = builder.createQuery(Post.class);
        Root<Post> root = query.from(Post.class);
        query.select(root);

        List<Post> posts = entityManager.createQuery(query).getResultList();
        posts.forEach(System.out::println);
    }
```



#### Native Query

- SQL 쿼리 실행하기

```java
@Override
    public void run(ApplicationArguments args) throws Exception {
        List<Post> posts = entityManager.createNativeQuery("select * from post", Post.class).getResultList();
        posts.forEach(System.out::println);
    }
```
