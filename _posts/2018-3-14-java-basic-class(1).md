---
layout: post
title: 2018-3-14 TIL Java 기본클래스 (1)
---

### 래퍼 클래스(Wrapper)

- int형 정수나 double형 실수 같은 기본 자료형들의 값들도 인스턴스로 표현해야하는 경우가 있기때문에, 래퍼클래스가 필요하다


```
package yoon;

public class UseWrapperClass {
    public static void showData(Object obj) {
        System.out.println(obj);
    }

    public static void main(String [] args) {
        Integer iInst = new Integer(3); // 정수 3을 감싸는 래퍼 인스턴스 생성
        showData(iInst);
        showData(new Double(7.14)); // 7.15를 감싸는 래퍼 인스턴스 전달
    }
}
//result
3
7.14
```
#### 모든 기본 자료형을 대상으로 래퍼클래스있다.

- Boolean
- Character
- Byte
- Short
- Integer
- Long
- Float
- Double

#### 래퍼클래스의 두 가지 기능

1. 인스턴스를 감싼다(박싱)
2. 인스턴스에서 값을 꺼낸다(언박싱)

![](https://github.com/jaeyeon93/jaeyeon93.github.io/blob/master/images/wrapper1.jpeg?raw=true
)

```
package yoon;

public class BoxingUnboxing {
    public static void main(String [] args) {
        Integer iObj = new Integer(10);
        Double dObj = new Double(3.4);
        System.out.println(iObj);
        System.out.println(dObj);
        System.out.println();

        int num1 = iObj.intValue();
        double num2 = dObj.doubleValue();
        System.out.println(iObj);
        System.out.println(dObj);
        System.out.println();

        // 래퍼 인스턴스 값의 증가
        iObj = new Integer(iObj.intValue() + 10);
        dObj = new Double(dObj.doubleValue() + 1.2);
        System.out.println(iObj);
        System.out.println(dObj);
    }
}
//result
10
3.4

10
3.4

20
4.6
```

- 래퍼인스턴스들은 담고 있는 값을 **수정하지 못한다.** 수정이 필요하면 새로운 래퍼 인스턴스를 생성해야한다.

- num++; : new Integer(num.intValue() + 1);
- num += 3; : new Integer(num.intValue() + 3);
- 위와 같이 처리하는 것을 오토박싱이라고 한다.

### Number 클래스와 래퍼 클래스의 static 메소드

- 모든 래퍼클래스는 java.lang.Number를 상속한다.

1. public abstract int intValue()
2. public abstract long longValue()
3. public abstract double doubleValue()

- 위와 같은 추상클래스들이 존재

```
package yoon;

public class NumberMethod {
    public static void main(String [] args) {
        Integer num1 = new Integer(29);
        System.out.println(num1.intValue());
        System.out.println(num1.doubleValue());

        Double num2 = new Double(3.14);
        System.out.println(num2.intValue());
        System.out.println(num2.doubleValue());
    }
}
//result
29
29.0
3
3.14
```

```
package yoon;

public class WrapperClassMethod {
    public static void main(String [] args) {
        // 클래스 메소드를 통한 인스턴스 생성 방법 2가지
        Integer n1 = Integer.valueOf(5); // 숫자 기반 Integer 인스턴스 생성
        Integer n2 = Integer.valueOf("1024"); // 문자열 기반 Integer 인스턴스 생성

        System.out.println("큰 수 : " + Integer.max(n1, n2));
        System.out.println("작은 수 : " + Integer.min(n1, n2));
        System.out.println("합 : " + Integer.sum(n1, n2));
        System.out.println();

        // 2진, 8진, 16진수 표현
        System.out.println("12의 2진 표현 : " + Integer.toBinaryString(12));
        System.out.println("12의 8진 표현 : " + Integer.toOctalString(12));
        System.out.println("12의 16진 표현 : " + Integer.toHexString(12));
    }
}
//result
큰 수 : 1024
작은 수 : 5
합 : 1029

12의 2진 표현 : 1100
12의 8진 표현 : 14
12의 16진 표현 : c
```
