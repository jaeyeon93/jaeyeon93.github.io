---
layout: post
title: 2020-4-22 스프링 Data common (6) 빈탐색원리
comments: true
---
### 스프링데이터 JPA

JpaRepository\<Entity, Id> 인터페이스

- @Repository 없이도 빈으로 등록해준다.



@EnableJpaRepositories

- Configuration에 같이 붙여야되지만 SpringBoot에서는 자동으로 해준다.



#### 빈으로 등록하는 원리

```java
@Import({JpaRepositoriesRegistrar.class})
public @interface EnableJpaRepositories {
   }



/**
 * {@link ImportBeanDefinitionRegistrar} to enable {@link EnableJpaRepositories} annotation.
 *
 * @author Oliver Gierke
 */
class JpaRepositoriesRegistrar extends RepositoryBeanDefinitionRegistrarSupport {

	/*
	 * (non-Javadoc)
	 * @see org.springframework.data.repository.config.RepositoryBeanDefinitionRegistrarSupport#getAnnotation()
	 */
	@Override
	protected Class<? extends Annotation> getAnnotation() {
		return EnableJpaRepositories.class;
	}

	/*
	 * (non-Javadoc)
	 * @see org.springframework.data.repository.config.RepositoryBeanDefinitionRegistrarSupport#getExtension()
	 */
	@Override
	protected RepositoryConfigurationExtension getExtension() {
		return new JpaRepositoryConfigExtension();
	}
}   
```

- @Import의 value는 Class\<?> []로 되어있다. 따라서 JpaRepositoriesRegistrar.class라는 클래스를 import한다.
- JpaRepositoriesRegister를 보면 ImportBeanDefinitionRegistrar가 최상위 인터페이스로 있는것을 확인할 수 있다.
- ImportBeanDefinitionRegister는 빈을 프로그래밍으로 등록할 수 있게 해준다.

```java
public class JimmyRegister implements ImportBeanDefinitionRegistrar {
    @Override
    public void registerBeanDefinitions(AnnotationMetadata importingClassMetadata, BeanDefinitionRegistry registry) {
        GenericBeanDefinition beanDefinition = new GenericBeanDefinition();
        beanDefinition.setBeanClass(Jimmy.class);
        beanDefinition.getPropertyValues().add("name", "jimmy");
        registry.registerBeanDefinition("jimmyBean", beanDefinition);
    }
}

public class Jimmy {
    private String name;
    private Long id;
}

@SpringBootApplication
@Import({JimmyRegister.class})
public class DemoApplication {
	public static void main(String[] args) {
    ...
	}
}

@Component
@Transactional
public class JpaRunner implements ApplicationRunner {
    @Autowired
    private ApplicationContext context;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        System.out.println("----");
        System.out.println(context.getBean("jimmyBean"));
        System.out.println("====");
    }
}

//result
me.jimmy.springmvc.jpa.Jimmy@d7dd13a
```

- ImportBeanDefinitionRegistar에서 class기반의 빈을 등록한다.
- @Import{register.class}를 통해 빈등록하는 register를 import하면 빈 어노테이션을 안붙인 클래스들도 빈으로 등록된다.
- 위의 원리로 JpaRepository가 빈으로 등록된다.



#### 로깅

```properties
logging.level.org.hibernate.SQL=debug // show-sql=true와 유사
logging.level.org.hibernate.type.descriptor=trace // values (?, ?, ?)에 값을 보여준다.
```

- Show-query=true를 하면 들어가는 values(?, ?)로 나온다. 값을 보기 위해서는 위의 코드들을 넣자. descriptor=trace레벨로 하자.
