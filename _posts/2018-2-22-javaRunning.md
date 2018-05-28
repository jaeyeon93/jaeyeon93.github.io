---
layout: post
title: 2018-2-22 TIL Java Running
---

#### 자바가 실행되는 순서
1. 자바코드 작성 ex) hello.java
2. 컴파일(내가 만든 프로그램 코드를 컴퓨터가 이해할 수 있도록 바꿔주는 작업)이다. ex) javac hello
3. 실행 ex) java hello
- .java라는 확장자로 되어 있는 소스를 컴파일하면 .class라는 확장자를 가진 파일이 생성이 된다. .class라는 파일을 실행시키는것이다.
- 컴파일은 javac라는 명령어로 하지만, 실행은 java라는 명령어로 한다.

- 실행을 목적으로 하는 모든 자바 클래스는 main() 메소드가 반드시 있어야한다. 자바 프로그램의 시작점은 main()메소드이기때문이다.

```
// declare java main method
public static void main(String args[]) {

}
```
#### 위 예제코드 해석
- public : 접근제어자
- static : 메소드를 static선언하면 객체를 생성하지 않아도 바로 호출을 할 수 있다.
- void : 메소드가 어떤 값을 넘겨줄지 정하는데, void는 리턴값이 없을 때, void를 쓴다.
- main : 메소드이름이다.
- (String args[]) : 메소드 이름 옆에 소괄호 안에 있는 것들을 매개변수(parameter)라고 한다. String args라는 변수가 배열이라는 말이다. args는 변수 이름이다.

#### helloworld 찍어보기

```
public class HelloWorld {
	public static void main (String args[]) {
    	System.out.println("Hello World");
    }
}

> javac HelloWorld
> java HelloWorld
> Hello World 출력
```

#### 메소드를 만들어보자

```
public class Calculator {
	public static void main (String [] args) {
    	System.out.println("Calculator class started");
    }
}

```
1. public static : 제어자. 메소드의 특성을 정하는 부분이다.
2. void : 메소드가 끝났을 때 돌려주는 타입
3. main : 메소드 이름
4. (String [] args) : 매개변수 목록
5. System.out.println("Calculator class started"); : 메소드 내용
