---
layout: post
title: 2018-6-17 Recursion (2)
---

Recursion
----

- recursion을 무한루프 안빠지게 하기

```
public class Recursion1 {
    public static void main(String[] args) {
        int n = 4;
        func(n);
    }

    public static void func(int k) {
        if (k <= 0)
            return;
        else {
            System.out.println("hello...");
            func(k - 1);
        }

    }
}
//result
hello...
hello...
hello...
hello...
```

![](https://github.com/jaeyeon93/jaeyeon93.github.io/blob/master/images/algorithm/recursion/recursion-1.png?raw=true)

- recursion은 항상 무한루프에 빠지지 않는다. recursion을 적절하게 사용을 하면 된다.

![](https://github.com/jaeyeon93/jaeyeon93.github.io/blob/master/images/algorithm/recursion/recursion-2.png?raw=true)

- recursion이 빠져나갈 수 있는 조건문을 만들어 놓아야한다. 이것을 Base case라고 한다.
- recursion을 반복을 하다보면 결국에는 base case로 수렴을 해야한다 :)

```
public class Recursion1 {
    public static void main(String[] args) {
        int result = func(4);
        System.out.println(result);
    }

    public static int func(int k) {
        if (k <= 0)
            return 0;
        else {
            return k + func(k - 1);
            // n이 0보다 크면 0에서 n까지의 합은 0에서 n -1까지의 합에 n을 더한것이다.
        }
    }
}
//result
10
```
![](https://github.com/jaeyeon93/jaeyeon93.github.io/blob/master/images/algorithm/recursion/recursion-3.png?raw=true)


```
// Factorial
public class Recursion2 {
    public static void main(String[] args) {
        int result = factorial(5);
        System.out.println(result);
    }

    public static int factorial(int n) {
        if (n == 0)
            return 1;
        return n * factorial(n - 1);
    }
}
//result
120
```
- 0! : 1

#### 피보나치
```
// Fibonacci
public class Recursion3 {
    public static void main(String[] args) {
        for (int i = 1; i < 10; i++) {
            System.out.println(fibonacci(i));
        }
    }

    public static int fibonacci(int n) {
        if (n < 2)
            return n;
        return fibonacci(n - 1) + fibonacci(n - 2);
    }
}
```

#### 최대공약수

- m >= n인 두 양의 정수 m과 n에 대해서 m이 n의 배수이면 gcd(m,n) = n이고, 그렇지 않으면 gcd(m,n) = gcd(n, m%n)이다.
-
