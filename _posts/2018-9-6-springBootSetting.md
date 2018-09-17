---
layout: post
title: 2018-9-6 SpringBoot 설정
---

스프링부트 자동설정
-

- @SpringBootApplication 어노테이션은 @SpringBootConfiguration, @ComponentScan, @EanbleAutoConfiguration 3가지 어노테이션을 포함한다.

```
@Configuration
@ComponentScan
public class SpringbootGettingStartApplication {
    public static void main(String[] args) {
        SpringApplication application = new SpringApplication(SpringbootGettingStartApplication.class);
        application.setWebApplicationType(WebApplicationType.NONE);
        application.run(args);
        SpringApplication.run(SpringbootGettingStartApplication.class, args);
    }
}
```
- 위의 코드를 통해, @EnableAutoConfiguration어노테이션은 @Componentscan이 일어난 다음에 진행이 된다.

- @Componentscan : @Component이 붙은 클래스들을 스프링이 실행될 때, 빈으로 등록을 시켜준다.

```
@ComponentScan(excludeFilters = {
		@Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
```

- @CompoenetScan은 동일한 패키지내에서만 일어난다. 패키지만 주의하면 된다.
- @AutoConfiguration은 spring-boot-autoconfigure 밑에 있는 META-INF 메타파일들을 스캔한다.  spring.factory에 있는 클래스들을 찾아서 설정을 한다.


### 자동설정 만들기

```
@Configuration
public class HolomanConfiguration {

    @Bean
    public Holoman holoman() {
        Holoman holoman = new Holoman();
        holoman.setHowLong(5);
        holoman.setName("jimmy");
        return holoman;
    }
}

// /resources/META-INF/spring.factories
org.springframework.boot.autoconfigure.EnableAutoConfiguratio=\
  me.whiteship.springbootstarter.HolomanConfiguration
```
- spring.factories 파일을 만들어서, 자동설정 추가를 해야한다.
