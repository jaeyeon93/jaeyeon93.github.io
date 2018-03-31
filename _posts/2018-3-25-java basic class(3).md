---
layout: post
title: 2018-3-25 TIL JAVA Basic(3)
---

## Arrays클래스

#### 배열의 복사

```
public static int [] copyof(int [] original, int newLength)
// original에 전달된 배열을 첫번째 요소부터 newLength길이만큼 복사

```
- 배열을 복사하여, 새로운 배열을 생성하는 메소드다.

```
package yoon;

import java.util.Arrays;

public class CopyOfArrays {
    public static void main(String [] args) {
        double [] arOrg = {1.1, 2.2, 3.3, 4.4, 5.5};

        //배열전체를복사
        double [] arCpy1 = Arrays.copyOf(arOrg, arOrg.length);

        // 세번째 요소까지만 복사
        double [] arCp2 = Arrays.copyOf(arOrg, 3);

        for(double d : arCpy1)
            System.out.println(d + "\t");
        System.out.println();
        }
    }

//result
1.1
2.2
3.3
4.4
5.5
```

```
package yoon;

public class CopyOfSystem {
    public static void main(String [] args) {
        double [] org = {1.1, 2.2, 3.3, 4.4, 5.5};
        double [] cpy = new double[3];

        // 배열 org의 인덱스1에서 배열 cpy인덱스 0으로 세개의 요소 복사
        System.arraycopy(org, 1, cpy , 0, 3);

        for(double d : cpy)
            System.out.print(d + "\t");
        System.out.println();
    }
}

//result
2.2	3.3	4.4
```

#### 배열의 비교

```
package yoon;

import java.util.Arrays;

public class ArrayEquals {
    public static void main(String [] args) {
        int [] ar1 = {1, 2, 3,4 ,5};
        int [] ar2 = Arrays.copyOf(ar1, ar1.length);
        System.out.println(Arrays.equals(ar1, ar2));
    }
}
//result
true
```
