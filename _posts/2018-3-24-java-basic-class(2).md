---
layout: post
title: 2018-3-24 TIL JAVA Basic Class(2)
---

### BigInteger

```
package yoon;

import java.math.BigInteger;

public class SoBigInteger {
    public static void main(String [] args) {
        // long형으로 표현 가능한 값의 크기 출력
        System.out.println("최대 정수 : " + Long.MAX_VALUE);
        System.out.println("최소 정수 : " + Long.MIN_VALUE);
        System.out.println();

        // 매우 큰 수를 BigInteger인스턴스로 표현
        BigInteger big1 = new BigInteger("100000000000");
        BigInteger big2 = new BigInteger("999999999999");

        //BigInteger기반 덧셈
        BigInteger r1 = big1.add(big2);
        System.out.println("덧셈 결과 : " + r1);

        //BigInteger곱셈
        BigInteger r2 = big1.multiply(big1);
        System.out.println("곱셈 결과 : " + r2);
        System.out.println();

        // 인스턴스에 저장된 값을 int형 정수로 변환
        int num = r1.intValueExact();
        System.out.println("From BigInteger : " + num);
    }
}
//result
최대 정수 : 9223372036854775807
최소 정수 : -9223372036854775808

덧셈 결과 : 1099999999999
곱셈 결과 : 10000000000000000000000
```
- BigInteger를 통해 큰수를 표현한다

- 덧셈 : public BigInteger add(BigInteger val)
- 뺄샘 : public BigInteger subtract(BigInteger val)
- 곱셈 : public BigInteger multiply(BigInteger val)
- 나눗셈의 몫 : public BigInteger divide(BigInteger val)
- 나눗셈의 나머지 : public BigInteger remainder(BigInteger val)

### BigDecimal

```
package yoon;

public class DoubleError {
    public static void main(String [] args) {
        double d1 = 1.5;
        double d2 = 0.1;
        System.out.println("덧셈 결과 : " + (d1 + d2));
        System.out.println("곱셈 결과 : " + (d1 * d2));
    }
}

//result
덧셈 결과 : 1.6
곱셈 결과 : 0.15000000000000002

Process finished with exit code 0

```
- 실수 표현에는 오차가 존재한다. BigDecimal을 통해서 오차를 제거한다.

```
package yoon;

public class WowBigDecimal {
    public static void main(String [] args) {
        WowBigDecimal d1 = new WowBigDecimal("1.6");
        WowBigDecimal d2 = new WowBigDecimal("0.1");
        System.out.println("덧셈 결과 : " + d1.add(d2));
    }
}
//result
덧셈 결과 : 1.7
곱셈 결과 : 0.16
```

### Math클래스와 난수의 생성

- Math클래스에 정의된 모든 메소드는 다 static으로 되어있다. 그러므로 Math클래스는 인스턴스의 생성이 목적으로 정의된 클래스가 아니다.

![](/Users/jaeyeonkim/Desktop/mathClass.png)


#### 기본적인 수학연산
```
package yoon;

public class SimpleMathUse {
    public static void main(String [] args) {
        System.out.println("원주율 : " + Math.PI);
        System.out.println("2의 제곱근 : " + Math.sqrt(2));
        System.out.println();
        System.out.println("파이에 대한 Degree : " + Math.toDegrees(Math.PI));
        System.out.println("2 파이에 대한 Degree : " + Math.toDegrees(2.0 * Math.PI));
        System.out.println();

        double radian45 = Math.toRadians(45); // 라디안으로 변환
        System.out.println("sin 45 : " + Math.sin(radian45));
        System.out.println("cos 45 : " + Math.cos(radian45));
        System.out.println("tan 45 : " + Math.tan(radian45));
        System.out.println();
        System.out.println("log 24 : " + Math.log(25));
        System.out.println("2의 16승 : " + Math.pow(2,16));
    }
}
//reuslt
원주율 : 3.141592653589793
2의 제곱근 : 1.4142135623730951

파이에 대한 Degree : 180.0
2 파이에 대한 Degree : 360.0

sin 45 : 0.7071067811865475
cos 45 : 0.7071067811865476
tan 45 : 0.9999999999999999

log 24 : 3.2188758248682006
2의 16승 : 65536.0
```

#### RandomNumber의 생성

- java.util.Random클래스를 통해 난수를 제공하고있다.

- public boolean nextBoolean() : boolean형 난수 발생
- public int nextInt() : int형 난수
- public long nextLoing() : long형 난수
- public int nextInt(int bount) : 0 이상 bound 미만 범위의 int형 반환
- public float nextFloat() : 0.0이상 1.0미만의 float형 난수 반환
- public double nextDouble() : 0.0이상 1.0미만의 double형 난수 반환

```
package yoon;

import java.util.Random;

public class RandomNumberGenerator {
    public static void main(String [] args) {
        Random rnd = new Random();

        for(int i = 0; i < 7; i++)
            System.out.println(rnd.nextInt(1000)); //0이상 1000미만의 난수생성

    }
}
//result
486
68
437
652
364
956
855
```

#### seed기반의 난수 생성

- 컴퓨터는 알고리즘을 기반으로 동작하기때문에 난수를 생성할때도 패턴이 존재를 한다. 그래서 컴퓨터가 생성하는 난수를 'Pseudo-random number(가짜난수)'라고한다.
```
package yoon;

import java.util.Random;

public class PseudoRandom {
    public static void main(String [] args) {
        Random rnd = new Random(12);

        for(int i = 0; i < 7; i++)
            System.out.println(rnd.nextInt(1000));
    }
}
//result
866
812
556
133
624
211
750
```
- Random rnd = new Random(12)에서 12는 난수의 생성과정에서 씨앗으로 사용이된다. 이를 씨드값이라고 한다. 난수생성알고리즘이 씨드값을 기반으로 돌아가기때문에 이 값이 같다면 생성되는 난수의 패턴은 100%일치를 한다.

```
package yoon;

import java.util.Random;

public class SeedSetRandom {
    public static void main(String [] args) {
        Random rnd = new Random(System.currentTimeMillis());

        for(int i = 0; i < 7; i++)
            System.out.println(rnd.nextInt(1000));
    }
}
//result
498
936
322
265
340
186
420
```
-  Random rnd = new Random(System.currentTimeMillis()); 여기의 메소드 호출문은, 컴퓨터의 현재 시간을 기준으로 1970년 1월 1일 자정이후로 지나온 시간을 계산하여 반환을 한다. 따라서 예제를 실행할때마다 seed값이 달라지므로 실행을 할때마다 random값이 달라진다.

- Random rand = new Random()의 경우 seed값을 전달하지 않는다. 하지만 내부적으로는 밑의 코드와 같다.

```
public Random() {
	this(System.currentTimeMillis()); // Random(long seed)생성자 호출
}
```

### 문자열 토큰의 구분

- "PM:08:45" => "PM,08,45"
- 오른쪽의 3가지 정보는 :(콜론)을 기준으로 나누어져 있다. 콜론을 '구분자(Delimiter)'라고 한다. 구분자를 기준으로 나뉜 문자열 조각은 '토큰(Token)'이라고한다.
- public StringTokenizer(String str, String delim)을 통해서 문자열을 쉽게 나눌 수 있다.

```
package yoon;

import java.util.StringTokenizer;

public class TokenizeString {
    public static void main(String [] args) {
        StringTokenizer st1 = new StringTokenizer("PM:08:45", ":");

        while (st1.hasMoreTokens())
            System.out.print(st1.nextToken() + " ");
        System.out.println();

        StringTokenizer st2 = new StringTokenizer("12 + 36 - 8 / 2 = 44", "+-/= ");

        while (st2.hasMoreTokens())
            System.out.print(st2.nextToken() + " ");
        System.out.println();
    }
}
//result
PM 08 45
12 36 8 2 44
```
- 토큰을 반환하는 메소드는 nextToken()이다. 하지만 토큰이 없으면 예외가 발생하므로 hasMoreTokens메소드 호출을 통해서 토큰이 있는지 확인을 먼저 해야한다.
