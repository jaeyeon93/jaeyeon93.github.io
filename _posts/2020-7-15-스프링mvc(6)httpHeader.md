---
layout: post
title: 2020-7-15 스프링 MVC(6) HTTP 헤더와 매개변수
comments: true
---

### 헤더와 매개변수

- Spring에서 이미 정의한 HttpHeaders를 이용

- 특정한 헤더가 있는 요청을 처리하고 싶을때 header="key"

  - ```java
    @Controller
    @RequestMapping(value = "/hello")
    public class SampleController {

        @GetMapping(value = "/jimmy", headers = HttpHeaders.ACCEPT_LANGUAGE)
        @ResponseBody
        public String test() {
            return "hello jimmy";
        }
    }

     @Test
    public void pathTest() throws Exception {
      mockMvc.perform(get("/hello/jimmy")
                      .header(HttpHeaders.FROM, "localhost"))
        .andDo(print())
        .andExpect(status().isOk())
        .andExpect(content().string("hello jimmy"))
        .andExpect(handler().methodName("test"));
    }
    ```

  -

- 특정한 헤더가 없는 요청을 처리할때 header="!key"

- 특정한 키-값이 있는경우 header="key=value"

- 특정한 요청 매개변수가 있는 요청 params="a" 없는 매개변수 params="!a"

- 특정한 요청 매개변수 키-값을 가진경우 params="a=b"

  - ```java
    @Controller
    @RequestMapping(value = "/hello")
    public class SampleController {

        @GetMapping(value = "/jimmy", params = "name=spring")
        @ResponseBody
        public String test() {
            return "hello jimmy";
        }
    }

    @RunWith(SpringRunner.class)
    @WebMvcTest
    public class SampleControllerTest {
        @Autowired
        private MockMvc mockMvc;
        @Test
        public void pathTest() throws Exception {
            mockMvc.perform(get("/hello/jimmy")
                    .param("name", "jimmy"))
                    .andDo(print())
                    .andExpect(status().isOk())
                    .andExpect(content().string("hello jimmy"))
                    .andExpect(handler().methodName("test"));
        }
    }

    ```

  - 컨트롤러에 정의된 Header요청에 대해서 처리. 다만 몇몇헤더들은 일치하지 않더라도 통과가 된다.



### Head Options요청처리

- 우리가 구현안해도 MVC에서 알아서 처리. Head Options

- HEAD

  - GET요청과 동일하지만 응답본문을 받지 않고 응답헤더만 받는다.

  - ```java
    @Test
    public void headTest() throws Exception {
      mockMvc.perform(head("/hello/jimmy"))
        .andDo(print())
        .andExpect(status().isOk());
    }
    ```

  - GET요청만 구현되있으면 된다.

- OPTIONS

  - 사용할 수 있는 HTTP Method제공

  - 서버 또는 특정 리스소가 제공하는 기능을 확인할 수 있다.

  - ```java
    @Controller
    @RequestMapping(value = "/hello")
    public class SampleController {

        @GetMapping("/jimmy")
        @ResponseBody
        public String test() {
            return "hello jimmy";
        }

        @PostMapping("/jimmy")
        @ResponseBody
        public String helloPost() {
            return "hello jimmy";
        }

        @PatchMapping("jimmy")
        @ResponseBody
        public String helloPatch() {
            return "hello jimmy";
        }
    }

    @Test
    public void optionsTest() throws Exception {
      mockMvc.perform(options("/hello/jimmy"))
        .andDo(print())
        .andExpect(status().isOk())
        .andExpect(header().stringValues(HttpHeaders.ALLOW, hasItems(
          containsString("GET"),
          containsString("POST"),
          containsString("PATCH"),
          containsString("OPTIONS"),
          containsString("HEAD")
        )));
    }

    //response
    Headers = [Allow:"GET,HEAD,POST,PATCH,OPTIONS"]
    ```

  - 서버는 Allow응답헤더에 사용할 수 있는 HTTP Method목록을 제공해야한다.



### 커스텀어노테이션

- 다른 어노테이션을 조합한 어노테이션을 composed annotation이라 한다.

- 어노테이션 위에 쓸 수 있는 어노테이션을 메타어노테이션이라 한다.

- RequestMapping을 메타어노테이션으로 사용하기 => @GetMapping같은 커스텀 어노테이션을 만들 수 있다.

- @Retention : 기본값은 class

  - 해당 어노테이션을 언제까지 유지할지 결정
  - Source: 소스코드까지만 유지. 컴파일하면 사라진다
  - Class : 컴파일한 .class파일에서도 유지. 클래스를 메모리로 읽어오면 사라진다.
  - Runtime : 클래스를 메모리에 읽어왔을때도 유지.

- @Target : 어디에서 어노테이션을 사용할지

- @Documented : 문서에 어노테이션 정보를 표시할지

  - ```java
    @Controller
    public class SampleController {

        @GetHelloMapping
        @ResponseBody
        public String test() {
            return "hello jimmy";
        }
    }

    @Retention(RetentionPolicy.RUNTIME)
    @RequestMapping(method = RequestMethod.GET, value = "/hello")
    public @interface GetHelloMapping {
    }

    @Test
    public void 어노테이션테스트() throws Exception {
      mockMvc.perform(get("/hello"))
        .andDo(print())
        .andExpect(status().isOk());
    }
    ```

#
