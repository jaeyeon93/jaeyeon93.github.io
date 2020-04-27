---
layout: post
title: 2020-4-25 스프링 Data common (8) 정리
comments: true
---
# SpringData

![spring-data](/Users/jaeyeonkim/Desktop/spring-data.png)

- 스프링데이터는 여러개의 프로젝트들의 묶음이다. (JPA, JDBC, KeyValue, MongoDB, Redis...) 이 프로젝트들의 공통된 속성은 Spring Data Common이다.
- 스프링데이터 : SQL, NoSQL저장소 지원 프로젝트의 묶음
- 스프링 데이터 Common : 여러 저장소 지원 프로젝트들의 묶음



## SpringData Common

![spring-data-2](/Users/jaeyeonkim/Desktop/spring-data-2.png)

- Repository는 marker interface이고 실제적인 기능은 CrudRepository에 메서드가 정의되어있다.
- 중간 레포지토리들은 빈을 만들지 안도록 @NoRepositoryBean가 붙어있다.

```properties
<dependency>
	<groupId>com.h2database</groupId>
  <artifactId>h2</artifactId>
  <scope>test</scope>
</dependency>
```

- h2를 test범위에서만 사용하게 함

```java
@RunWith(SpringRunner.class)
@DataJpaTest
public class PostRepositoryTest {
    @Autowired
    private PostRepository postRepository;

    @Test
    @Rollback(false)
    public void crudRepository() {
        // Given
        Post post = new Post();
        post.setTitle("hello spring common");
        assertThat(post.getId()).isNull();

        // When
        Post newPost = postRepository.save(post);

        // Then
        assertThat(newPost.getId()).isNotNull();

        // When JPA Repository method
        List<Post> posts = postRepository.findAll();

        // Then
        assertThat(posts.size()).isEqualTo(1);
        assertThat(posts).contains(newPost);

        // When
        Page<Post> page = postRepository.findAll(PageRequest.of(0, 10));
        assertThat(page.getTotalElements()).isEqualTo(1);
        assertThat(page.getNumber()).isEqualTo(0);
        assertThat(page.getSize()).isEqualTo(10);
    }
}
```

- Data Access Layer 테스트를 위해 @DataJpaTest를 제공

- 실제 쿼리를 보면 insert쿼리가 날라가지 않는다. 쿼리가 안날라가는건 JPA때문이고, 스프링은 기본적으로 트랜잭션이 걸려있어서 롤백이 되었다. 그래서 안보인다.



#### 인터페이스 정의하기

- repository를 커스텀하게 만들기

```java
// interface
@NoRepositoryBean
public interface MyRepository<T, Id extends Serializable> extends Repository<T, Id> {
    <E extends T> E save(@NonNull E entity);

    List<T> findAll();

    long count();

    @Nullable
    <E extends T>Optional<E> findById(Id id);
}
// repository
public interface CommentRepository extends MyRepository<Comment, Long> {
}
//Test
@RunWith(SpringRunner.class)
@DataJpaTest
public class CommentRepositoryTest {

    @Autowired
    private CommentRepository commentRepository;

    @Test
    public void crudTest() {
        Comment comment = new Comment();
        comment.setComment("Hello Comment");
        commentRepository.save(comment);

        List<Comment> all = commentRepository.findAll();
        assertThat(all.size()).isEqualTo(1);
    }
}
```



#### Null처리

- 스프링데이터2.0부터 자바8의 Optional지원
- 컬렉션은 Null을 리턴하지 않고 비어있는 컬렉션을 리턴. 따라서 null체크를 할 필요가 없다.
- 스프링 프레임워크 5.0부터 Null 어노테이션 지원
  - @NonNullApi, @NonNull, @Nullable
  - 런타임 체크 지원한다
  - Intellij가 아직은 nullable을 지원하는게 정확하지 않ㄴ다. runtime assertions를 검색해야한다.

#### 쿼리만들기

1. 메서드 이름을 분석해서 스프링 데이터가 직접 쿼리를 만드는 방법(create)
2. 미리 정의한 쿼리 찾아 사용(USE_DECLARED_QUERY)
3. 미리 정의한 쿼리 찾아보고 없으면 만들기(CREATE_IF_NOT_FOUND)



- @SpringApplication과 함께 @EnalbeJpaRepositories(queryLookupStrategy =... )을 붙인다. 기본값은 CREATE_IF_NOT_FOUND

```java
public interface CommentRepository extends MyRepository<Comment, Long> {

    @Query(value = "SELECT c FROM Comment AS c", nativeQuery = true) // case2
    List<Comment> findByTitleContains(String keyword); // case1
}
```

- 메서드 이름만 있으면 메서드 이름을 보고 쿼리를 생성하고, @Query가 있으면 쿼리를 보고 생성한다.



#### 어떻게 쿼리를 생성하는가?

```java
public @interface EnableJpaRepositories {
	Key queryLookupStrategy() default Key.CREATE_IF_NOT_FOUND;
}

public interface QueryLookupStrategy {
	RepositoryQuery resolveQuery(Method method, RepositoryMetadata metadata, ProjectionFactory factory,
			NamedQueries namedQueries);
}


```

- QueryLookupStrategy에 보면 resolveQuery라는 메서드가 있다.
- RepositoryQuery의 구현체인 JpaQueryLookupStrategy에 보면 쿼리를 생성하는 전략들이 존재
- JpaQueryLookupStrategy 내부에는 내부클래스로 DeclaredQueryLookupStrategy, CreateQueryLookupStrategy 등 내부클래스들이 정의되어있다.



#### 쿼리정의하기

- 쿼리를 못만드는 경우면 아예 처음부터 에러가 난다.

> - 리턴타입 {접두어}{도입부}By{프로퍼티 표현식}(조건식)[And|Or]{프로퍼티표현식}{조건식}{정렬조건}{매개변수}...
> - 접두어 : Find, Get, query, count...
> - 도입부 : distinct, first(number), top(number)
> - 프로퍼티표현식 :
> - 조건식 : IngnoreCase, between, lessthan, greaterthan, like, contains...
> - 정렬조건 : orderBy{프로퍼티}asc|desc
> - 리턴타입 : E, Optional\<E>, List\<E>, Page\<E>, Slice\<E>, Stream\<E>
> - 매개변수 : Pageable, Sort

### 커스텀 레포지토리

#### 기능을 추가

```java
public interface PostCustomRepository {
    List<Post> findMyPost();
}

public class PostCustomRepositoryImpl implements PostCustomRepository {
    @Autowired
    private EntityManager entityManager;

    @Override
    public List<Post> findMyPost() {
        System.out.println("custom findMyPost");
        return entityManager.createQuery("SELECT p from Post AS p", Post.class)
                .getResultList();
    }
}

public interface PostRepository extends JpaRepository<Post, Long>, PostCustomRepository {
}
```

- CustomRepository라는 인페이스를 정의하고, 그 구현체를 만든다. 그리고 CustomRepository인터페이스를 기존 PostRepository에서 implement하도록 한다.



#### 기본기능을 재정의(덮어쓰기)



```java
//JpaRepository의 구현체인 SimpleJpaRepository의코드
@Transactional
	public void delete(T entity) {

		Assert.notNull(entity, "The entity must not be null!");
		em.remove(em.contains(entity) ? entity : em.merge(entity));
	}

// 인터페이스에 추가
public interface PostCustomRepository<T> {
    List<Post> findMyPost();
    void delete(T entity);
}

// delete 재정의
public class PostCustomRepositoryImpl implements PostCustomRepository {
    @Override
    public void delete(Object entity) {
        System.out.println("custom delete");
        entityManager.remove(entity);
    }
}

//Test
@Test
public void customRepository() {
    Post post = new Post();
    post.setTitle("hibernate");
    postRepository.save(post);
  	postRepository.findMyPost(); // case2
    postRepository.delete(post);
  	postRepository.flush(); // case3. delete가 출력된다.
}
```

- merge를 detached상태의 인스턴스를 persistence상태로 변경한다.
- detached상태의 인스턴스는 persistence 상태에서 빠진상태. persistenceContext에서 관리를 받지않는 상태.
- remove상태로 바꾼다. => persistencecontext에서 관리를 하는중. cascade때문에 바로 지우지 않고 removed상태를 우선 유지하는것이다.
- delete가 중복된다. jpa는 내가 커스텀하게 구현한 구현체를 우선순위로 준다. 따라서 위의 PostCustomRepositoryImpl에서 정의된 delete가 호출된다.
- @Rollback을 false로 하면 delete, insert 쿼리가 보인다. 하지만 기본적으로 롤백이 적용되기때문에 보이지 않는다.
- findMyPost()는 select이고, insert에 영향을 주기때문에 쿼리가 찍힌다. Delete가 안찍히는 이유는 기본적으로 transaction에서 롤백이 되기때문에 안한다. 강제로 하기 위해서는 flush()를 추가해야한다.
- flush를 추가하면 강제적으로 database와 sync를 한다.



### 모든 엔티티 레포지토리에 오버라이드하고 커스텀 기능넣기

1. JpaRepostiory를 상속 받는 인터페이스 정의
2. 기본 구현체를 상속 받는 커스텀 구현체 만들기
3. @EnableRepositories에 설정
   - repositoryBaseClass

```java
// step1
@NoRepositoryBean
public interface CommonRepository<T, ID> extends JpaRepository<T, ID> {
    boolean contains(T entity);
}

// step2
public class CommonRepositoryImpl<T, ID extends Serializable> extends SimpleJpaRepository<T, ID> implements CommonRepository<T, ID> {
    private EntityManager entityManager;
    public CommonRepositoryImpl(JpaEntityInformation<T, ?> entityInformation, EntityManager entityManager) {
        super(entityInformation, entityManager);
        this.entityManager = entityManager;
    }
    @Override
    public boolean contains(T entity) {
        return entityManager.contains(entity);
    }
}

// step3
@EnableJpaRepositories(repositoryBaseClass = CommonRepositoryImpl.class)

// step4
public interface PostRepository extends CommonRepository<Post, Long> {
}
```

- JpaRepository를 상속받는 인터페이스를 정의한다. 사용하지 않을꺼기 때문에 @NoRepositoryBean을 추가
- JpaRepository의 구현체는 SimpleJpaRepository로서 앞에서 정의한 인터페이스를 구현하고, SimpleJpaRepository를 상속받은 CommonRepositoryImpl을 정의. 여기서 핵심은 EntityManager를 생성자에 추가하고, custom method를 정의한 부분이다.
- 정의한 Repository를 상속한다.
- 기타 기능들을 더 정의하고 싶으면 SimpleJpaRepository를 상속한 클래스에서 다른 메서드들을 override한다.



#### 도메인 이벤트

- 도메인 이벤트 퍼블리셔 : @Entity를 붙인 도메인 클래스의 변화가 있을때 이벤트를 발생시킨다.
- ApplicationContext가 ApplicationEventPublisher를 상속한다. 따라서 이벤트 퍼블리싱을 할 수 있다.
  - 리스너 : implement ApplicationListenr\<E extends ApplicationEvent>
  - @EventListener

```java
// 기존의 방식
// 이벤트 퍼블리셔
public class PostPublishedEvent extends ApplicationEvent {

    private final Post post;
    /**
     * Create a new ApplicationEvent.
     *
     * @param source the object on which the event initially occurred (never {@code null})
     */
    public PostPublishedEvent(Object source) {
        super(source);
        this.post = (Post)source;
    }

    public Post getPost() {
        return post;
    }
}

// 리스너
public class PostListener implements ApplicationListener<PostPublishedEvent> {
    @Override
    public void onApplicationEvent(PostPublishedEvent event) {
        System.out.println("------");
        System.out.println(event.getPost().getTitle() + " post published");
        System.out.println("------");
    }
}

// 설정빈
@Configuration
public class PostRepositoryConfig {
    @Bean
    public PostListener postListener() {
        return new PostListener();
    }
}

// 테스트코드
@RunWith(SpringRunner.class)
@DataJpaTest
@Import(PostRepositoryConfig.class)
public class DomainPublishTest {
    @Autowired
    ApplicationContext applicationContext;

    @Test
    public void event() {
        Post post = new Post();
        post.setTitle("event");
        PostPublishedEvent event = new PostPublishedEvent(post);

        applicationContext.publishEvent(event);
    }
}

```

- 기존에는 이벤트 퍼블리셔 리스너를 다 만들어야됬지만, 스프링 JPA에서는 이벤트를 자동으로 발생시키고 퍼블리싱하는 기능을 제공해준다.

스프링 데이터 도메인이벤트 Publisher

- @DomainEvents : 이벤트를 모아두는곳
- @AfterDomainEventPublication : 컬렉션에 담아있던 이벤트를 자동으로 비워주는 메서드
- extends AbstractAggregationRoot\<E> : 위의 기능들을 미리 구현해놓은클래스
- 현재는 save()할때만 발생
- 번외 : @DataJpaTest는 슬라이싱 테스트이기때문에 데이터관련된 빈들만 등록이 된다. 따라서 @Import로 직접적으로 빈을 추가해주지 않으면 @Configuration이 붙은 설정빈은 등록이 안된다.

```java
// 방법1
public class PostListener {
    @EventListener
    public void onApplicationEvent(PostPublishedEvent event) {
        System.out.println("------");
        System.out.println(event.getPost().getTitle() + " post published");
        System.out.println("------");
    }
}

// 방법2
@Configuration
public class PostRepositoryConfig {
    @Bean
    public ApplicationListener<PostPublishedEvent> postListener() {
        return event -> {
            System.out.println("------------------------");
            System.out.println(event.getPost().getTitle() + " is publishedEvenet");
            System.out.println("------------------------");
        };
    }
}
```

- 위의 퍼블리셔, 설정파일은 그대로 두고. 리스너에서 ApplicationEvent를 상속받던것을 @EventListener로 대체했다. 이 기능을 사용하기 위해서는 PostListener가 반드시 빈으로 등록되어야한다.
- 설정파일에 바로 리스트를 만들어도 된다!!!



```java
public class AbstractAggregateRoot<A extends AbstractAggregateRoot<A>> {
	private transient final @Transient List<Object> domainEvents = new ArrayList<>();
	/**
	 * Registers the given event object for publication on a call to a Spring Data repository's save methods.
	 *
	 * @param event must not be {@literal null}.
	 * @return the event that has been added.
	 * @see #andEvent(Object)
	 */
	protected <T> T registerEvent(T event) {
		Assert.notNull(event, "Domain event must not be null!");
		this.domainEvents.add(event);
		return event;
	}
	/**
	 * Clears all domain events currently held. Usually invoked by the infrastructure in place in Spring Data
	 * repositories.
	 */
	@AfterDomainEventPublication
	protected void clearDomainEvents() {
		this.domainEvents.clear();
	}
	/**
	 * All domain events currently captured by the aggregate.
	 */
	@DomainEvents
	protected Collection<Object> domainEvents() {
		return Collections.unmodifiableList(domainEvents);
	}
	.....
}

```

-  스프링 데이터의 이벤트 퍼블리셔를 사용하기 위해서는 AbstractAggregateRoot\<Post>를 상속받는다.
-  내부적인 코드는 이벤트들을 컬렉션 형태로 저장하고 있는 domainEvents가 있고, registerEvent()메서드를 보면 domainEvents에 이벤트들을 추가할 수 있다.
-  @DomainEvents가 붙은 domainEvents()의 경우 unmodifiableList를 통해 읽기전용 컬렉션을 리턴해준다.



#### queryDSL

사용하는 이유 : 조건문을 표현하는 방법이 타입세이프하다. 자바코드로 조건문을 표현하고 조합할 수 있다.



쿼리메소드

- Optional\<T> findOne(predicate): 이런 조런 조건으로 하나를 찾는다.
- List\<T> | Page\<T> | ...findAll(predicate) : 이런저런 조건으로 무언가를 여러개 찾는다.
- QuerydslPredicateExecutor 인터페이스



querydsl

- [공식홈페이지](http://www.querydsl.com)
- 타입세이프한 쿼리를 만들 수 있게 도와주는 라이브러리
- JPA, SQL, MongoDB, JDO, Lucene, Collection 지원
- [JPA연동 가이드](http://www.querydsl.com/static/querydsl/4.1.3/reference/html_single/#jpa_integration)



연동방법

- 기본리포지토리 커스텀 안하면 쉽다
- 커스텀하면 어렵다.

```xml
<!--		querydsl dependency-->
		<dependency>
			<groupId>com.querydsl</groupId>
			<artifactId>querydsl-apt</artifactId>
		</dependency>
		<dependency>
			<groupId>com.querydsl</groupId>
			<artifactId>querydsl-jpa</artifactId>
		</dependency>


<!-- plugin -->
<plugin>
				<groupId>com.mysema.maven</groupId>
				<artifactId>apt-maven-plugin</artifactId>
				<version>1.1.3</version>
				<executions>
					<execution>
						<goals>
							<goal>process</goal>
						</goals>
						<configuration>
							<outputDirectory>target/generated-sources/java</outputDirectory>
							<processor>com.querydsl.apt.jpa.JPAAnnotationProcessor</processor>
						</configuration>
					</execution>
				</executions>
			</plugin>
```



```java
public interface AccountRepository extends JpaRepository<Account, Long>, QuerydslPredicateExecutor<Account> {
}

@Entity
public class Account {
    @Id @GeneratedValue
    private Long id;
    private String firstName;
    private String lastName;
		....
}

@DataJpaTest
@RunWith(SpringRunner.class)
public class AccountTest {
    @Autowired
    private AccountRepository accountRepository;
    @Test
    public void crud() {
        QAccount account = QAccount.account;
        Predicate predicate = account
                .firstName.containsIgnoreCase("jimmy")
                .and(account.lastName.startsWith("kim"));
        Optional<Account> one = accountRepository.findOne(predicate);
        assertThat(one).isEmpty();
    }
}
```

- 위의 방법을 이용해서 queryDSL을 사용할 수 있다. custom하는 방법은 차후에..





## Common: Web 웹지원기능

- EnableSpringDataWebSupport



제공하는 기능

- 도메인 클래스 컨버터
- @RequestHandler 메소드에서 Pageable과 Sort매개변수 사용
- Page관련 HATEOAS기능 제공
  - HATEOAS : 하이퍼미디어를 좀 더 잘 사용해라?
  - PagedResourcesAssembler
  - PagedResource
- Payload프로젝션
  - @ProjectedPayload을 사용. 요청으로 들어오는 JSON의 일부만 사용해서 바인딩한다.
  - 요청으로 들어오는 데이터 중 일부만 바인딩 받아오기
  - @ProjectedPayload, @XBRead, @JsonPath
- 요청 쿼리 매개변수를 QueryDSLdml Predicate로 받아오기
  - ?firstname=Mr&lastname=White => Predicate



```java
@RunWith(SpringRunner.class)
@SpringBootTest
@AutoConfigureMockMvc
public class PostControllerTest {
  @Test
    public void getPost() throws Exception {
        Post post = new Post();
        post.setTitle("mock");
        postRepository.save(post);

        mockMvc.perform(get("/posts/" + post.getId()))
                .andDo(print())
                .andExpect(status().isOk())
                .andExpect(content().string("mock"));
    }
}

```

- integrationTest라서 모든 빈들이 생성되고 설정파일들이 등록된다.



#### DomainClassConverter



- [컨버터링크](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/convert/converter/Converter.html) 비교. spring formatter
- DomainClassConverter : 타입을 다른 타입으로 변환하는 인터페이스.

```java
@GetMapping("/posts/{id}")
    public String getPost(@PathVariable("id") Post post) {
        return post.getTitle();
    }

@GetMapping("/posts")
    public Page<Post> getPosts(Pageable pageable) {
        return postRepository.findAll(pageable);
    }

@RunWith(SpringRunner.class)
@SpringBootTest
@AutoConfigureMockMvc
@ActiveProfiles("test")
public class PostControllerTest {
    @Autowired
    MockMvc mockMvc;

    @Autowired
    private PostRepository postRepository;

    @Test
    public void getPosts() throws Exception {
        Post post = new Post();
        post.setTitle("mock");
        postRepository.save(post);

        mockMvc.perform(get("/posts/")
                .param("page", "0")
                .param("size","10")
                .param("sort","created,desc")
                .param("sort","title"))
                .andDo(print())
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.content[0].title", is("mock")));
    }
}
```

- pageable을 이용해서 데이터를 가져올 수도 있다.



#### HATEOAS

- PagedResourcesAssembler\<T> T타입의 Resource를 만들어준다.

```java
@GetMapping("/posts")
    public PagedModel<EntityModel<Post>> getPosts(Pageable pageable, PagedResourcesAssembler<Post> assembler) {
        Page<Post> all = postRepository.findAll(pageable);
        return assembler.toModel(all);
    }
```

- PagedResource들을 활용한다.



```json
{
  "_embedded":{
    "postList":[
      {
        "id":67,
        "title":"jpa",
        "created":null
      },
      {
        "id":69,
        "title":"jpa",
        "created":null
      },
			....
    ]
  },
  "_links":{
    "first":{ // 첫번째 페이지 링크
      "href":"http://localhost/posts?page=0&size=10&sort=created,desc&sort=title,asc"
    },
    "self":{ // 현재페이지 링크
      "href":"http://localhost/posts?page=0&size=10&sort=created,desc&sort=title,asc"
    },
    "next":{
      "href":"http://localhost/posts?page=1&size=10&sort=created,desc&sort=title,asc"
    },
    "last":{
      "href":"http://localhost/posts?page=9&size=10&sort=created,desc&sort=title,asc"
    }
  },
  "page":{
    "size":10,
    "totalElements":100,
    "totalPages":10,
    "number":0
  }
}
```

- 헤이토스는 위에 있는 links들이 핵심이다.



```json
// pageable response
{
  "content":[
    {
      "id":76,
      "title":"jpa",
      "created":null
    },
    {
      "id":68,
      "title":"jpa",
      "created":null
    },
   ....
  ],
  "pageable":{
    "sort":{
      "unsorted":false,
      "sorted":true,
      "empty":false
    },
    "offset":0,
    "pageNumber":0,
    "pageSize":10,
    "paged":true,
    "unpaged":false
  },
  "last":false,
  "totalPages":10,
  "totalElements":100,
  "size":10,
  "number":0,
  "numberOfElements":10,
  "first":true,
  "sort":{
    "unsorted":false,
    "sorted":true,
    "empty":false
  },
  "empty":false
}
```

- pageable에 대한 정보. 헤이토스는 링크의 이름만 알면 잘 활용할 수 있다.
