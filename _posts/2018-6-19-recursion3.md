---
layout: post
title: 2018-6-19 Recursion (3)
---

Recursion - 세번째
---

- 적어도 하나의 base case가 있어야된다. 즉 순환되지 않고 종료되는 case가 있어야한다.
- 모든 case는 결국 base case로 수렴해야 한다.

##### 암시적(implicit) 매개변수를 명시적(explicit) 매개변수로 바꿔라

```
// 순차 탐색
public class Recursion8 {
    public static void main(String[] args) {
        int [] data = {1, 3, 5, 6, 7, 9};
        int n = data.length;
        int target = 9;
        System.out.println(search(data, n, target));
    }

    public static int search(int [] data, int n, int tartget) {
        for (int i = 0; i < n; i++)
            if (data[i] == tartget)
                return i;
        return -1;
    }
}
//result
5
```
- 이 함수에서 data[0]에서 data[n-1] 사이의 target을 검색하는 것이다. 검색 구간의 시작 인덱스 0은 보통 생략한다. 즉 암시적 매개변수이다.

```
// 순차탐색2
public class Recursion9 {
    public static void main(String[] args) {
        int [] data = {1,2,3,4,5,6,7,8,9,10};
        System.out.println("result : " + search(data, 0, data.length, 6));
    }

    public static int search(int [] data, int begin, int end, int target) {
        // begin == end는 데이터가 1개라는 의, begin > end라면 데이터의 갯수가 0개
        if (begin > end)
            return -1;
        else if (target == data[begin])
            return begin;
        return search(data, begin + 1, end, target);
    }
}
//result
result : 5
```
- end는 보통 data.length로 한다.

#### 순차탐색 중간에서부터 검색

```
public static int search2(int [] data, int begin, int end, int target) {
        if (begin > end)
            return -1;
        int middle = (begin + end) / 2;
        if (data[middle] == target)
            return middle;
        int index = search2(data, begin, middle - 1, target);
        if (index != -1)
            return index;
        return search2(data, middle + 1, end, target);
    }
```
1. middle을 설정해서 만약에 data[middle] == target이면, middle을 리턴

#### 최대값 찾기

```
 public static int findMax(int [] data, int begin, int end) {
        if (begin == end)
            return data[begin];
        return Math.max(data[begin], findMax(data, begin + 1, end));
        // findMax(data, begin + 1, end) : 데이터의 구간이 계속 변한다.
    }

    public static int findMax2(int [] data, int begin, int end) {
        if (begin == end)
            return data[begin];
        int middle = (begin + end) / 2;
        int max1 = findMax(data, begin, middle);
        int max2 = findMax(data, middle + 1, end);
        return Math.max(max1, max2);
    }
```

#### 2진탐색

```

public class Recursion11_BinarySearch {
    public static int binarySearch(String [] items, String target, int begin, int end) {
        if (begin == end)
            return -1;
        int middle = (begin + end) / 2;
        int compResult = target.compareTo(items[middle]);
        if (compResult == 0)
            return middle;
        else if (compResult < 0)
            return binarySearch(items, target, begin, middle - 1);
        return binarySearch(items, target, middle + 1, end);
    }
}

```
- 보통 begin은 0, end는 data.length - 1로 한다.
- begin > end, 데이터의 갯수가 0인경우
- compareTo의 경우 같으면 0을 리턴, 작으면 -1, 크면 양수를 리턴한다.
- data가 크기순서대로 저장이 되어있기때문에, 만약에 compareTo가 음수를 리턴한다면 찾는데이터의 위치가 middle보다 작으ㅡ로 begin에서부터 middle-1까지를 탐색한다.
- 역으로 compareTo가 양수를 리턴하면, 찾는 데이터의 크기가 middle보다 크므로, middle+1에서부터 end까지를 탐색한다.
