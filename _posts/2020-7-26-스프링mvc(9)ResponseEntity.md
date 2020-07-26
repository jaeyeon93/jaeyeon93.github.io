---
layout: post
title: 2020-7-26 스프링 MVC(9) ResponseEntity
comments: true
---

# ResponseEntity

### 파일 다운로드

- 파일 리소스 읽는 방법 : 스프링 ResourceLoader사용하기
- 파일 다운로드 응답 헤더에 설정할 내용
  - Content-Disposition : 사용자가 해당 파일을 받을 때 사용할 파일 이름
  - Content-Type : 어떤 파일인가
  - Content-Length : 얼마나 큰 파일인가
- ResponseEntity
  - 응답 상태코드
  - 응답 헤더
  - 응답 본문



### @RequestBody & HttpEntity

@RequestBody

- 요청본문(body)에 들어있는 데이터를 를  HttpMessageConverter를 통해 변환한 객체로 받아올 수 있다.

- @Valid, @Validated를 통해 값을 검증할 수 있다.

- BindingResult아규먼트를 통해 코드로 바인딩 또는 검증에러를 확인할 수 있다.

- ```java
  @RestController
  @RequestMapping("/api/events")
  public class EventApi {

    @PostMapping
    public  Event createEvent(@RequestBody Event event) {
      // save
      return event;
    }
  }

  @RunWith(SpringRunner.class)
  @SpringBootTest
  @AutoConfigureMockMvc
  public class EventApiTest {

    @Autowired
    private MockMvc mockMvc;

    @Autowired
    private ObjectMapper objectMapper;

    @Test
    public void createEvent() throws Exception {
      Event event = new Event();
      event.setName("api event");
      event.setLimit(100);

      String json = objectMapper.writeValueAsString(event);


      mockMvc.perform(post("/api/events")
                      .contentType(MediaType.APPLICATION_JSON)
                      .content(json))
        .andDo(print())
        .andExpect(status().isOk())
        .andExpect(jsonPath("name").value("api event"))
        .andExpect(jsonPath("limit").value(100));
    }
  }

  @RestController
  @RequestMapping("/api/events")
  public class EventApi {
    @PostMapping
    public  Event createEvent(@RequestBody @Valid Event event, BindingResult bindingResult) {
      if (bindingResult.hasErrors())
        bindingResult.getAllErrors().forEach(System.out::println);
      return event;
    }
  }
  ```

  - 요청이 올때 HttpMessageConverter를 통해 Event객체로 바꿔준다.
  - @EnableWebMvc를 Configuration을 추가.
  - HandlerAdaptor가 메세지 컨버터들을 사용한다.
  - mock의 content()가 http body를 의미

HttpMessageConverter

- 스프링MVC 설정 (WebMvcConfigurer)에서 설정할 수 있다.
- configureMessageConverters: 기본 메세지 컨버터 대체
- extendMessageConverters: 메세지 컨버터 추가
- 기본 컨버터
  - WebMvcConfigurationSupport.addDefaultHttpMessageConverters

HttpEntity

- @RequestBody와 비슷하지만 추가적으로 요청 헤더 정보를 사용할 수 있다.

  - ```java
    @RestController
    @RequestMapping("/api/events")
    public class EventApi {

      @PostMapping
      public  Event createEvent(HttpEntity<Event> request) {
        MediaType contentType = request.getHeaders().getContentType();
        System.out.println(contentType);
        return request.getBody();
      }
    }

    ```

  - @RequestBody에는 헤더정보가 없어서 HttpEntity\<T>를 이용해서 요청에 해당하는 헤더와 바디정보를 함께 가져올 수 있다.

### ResponseBody, ResponseEntity

ResponseBody

- ResponseBody는 메서드레벨에서 붙인다
- 데이터를 HttpMessageConverter를 사용해 응답 본문 메세지로 보낼때 사용
- @RestController 사용시 자동으로 모든 핸들러 메서드에 적용된다.

ResponseEntity

- 응답 헤더 상태 코드 본문을 직접 다루고 싶은 경우에 사용한다.

  - ```java
    @Controller
    @RequestMapping("/api/events")
    public class EventApi {

      @PostMapping
      public ResponseEntity<Event> createEvent(@RequestBody @Valid Event event, BindingResult bindingResult) {
        if (bindingResult.hasErrors())
          return ResponseEntity.badRequest().build();
        return ResponseEntity.ok(event);
      }
    }

    ```



### @ModelAttributes 또 다른 사용법

- RequestMapping을 사용한 핸들러 메서드의 아규먼트에 사용하기

- @Controller , @ControllerAdvice를 사용한 클래스에서 모델 정보를 초기화할 때 사용한다.

- @RequestMapping과 같이 사용한다면 해당 메서드에서 리턴하는 객체를 모델에 넣어준다.

  - RequestToViewNameTranslator를 통해 ViewName을 전달

- @ModelAttributes메서드

  - ```java
    @ModelAttribute
    public void categories(Model model) {
      model.addAttribute("categories", Arrays.asList("study", "seminar"));
    }
    // 2nd
    @ModelAttribute("categories")
    public List<String> categories(Model model) {
      return Arrays.asList("study", "seminar");
    }
    //test message
    ModelAndView:
    View name = /events/list
      View = null
      Attribute = newEvent
      value = me.jimmy.springmvc.Event@7fdab70c
      errors = []
      Attribute = categories
      value = [study, seminar]
      Attribute = eventList
      value = [me.jimmy.springmvc.Event@25ad4f71]

    ```

  - 기본 카테고리 정보를 모든 뷰에서 참조할 수 있게된다. 테스트메세지를 보면 ModelAndView의 value에 값이 들어있는 것을 확인할 수 있다.



### Databinder : @InitBinder

- 특정 컨트롤러에서 바인딩 또는 검증 설정을 변경하고 싶을 때 사용

- @InitBinder("name")을 주면 name에 해당하는 Model에 대해서만 적용된다.

- ```java
  @InitBinder
  public void initEventBinder(WebDataBinder webDataBinder) {
    webDataBinder.setDisallowedFields("id");
  }
  ```

- 바인딩 설정 : webDataBinder.setDisallowedFileds(); => 받고 싶지 않은 필드를 필터링할 수 있다.

  - 폼으로 입력을 받아도 값에 설정한

- 포매터설정 : webDataBinder.addCustomFormatter();

- Validator설정 : webDataBinder.addValidators();

  - ```java
    public class EventValidator implements Validator {
      @Override
      public boolean supports(Class<?> clazz) {
        return Event.class.isAssignableFrom(clazz);
      }

      @Override
      public void validate(Object target, Errors errors) {
        Event event = (Event)target;
        if (event.getName().equalsIgnoreCase("aaa"))
          errors.rejectValue("name", "wrongValue", "the value is not allowed");
      }
    }

    @Controller
    @SessionAttributes("event")
    public class EventController {

      @InitBinder
      public void initEventBinder(WebDataBinder webDataBinder) {
        webDataBinder.setDisallowedFields("id");
        webDataBinder.addValidators(new EventValidator());
      }
    }
    ```

  - custom한 Validator를 등록한다.

  - Validators를 구현하면 커스텀한 검증을 할 수 있다. **빈으로도 등록할 수 있다.** 대신 명시적으로 검증해야한다.

- 특정 모델객체만 바인딩 또는 Validator 설정을 적용하고 싶을때 : @InitBinder("event")
