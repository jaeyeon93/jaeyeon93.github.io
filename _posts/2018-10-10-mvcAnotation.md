---
layout: post
title: 2018-10-10 스프링웹MVC
---

Spring web-mvc
-

```
//ControllerTest
@RunWith(SpringRunner.class)
@WebMvcTest(UserController.class)
public class UserControllerTest {

    @Autowired
    MockMvc mockMvc;

    @Test
    public void hello() throws Exception {
        mockMvc.perform(get("/hello"))
                .andExpect(status().isOk())
                .andExpect(content().string("hello"));
    }
}


//Controller Code
@RestController
public class UserController {

    @GetMapping("hello")
    public String hello() {
        return "hello";
    }
}

```
- TDD방식으로 바로 개발을 할 수 있다.
- 아무런 설정없이 Spring Web MVC를 사용할 수 있는 이유는 spring-boot-autoconfigureation안에 있는 spring.factories때문이다. 이 자동 설정 파일이 적용이 됬기때문에 우리가 아무런 문제없이 적용을 할 수 있다.


#### Q. spring-boot가 제공해주는 mvc기능을 확장하려면?

```
@Configuration
public class WebConfig implements WebMvcConfigurer {
}

// 이렇게 하면안된다
@Configuration
@EnableWebMvc
public class WebConfig implements WebMvcConfigurer {
}

```
- Configuration어노테이션만 붙어야된다. 그러면 자동설정을 확장할 수 있다. 만약에 밑에처럼 @EnableMvc를 붙이면 모든 설정을 저기서 다 해야한다.
- 스프링MVC확장 : @Configuration + WebMvcConfigurer
- 스프링MVC재정의 : @Configuration + @EnableWebMvc


## Spring Mvc HttpMessageConverters


- HttpMessageConverters : Http 요청 본문을 객체로 변경하거나, 객체를 Http 응답 본문으로 변경할 때 사용한다. RequestBody, ResponseBody와 같이 사용된다.

```
@RestController
public class UserController {

    @GetMapping("hello")
    public String hello() {
        return "hello";
    }

    @PostMapping("/user")
    public @ResponseBody User create(@RequestBody User user) {

    }
}
```
- @RequestBody User user : 이때 HttpMessageConverter가 사용이 된다. Converter는 여러가지가 있고, 우리가 요청을 받았는지, 어떤 응답을 해야되는지에 따라서 Converter가 다르다.
- 요청에 있는 Content-Type에 Json이 되있으면, JsonMessageConverter가 사용이 된다.
- 리턴할때 @ResponseBody User, 객체자체를 리턴할 수 없기때문에, 변환을 해야한다. 기본적으로는 JsonMessageConverter가 사용이 된다.
- 위에 @RestController가 붙어있으면 @ResponseBody를 생략해도 된다.


#### @RestCotroller를 안사용하고, 일반 @Controller를 사용할경우

```
@Controller
public class UserController {

    @GetMapping("hello")
    public String hello() {
        return "hello";
    }
}
```
- 위와 같이 그냥 컨트롤러 어노테이션을 붙이면 return hello를 할경우 ViewNameResolver가 동작을 해서 hello에 해당하는 뷰를 리턴해준다.
