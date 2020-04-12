---
layout: post
title: 2020-4-12 스프링 Data common (3) JPA 단방향, 양방향 매핑
comments: true
---
#### 1대다 맵핑

관계에는 항상 두 엔티티가 존재. 3개의 관계 이런건 전혀없다.

- 하나는 주인이고 하나는 종속된쪽이다.
- 해당 관계의 반대쪽 레퍼런스를 가지고 있는 쪽이 주인.



##### ManyToOne(단방향)

```java
@Entity
public class Study {

    @Id @GeneratedValue
    private Long id;

    private String name;

    @ManyToOne
    private Account owner;
}

create table study (
       id bigint not null,
        name varchar(255),
        owner_id bigint,
        primary key (id)
    ) engine=MyISAM

alter table study
       add constraint FK210g5r7wftvloq2ics531e6e4
       foreign key (owner_id)
       references account (id)


+----+-------------------+----------+
| id | name              | owner_id |
+----+-------------------+----------+
|  2 | spring data study |        1 |
+----+-------------------+----------+

mysql> select * from account;
+----+-----------+----------+
| id | password  | username |
+----+-----------+----------+
|  1 | hibernate | jimmy    |
+----+-----------+----------+  
```



- oneToMany, ManyToOne이 많이 헷갈리는데 현재 레퍼런스를 보면된다. 위에서 Account가 콜렉션이 아니라 한개이면 one이다. 항상 오른쪽의 끝을 봐라.
- 실제 코드를 보면 owner_id Column이라고 생성이 된다. Owner_id에 대한 제약이 foreign_key로 잡힌다.
- 관계에서 주인은 Study가 주인이다.
- 주인 : 관계를 설정했을때, 반영이 되는것



#### ManyToOne(단방향)



```java
@Entity
public class Account {

    @Id @GeneratedValue
    private Long id;
		...

    @OneToMany
    private Set<Study> studies = new HashSet<>();
}

create table account (
       id bigint not null,
        password varchar(255),
        username varchar(255),
        primary key (id)
    ) engine=MyISAM

create table account_studies (
       account_id bigint not null,
        studies_id bigint not null,
        primary key (account_id, studies_id)
    ) engine=MyISAM

create table study (
       id bigint not null,
        name varchar(255),
        primary key (id)
    ) engine=MyISAM


// 저장하는 순서

Hibernate:
    insert
    into
        account
        (password, username, id)
    values
        (?, ?, ?)
Hibernate:
    insert
    into
        study
        (name, id)
    values
        (?, ?)
Hibernate:
    insert
    into
        account_studies
        (account_id, studies_id)
    values
        (?, ?)

// 데이터 조회
mysql> select * from account;
+----+-----------+----------+
| id | password  | username |
+----+-----------+----------+
|  1 | hibernate | jimmy    |
+----+-----------+----------+

mysql> select * from study;
+----+-------------------+
| id | name              |
+----+-------------------+
|  2 | spring data study |
+----+-------------------+

mysql> select * from account_studies;
+------------+------------+
| account_id | studies_id |
+------------+------------+
|          1 |          2 |
+------------+------------+  
```

- 테이블을 생성할때, account_study를 따로 만든다.
- 삽입할때, account, study를 먼저 삽입하고 두 정보를 가지고 있는 account_studies에 account_id, studies_id를 함께 넣는다.
- 데이터를 조회하면 account, study테이블에는 관계에 대한 정보가 아무것도 없다. account_studies에 다 들어가있다.





#### 양방향

- Account에 OneToMany붙여서 studies조회하고, Study에 ManyToOne붙여서 owner를 조회하면**양방향 관계 아니다. 각각 단방향관계다.**
- 양방향을 만들기 위해 OneToMany에 mappedBy설정을 해야한다. mappedBy를 안붙이는것과 붙이는것의 sql 구조적인 차이는 아직 잘 모르겠다.
- 양방향관계에서는 foreignKey를 가진쪽이 owner이다.

- 관계의 주인이 굉장히 중요해진다.

```java
@Component
@Transactional
public class JpaRunner implements ApplicationRunner {
    @PersistenceContext
    EntityManager entityManager;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        Account account = new Account();
  			...
        Study study = new Study();
        study.setName("spring data study");
        account.getStudies().add(study);
  			study.setOwner(account); // 이 코드를 반드시 추가해야한다.
    }
}

// 코드추가 안했을때
mysql> select * from study;
+----+-------------------+----------+
| id | name              | owner_id |
+----+-------------------+----------+
|  2 | spring data study |     NULL |
+----+-------------------+----------+

// 코드추가  
mysql> select * from study;
+----+-------------------+----------+
| id | name              | owner_id |
+----+-------------------+----------+
|  2 | spring data study |        1 |
+----+-------------------+----------+  
```

- 관계의 주인은 Study인데, Account쪽에서 추가를 하고 있다.
- 위에 있는 2줄의 코드를 하나의 메서드로 만들어서 설정을 해주면 좋다.

```java
// In Account Class
public void addStudy(Study study) {
        this.getStudies().add(study);
        study.setOwner(this);
    }

public void removeStudy(Study study) {
        this.getStudies().remove(study);
        study.setOwner(null);
    }
```

- 위와 같은 메서드를 convenient메서드라고 한다.
