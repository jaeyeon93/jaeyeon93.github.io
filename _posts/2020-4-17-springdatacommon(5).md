---
layout: post
title: 2020-4-17 스프링 Data common (5) JPA Fetch
comments: true
---
### Fetch

- 연관관계의 데이터를 어떻게 가져올거냐? 미리(eager), 나중에(lazy)
- @OneToMany의 기본값은 Lazy => 이게 합리적. 컬렉션에 몇개가 있는지 모르는데, 즉시 로딩을 하면 객체에 불필요한 값들을 로딩할 수 있다.
- @ManyToOne의 기본값은 Eager => ManyToOne은 어차피 데이터가 1개여서 Eager로 로딩



#### OneToMany에 Eager

```sql
/*Fetch type EAGER*/
Hibernate:
    select
        post0_.id as id1_3_0_,
        post0_.title as title2_3_0_,
        comments1_.post_id as post_id3_2_1_,
        comments1_.id as id1_2_1_,
        comments1_.id as id1_2_2_,
        comments1_.comment as comment2_2_2_,
        comments1_.post_id as post_id3_2_2_
    from
        post post0_
    left outer join
        comment comments1_
            on post0_.id=comments1_.post_id
    where
        post0_.id=?
/*Fetch type Lazy(default) contain Commnets*/

Hibernate:
    select
        post0_.id as id1_3_0_,
        post0_.title as title2_3_0_
    from
        post post0_
    where
        post0_.id=?
===== 두번째 select가 시작되는 부분
Hibernate:
    select
        comments0_.post_id as post_id3_2_0_,
        comments0_.id as id1_2_0_,
        comments0_.id as id1_2_1_,
        comments0_.comment as comment2_2_1_,
        comments0_.post_id as post_id3_2_1_
    from
        comment comments0_
    where
        comments0_.post_id=?
/*Fetch type Lazy(default) get Title*/
Hibernate:
    select
        post0_.id as id1_3_0_,
        post0_.title as title2_3_0_
    from
        post post0_
    where
        post0_.id=?
```

- @OneToMany에서 FetchType을 각각 EAGER, LAZY로 했다.
- EAGER를 했을때는, comments를 가져올때나 가져오지 않을때나 둘 다 left join을 해서 데이터를 가져온다.
- 반면 Lazy를 할때는 comments를 안가져오면 select를 한번만 날리고, comments를 포함한 경우 select를 두번 날린다.
- Fetch를 잘해야 성능상에 이슈를 해결한다. N+1역시도 해결할 수 있다.
