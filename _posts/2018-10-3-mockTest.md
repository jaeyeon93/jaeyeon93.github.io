---
layout: post
title: 2018-10-3 Spring Test
---

Spring Test
---

- 스프링에서 테스트를 하기 위해서 밑에처럼 spring-boot-starter-test 의존성을 추가해야한다.
```
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-test</artifactId>
  <scope>test</scope>
</dependency>
```

### 테스트코드 작성하기


#### MockMVC를 사용해서 테스트하기
```
@RunWith(SpringRunner.class)
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.MOCK)
@AutoConfigureMockMvc
public class SampleControllerTest {
    @Autowired
    MockMvc mockMvc;

    @Test
    public void hello() throws Exception {
        mockMvc.perform(get("/hello"))
                .andExpect(status().isOk())
                .andExpect(content().string("hello jimmy"))
                .andDo(print());
    }
}

//result
MockHttpServletRequest:
      HTTP Method = GET
      Request URI = /hello
       Parameters = {}
          Headers = {}
             Body = null
    Session Attrs = {}

Handler:
             Type = me.jimmy.sample.SampleController
           Method = public java.lang.String me.jimmy.sample.SampleController.hello()

Async:
    Async started = false
     Async result = null

Resolved Exception:
             Type = null

ModelAndView:
        View name = null
             View = null
            Model = null

FlashMap:
       Attributes = null

MockHttpServletResponse:
           Status = 200
    Error message = null
          Headers = {Content-Type=[text/plain;charset=UTF-8], Content-Length=[11]}
     Content type = text/plain;charset=UTF-8
             Body = hello jimmy
    Forwarded URL = null
   Redirected URL = null
          Cookies = []
```
- 위와 같이하면 서브릿컨테이너를 띄우지 않고, Mocking을 해서, MockMVC를 통해 Mockup된 서브릿에 요청을 한다.
- WebEnvironment.MOCK일때 사용을 한다.


#### RandomPort를 사용해서 테스트하기

- 실제로 서브릿과 내장톰켓이 실행된다. RestTemplate, TestRestTemplate, TestWebClient를 사용을 해야한다.

```
@RunWith(SpringRunner.class)
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class SampleControllerTest2 {

    @Autowired
    TestRestTemplate testRestTemplate;

    @Test
    public void hello() throws Exception {
        String result = testRestTemplate.getForObject("/hello", String.class);
        assertThat(result, is("hello jimmy"));
    }
}
```
- 실제로 내장 톰켓이 들어간다.

#### Q. Service단까지 안가고 Controller단에서 끊고 싶다면??

```
@MockBean
SampleService mockSampleService;
```
- 위의 코드를 사용하면 ApplicatinoContext에 들어있는 SampleService Bean을 위에서 만든 mockBean으로 교체를 한다.
- 실제적으로 서비스는 MockSampleService를 사용하게 된다.

```
@RunWith(SpringRunner.class)
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class SampleControllerTest2 {

    @Autowired
    TestRestTemplate testRestTemplate;

    @MockBean
    SampleService mockSampleService;

    @Test
    public void hello() throws Exception {
        when(mockSampleService.getName()).thenReturn("whiteship");

        String result = testRestTemplate.getForObject("/hello", String.class);
        assertThat(result, is("hello whiteship"));
    }
}
```

### WebTestClient를 사용하기

- 기존에 우리가 사용하던 RestClient sync 방식이라서 요청이 끝날때까지 기다린다.
- WebClient는 비동기방식이라서 바로바로 callBack을 할 수 있다.


```
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-webflux</artifactId>
</dependency>
```
- webClient를 사용하기 위해서는 webflux라는 의존성을 추가한다.
- RestTemplate이 가장불편, 그 다음 MockMVC, WebClient가 가장 편하다.

```
@RunWith(SpringRunner.class)
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class SampleControllerTest3 {
    @Autowired
    WebTestClient webTestClient;

    @MockBean
    SampleService mockSampleService;

    @Test
    public void hello() throws Exception {
        when(mockSampleService.getName()).thenReturn("whiteship");

        webTestClient.get().uri("/hello").exchange()
                .expectStatus().isOk()
                .expectBody(String.class).isEqualTo("hello whiteship");
    }
}
```
- 위의 3가지 테스트는 통합테스트이다. @SpringbootTest가  @SpringApplication을 찾아가서 모든 빈을 찾아서 등록하고, Mock빈을 찾아서 교체하는 등 많은 일을 한다.
- 수많은 Bean을 등록안하고, 필요한 Bean들만 등록하기 위해서는??? 슬라이스 테스트를 사용한다.

#### 슬라이스 테스트
- 레이어별로 잘라서 테스트하고 싶을때
- @JsonTest, @WebMvcTest, @WebFluxTest, DataJpaTest


#### JsonTest

```
@RunWith(SpringRunner.class)
@JsonTest
public class SampleControllerJsonTest {
    @Autowired
    JacksonTester<//Domain>
}


```

#### WebMVCTest

```
@RunWith(SpringRunner.class)
@WebMvcTest(SampleController.class)
public class SampleControllerJsonTest {
    @MockBean
    SampleService sampleService;

    @Autowired
    MockMvc mockMvc;

    @Test
    public void hello() throws Exception {
        when(sampleService.getName()).thenReturn("whiteship");

        mockMvc.perform(get("/hello"))
                .andExpect(content().string("hello whiteship"));
    }
}

```
- @MockBean을 꼭 등록해야한다.
- @WebMvcTest를 하면, @Controller관련 어노테이션들만 빈으로 등록을 한다. @Service, @Repository등 다 등록이 안된다.


### 테스트유틸

- OutputCapture : 가장많이 쓴다. 로그를 비롯한 콘솔에 찍히는 모든것을 다 캡처한다.
- TestPropertyValues
- TestRestTemplate
- ConfigFileApplicationContextInitializer

```
@RunWith(SpringRunner.class)
@WebMvcTest(SampleController.class)
public class SampleControllerJsonTest {

    @Rule
    public OutputCapture outputCapture = new OutputCapture();

    @MockBean
    SampleService sampleService;

    @Autowired
    MockMvc mockMvc;

    @Test
    public void hello() throws Exception {
        when(sampleService.getName()).thenReturn("whiteship");

        mockMvc.perform(get("/hello"))
                .andExpect(content().string("hello whiteship"));
        System.out.println(outputCapture.toString());
    }
}
```
