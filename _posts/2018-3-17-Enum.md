---
layout: post
title: 2018-3-17 TIL Java enum
---

## Enum

- 열거형의 목적은 '의미가 부여된 이름'을 갖는 '상수'의 선언이다.

```
enum MyEnum { // 열거자료형 MyEnum의 정의
	DO, RE, MI, FA, SO, RA, SI
}
```
- 안에 위치한 이름들을 가리켜 열거형값이라고한다.

- 열거형은 참조변수 선언도 가능하다. 단 선언된 참조변수는 해당 열거형 내에 선언된 '열거형 값'만 대입이 가능하다.
- MyEnum me = MyEnum.DO;


```
package yoon;

enum Scale {
    DO, RE, MI, FA, SO, RA, TI
}

public class SimpleEnum {
    public static void main(String [] args) {
        Scale sc = Scale.DO;
        System.out.println(sc);

        switch (sc) {
            case DO:
                System.out.println("도~");
                break;
            case RE:
                System.out.println("레~");
                break;
            case MI:
                System.out.println("미~");
                break;
            case FA:
                System.out.println("파~");
                break;
            default:
                System.out.println("솔~ 라~ 시~");
                break;
        }
    }
}
//result
DO
도~
```

#### 클래스 내에 정의가 가능한 열거형

```
class Customer {
	enum Gender {
    	MALE, FEMALE
    }
    ...
    Gener gen;
    ...
}
```

```
package yoon;

class Customer {
    enum Gender {
        MALE, FEMALE
    }

    private String name;
    private Gender gen;

    Customer(String n, String g) {
        name = n;

        if(g.equals("man"))
            gen = Gender.MALE;
        else
            gen = Gender.FEMALE;
    }

    @Override
    public String toString() {
        if(gen == Gender.MALE)
            return "Thank you, Mr " + name;
        else
            return "Thank you, Mrs " + name;
    }
}

public class InnerEnum {
    public static void main(String [] args) {
        Customer cus1 = new Customer("Brwon", "man");
        Customer cus2 = new Customer("Susan Hill", "woman");

        System.out.println(cus1);
        System.out.println(cus2);
    }
}
//result
Thank you, Mr Brwon
Thank you, Mrs Susan Hill
```

```
package yoon;

enum Person2 {
    MAN(29);

    int age;
    private Person2(int age) {
        this.age = age;
    }
    @Override
    public String toString() {
        return "I am " + age + " years old";
    }
}

public class EnumParamConstructor {
    public static void main(String [] args) {
        System.out.println(Person2.MAN);
    }
}
//result
I am 29 years old
```

- 열거형의 생성자는 무조건 private로 선언한다
- 소괄호를 통해 생성자에 인자전달이 가능하다.
