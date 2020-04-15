---
layout: post
title: 2020-4-15 스프링 Data common (4) JPA Cascade
comments: true
---

### Cascade

- cascade : 엔티티의 상태 변화를 전파시키는것이다. 연관이 되어있는 엔티티에도 상태변화를 똑같이 전이 시키고 싶다.

![](https://jstobigdata.com/wp-content/uploads/2019/08/Entity-instance-states-1024x536.png)



엔티티의 상태

- Transient : JPA가 모르는 상태
- Persistent : JPA가 관리중인 상태(1차 캐시, Dirty Checking, Write Behind...)
- Detached : JPA가 더이상 관리하지 않은 상태
- Removed: JPA가 관리하긴 하지만 삭제하기로 한 상태

```java
public void run(ApplicationArguments args) throws Exception {
        Account account = new Account();
        account.setUsername("jimmy");
        account.setPassword("hibernate");

        Study study = new Study();
        study.setName("spring data study");
        account.addStudy(study);
        Session session = entityManager.unwrap(Session.class);
        session.save(account);
        session.save(study);

        System.out.println("=======================");
        Account noSelect = session.load(Account.class, account.getId());
        noSelect.setUsername("whiteship");
        noSelect.setUsername("whiteship1");
        noSelect.setUsername("whiteship2");
        noSelect.setUsername("whiteship3");
        noSelect.setUsername("update");
        System.out.println("no select " + noSelect.getUsername());
    }
```

- 위의 코드들은 Transient상태.
- save를 하게 되면 persistent상태가 된다. 이때 바로 DB에 데이터가 들어가는것은 아니다. JPA가 상태를 보고 추후 DB에 저장한다.
- persistent에서는 1차캐시의 역할을 하기때문에, 밑에서 select를 날리지도 않고 바로 가져온다.
- Dirtycheck: 객체의 변경사항을 비교한다.
- write behind : 트랜잭션이 끝나면 객체의 변경사항을 쿼리로 날리게 된다.
- 위의 코드에서 setUsername이 엄청나게 많은데, 결과론적으로는 update쿼리가 1번만 나간다. 이 역시도 setUseranme이 초창기 값인 jimmy라면 update쿼리가 날라가지 않는다. 이게 캐싱기능이 있기때문에 가능하다.
- 트랜잭션이 끝나고 세션밖으로 나오면 Detached상태가 된다.
- detached상태에서는 dirty checking, write behind, lazy-loading등이 발생하지도 않는다. 다시 persistent로 만들기 위해 repersitent를 해야한다.
- cascade의 경우 oneToMany, ManyToOne의 관계가 아닌 Parent, Child관계에서 적용할 수 있다.
- session.load() : 프록시로도 가져올 수 있다.
- session.get() : 무조건 DB에서 가져오고 해당하는게 없으면 null로 만든다.
