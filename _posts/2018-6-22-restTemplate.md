---
layout: post
title: 2018-6-22 RestTemplate
---

### RestTemplate

- test RestTemplate의 역할은 브라우저의 역할을 수행하고 있다. UI가 없는 상태로 클라이언트의 역할을 한다.
- UI가 없는 상태에서, 클라이언트에서 서버로 요청을 보낼 수 있다. 그러므로 서버에서는 요청에 대한 유효성을 늘 체크를 해야 한다.
- 유효성체크는 백엔드에서는 필수, 클라이언트에서는 약간의 옵션?
- TestRestTemplate은 RestTemplate을 상속하고 있다.
- 서버가 언제든지 클라이언트의 역할로도 바뀔 수 가 있다. 서버가 다른 서버에게 요청을 하는 순간, 요청을 보내는 서버는 클라이언트가 된다. RestTemplate으로 url, path를 key-value형태로 GET, POST, PUT의 형태로 보내면 된다.


### Interceptor
- Interceptor는 BasicAuth에서 현재 사용을 하고 있다.
- 성능측정을 위한 각 메서드의 실행부터 종료까지의 시간을 구하고 싶을때 사용.

```
public class BasicAuthInterceptor extends HandlerInterceptorAdapter {
    private static final Logger log = LoggerFactory.getLogger(BasicAuthInterceptor.class);

    @Autowired
    private UserService userService;

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
            throws Exception {
        String authorization = request.getHeader("Authorization");
        log.debug("Authorization : {}", authorization);
        if (authorization == null || !authorization.startsWith("Basic")) {
            return true;
        }

        String base64Credentials = authorization.substring("Basic".length()).trim();
        String credentials = new String(Base64.getDecoder().decode(base64Credentials), Charset.forName("UTF-8"));
        final String[] values = credentials.split(":", 2);
        log.debug("username : {}", values[0]);
        log.debug("password : {}", values[1]);

        try {
            User user = userService.login(values[0], values[1]);
            log.debug("Login Success : {}", user);
            request.getSession().setAttribute(HttpSessionUtils.USER_SESSION_KEY, user);
            return true;
        } catch (UnAuthenticationException e) {
            return true;
        }
    }
}
```
- prehandler메소드는 메소드가 시작전에 호출이 된다.
