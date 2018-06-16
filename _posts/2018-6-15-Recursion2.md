---
layout: post
title: 2018-6-15 Recursion (2)
---

Recursion2
----

- for문을 이용하지 않고, Recursion을 이용해서 풀 수 있는 방법이 많다.

```
// 문자열의 길이구하기
public class Recursion5 {
    public static void main(String[] args) {
        int result = length("hello world");
        System.out.println("result : " + result);
    }

    public static int length(String str) {
        if (str.equals(""))
            return 0;
        return 1 + length(str.substring(1));
    }
}
//result
11
```
![](https://github.com/jaeyeon93/jaeyeon93.github.io/blob/master/images/algorithm/recursion/recursion-5.png?raw=true)

```
// 문자열 프린트
public class Recursion6 {
    public static void main(String[] args) {
        String str = "hello world";
        printChars(str);
    }

    public static void printChars(String str) {
        if (str.length() == 0)
            return;
        System.out.print(str.charAt(0));
        printChars(str.substring(1));
    }
}
//result
hello world
```
```
//배열의 합
public class Recursion7 {
    public static void main(String[] args) {
        int [] data = {1, 3, 4, 5,6};
        int result = sum(5, data);
        System.out.println("result is " + result);
    }

    public static int sum(int n, int [] data) {
        if (n <= 0)
            return 0;
        return sum(n - 1, data) + data[n - 1];
    }
}
//result
19
```

### Recursion vs Iteration
- 모든 순환함수는 반복문(Iteration)으로 변경가능하다.
- 그 역도 성립, 즉 모든 반복문은 Recursion으로 표현이 가능하다.
- 순환함수는 복잡한 알고리즘을 단순하게 표현하게 해준다. 하지만 함수 호출에 따른 오버해드가 있다(매개변수 전달)
