---
layout: post
title: 2018-8-8 포비강의정리
---

#### 오늘의 할 주제
- DI와 Spring DI
- Suvelet Container / Spring(DI or Bean) Container


#### Servelet은 HTTP를 좀 더 사용하기 편하도록 만든것이다. 이것을 좀 더 추상화를 한 것이 Srping MVC이다.

- 스프링은 servelet Container위에서 동작을 하고 있다.
- SpringMVC는 기본적으로


#### 컨테이너란 무엇일까?
- 서블릿은 SUN사에서 제안한 웹서비스를 위한 인터페이스로, 원칙적으로는 javax.servlet.Servlet 인터페이스의 구현체입니다. 일반적인 자바 독립 실행프로그램과 달리 main 메서드가 없으며, 서블릿 컨테이너에 등록된 후 서블릿 컨테이너에 의해 생성, 호출, 소멸이 이루어집니다.
- 서블릿 컨테이너는 이러한 서블릿을 관리하며 네크워크 통신, 서블릿의 생명주기 관리, 스레드 기반의 병렬처리를 대행합니다. 즉, 웹 클라이언트로 부터 HTTP 요청이 전달되면 해당 HTTP 요청을 해석하여 적정한 서블릿의 service 메서드를 ServletRequet, ServletResponse 매개변수와 함께 호출합니다.
- 핵심은 라이프사이클이다.
- 라이프사이클을 관리를 하는것이 핵심이다.
- SpringBean의 라이프사이클을 관리를 하는것이, Spring Container의 역할이다.

#### 라이프사이클이란?

- 객체의 생성부터 소멸까지 생명주기를 관리를 해준다.
- 컨테이너가 없었을때는, 개발자들이 직접 코딩을 통해 객체들을 관리를 다 했다. 컨테이너기반으로 개발을 하면 라이프사이클의 주체가 컨테이너가 된다.

```
@Resource(name = "userService")
    private UserService userService;
```
- 위의 경우 직접 인스턴스를 생성을 한적이 없다. 컨테이너가 관리를 하나?

#### 컨테이너의 장단점

- 우선 객체를 봤을때, 상태값을 가지는지 안가지는지를 확인을 해야한다. 인스턴스변수를 가지고 있으면 상태를 가지고 있다고 주로 판단을 한다.
- 상태를 가지지 않는 객체와 의존관계를 가지면, 그 해당객체 역시 상태가 없다고 볼 수 있다.
- 상태를 가지지 않는 객체는 싱글턴패턴으로 인스턴스를 만들어서 사용을 하는게, 성능상 좋다.
- 의존관계에 있는 객체들을 서로 연결을 해주는 것이 Dependancy Injection이라고 한다.
- 하나라도 상태가 있으면 엄청난 장애가 발생을 한다.
- 의존관계에 있는 모든 객체에 대해서 꼬리에 꼬리를 물어서 상태의 유무를 확인해야한다.
- 상태값을 가지고 있는 객체를 상속을 받으면, 상위 클래스에 있는 상태를 함께 가지게 된다.

#### 서브릿의 인스턴스는 몇 개 인가?
1. 컨테이너가 기본적으로 관리를 하는 인스턴스는 1개이다.
2. 서브릿도 상태를 가지는 객체를 인스턴스로 만들면 안된다.


- @Bean이라는 어노테이션을 설정하고, 객체를 만들면 이것은 SpringContainer에 Bean으로 등록을 한다는 의미이다.
- @Service 어노티이션을 사용을 하면, @Bean처럼 객체에 인스턴스를 만들고 자동으로 컨테이너에 생성을 해준다.

#### 어떤경우에 Bean어노테이션을 써야되는가?

1. 우리가 직접 코드를 수정할 수 없는 코드들은 @Bean을 직접 입력을 해서 구현을 해야한다.
2. 우리가 직접 코드를 수정할 수 있는 부분들은 그냥 어노테이션을 사용을 하는 것이 편하다. 다만 수정할 수 없는 부분에 대해서만 @Bean어노테이션을 사용을 하자.


#### 스프링을 사용하면 DI를 통해서 개발을 하는데, 왜 DI를 사용을 하느냐? 필요성은?



그램발표
===

### 리플랙션
- 구체적인 클래스 타입을 알지 못해도 컴파일된 바이트 코드를 가지고 클래스의 정보를 알아내고 사용할 수 있는 자바 API
- 동적로딩을 지원하는 자바의 특징을 이용.
- 자바는 실행시 모든 클래스가 로딩되지않고, 필요할 때 로딩이 된다.

#### 클래스로드
- 클래스파일의 내부를 분석(링크같은처리) 후, 런타임데이터영역(메모리영역)에 배치한다.
