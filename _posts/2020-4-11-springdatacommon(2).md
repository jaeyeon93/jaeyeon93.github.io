---
layout: post
title: 2020-4-11 스프링 Data common (2) JPA 시작 및 엔티티매핑
comments: true
---
## JPA 시작

```properties
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/jimmydb?serverTimezone=UTC
spring.datasource.username=id
spring.datasource.password=pw
spring.jpa.hibernate.ddl-auto=create => 앱을 실행할때마다 다 초기화해서 새로 만든다. 계속 유지하고 싶으면 update

spring.jpa.hibernate.ddl-auto=validate // 실제로는 validate가 좋다. create는 새로만들겠다.
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5Dialect
```

- 하이버네이트가 구동시에 체크. 개발시에는 create or update 실제 배포에서는 validate
- 특정 MySQL버전부터는 timezone을 설정해줘야한다. 그래서 serverTimezone=UTC로 설정
- Hibernate.dialect 옵션을 통해 MySQL 5.x 버전을 사용한다고 명시한다. 이 옵션을 변경하면 다른 데이터베이스로 변경하기 용이하다.

```java
@Entity
public class Account {

    @Id @GeneratedValue
    private Long id;

    private String username;

    private String password;
}
```

- @Entity는 DB에 Account에 테이블에 매핑된다는 것을 가르쳐준다.
- field들은 기본적으로 테이블에 다 매핑이 된다. @Entity때문. @Column이 생략된거나 마찬가지.
- JPA를 사용하는데 필요한 BEAN들이 자동으로 주입받는다.

```java
@Component
@Transactional
public class JpaRunner implements ApplicationRunner {
    @PersistenceContext
    EntityManager entityManager;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        Account account = new Account();
        account.setUsername("jimmy");
        account.setPassword("hibernate");
        Session session = entityManager.unwrap(Session.class);
        session.save(account);

//        entityManager.persist(account);
    }
}
```

- Spring에서 ApplicationContext가 핵심이듯이 JPA에서는 EntityManager가 핵심이다. 엔티티매니저를 통해 데이터베이스를 영속화 할 수 있다.
- 영속화 : 데이터베이스에 저장한다.
- 엔티티매니저와 관련된 모듈과 오퍼레이션들은 동일한 트랜잭션에서 동작해야한다. @Transactional를 통해 클래스, 메서드에 적용한다.



## 엔티티매핑

####  javax.persistence에서 오는 어노테이션을 써야한다.



- 테이블 매핑하는 방법
  - 어노테이션
  - XML => 최근에는 거의 보기 힘들다.



@Entity

- 이름을 줄 수 있지만 기본적으로 클래스와 같은 이름을 사용하기 때문에 바꾸지 않는다. 엔티티의 이름은 하이버네이트에서 적용되는 이름이고 실제 테이블 이름은 @Table에 이름을 붙여서 사용한다. **하지만 엔티티에 아무런 설정을 안하면 클래스이름으로 설정이 된다. 문제가 생길때가 있다. 어떤 디비에서는 User, 특정 키워드를 등록 못하는경우가 있다. **



@Id

- main key를 설정. 모든 Primitive와 Wrapper 타입을 지원.
- primitive를 쓸것이냐 wrapper를 쓸것이냐가 논쟁이있다. long(primitive)면 기본적으로 0으로 세팅된다. DB에서 만들어진 0과 내가 직접만든 0을 구분하기 위해 Wrapper를 쓰는게 좋다.



@GenerateValue

- 주키를 자동매핑.
- DB에 따라 자동생성하는 전략을 취한다.
  - 기본전략은 AUTO
  - TABLE, IDENTITY, SEQUENCE 중 하나.



@Column

- 모든 엔티티에 들어있는 멤버변수에는 @Column이 생략
- Column에는 다양한 옵션들이 있다(nullable, unique etc.. )
-

@Temporal

- JPA 2.1에서는 Date, Calander밖에 안된다. 2.2이후로는 java8이후의 데이트 라이브러리들을 지원한다.



```java
@Temporal(TemporalType.DATE)
DATE
TIME
TIMESTAMP
```

@Trasient

- 컬럼으로 매핑하고 싶지 않은 멤버에 설정



```properties
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
```

- show-sql을 하면 콘솔에 sql을 찍어준다. 가독성이 안좋아서 format_sql도 함께 넣어준다.
- 개발시에는 true하고. 운영시에 보이게 하기 위해서는 하이버네이트보다는 로거에서 쓴다.





엔티티타입과 Value타입 구분

- 식별자가 있어야 하는가
- 독립적으로 존재해야 하는가
- value : 다른 엔티티에 종속적인가



Composit Value 타입 매핑

- @Embadable
- @Embadded
- @AttributeOverrides
- @AttributeOverride

```java
@Entity
public class Account {
    @Id @GeneratedValue
    private Long id;
		.
		.
    .
    @Embedded
    @AttributeOverrides({
            @AttributeOverride(name = "street", column = @Column(name = "home_street"))
    })
    private Address homeAddress;
}

@Embeddable
public class Address {
    private String street;

    private String city;

    private String state;

    private String zipcode;
}
// DB table
create table account (
       id bigint not null,
        create_date date,
        city varchar(255),
        state varchar(255),
        home_street varchar(255),
        zipcode varchar(255),
        password varchar(255),
        username varchar(255),
        primary key (id)
    ) engine=MyISAM
```

- Embeddable을 붙여서 의존관계를 설정한다. Account에는 homeAddress, OfficeAddress 등이 존재할 수 있으므로 @AttributeOverrides를 설정한다.
- @Embeddable한 클래스를 실행하면 테이블이 따로 생성되는 것이 아니라 상위?클래스에 테이블 열에 같이 생성된다.



Value타입 종류

- 기본타입(String, Date, Boolean...)
- Compose Value타입
- Collection Value타입
  - 기본타입컬렉션
  - 컴포짓 타입의 콜렉션
