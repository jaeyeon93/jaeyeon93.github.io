---
layout: post
title: 2018-10-7- SpringData InmemoryDB
---

스프링데이터 : 인메모리
-

#### 인메모리데이터베이스종류
1. H2
2. HSQL
3. Derby

#### spring-JDBC가 클래스패스에 있으면 자동 설정이 필요한 빈을 설정 해줍니다.

- DataSource
- JdbcTemplate
#### 인-메모리 데이터베이스 기본 연결 정보 확인하는 방법

- URL: “testdb”
- username: “sa”
- password: “”
#### H2 콘솔 사용하는 방법

- spring-boot-devtools를 추가하거나…
- spring.h2.console.enabled=true 만 추가.
- /h2-console로 접속 (이 path도 바꿀 수 있음)
#### 실습 코드

- CREATE TABLE USER (ID INTEGER NOT NULL, name VARCHAR(255), PRIMARY KEY (id))
- INSERT INTO USER VALUES (1, ‘keesun’)

```
public class H2Runner implements ApplicationRunner {
    public static final Logger logger = LoggerFactory.getLogger(H2Runner.class);
    @Autowired
    private DataSource dataSource;

    @Autowired
    private JdbcTemplate jdbcTemplate;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        try (Connection connection = dataSource.getConnection()) {
            logger.info("dburl : {}", connection.getMetaData().getURL());
            logger.info("dbuserName : {}", connection.getMetaData().getUserName());

            Statement statement = connection.createStatement();
            String sql = "CREATE TABLE USER (ID INTEGER NOT NULL, name VARCHAR(255), PRIMARY KEY (id))";
            statement.executeUpdate(sql);
        }

        jdbcTemplate.execute("insert into user values(1, 'jimmy')");
    }
}

```
