---
layout: post
title: 2020-9-22 LeetCode - Longest SubString without repeating
comments: true
---


# 문제 : Longest-substring-without-repeating


## 문제해설

- [문제링크](https://leetcode.com/problems/longest-substring-without-repeating-characters/)
- 문자열 s가 주어질때, 반복되는 문자가 없는 가장 긴 부분 문자열을 구해라.



### 예시

```
Input: s = "abcabcbb"
Output: 3
Explanation: The answer is "abc", with the length of 3.

Input: s = "bbbbb"
Output: 1
Explanation: The answer is "b", with the length of 1.

Input: s = "pwwkew"
Output: 3
Explanation: The answer is "wke", with the length of 3.
Notice that the answer must be a substring, "pwke" is a subsequence and not a substring.

Input: s = ""
Output: 0

Input: s = " "
Output: 1

Input: s = "au"
Output: 2
```

- 부분배열 subString에는 각각 중복이 없는 문자열들이 들어있어야한다. 만약에 중복되는 문자열이 존재할 경우, 카운팅이 안된다.

### 제약식

- 0<= s.length <= 5 * 10^4
- 입력받은 문자열 s는 알파벳과 숫자, 기호, 공백으로 이루어져 있다.



## 내가 푼 코드

```java
public static int lengthOfLongestSubstring(String s) {
  StringBuilder sb;
  int n = s.length();
  int result = 0;
  char [] arr = s.toCharArray();
  for (int i = 0; i < n; i++) {
    sb = new StringBuilder(arr[i]);
    int size = 1;
    for (int j = i+1; j+1 <= n; j++) {
      if (arr[i] != arr[j] && sb.indexOf(String.valueOf(arr[j])) == -1) {
        sb.append(arr[j]);
        size++;
      } else {
        break;
      }
    }
    if (size > result) result = size;
  }
  return result;
}
```

- 처음에는 매우 지저분한 방식으로 풀었다. String.charAt(index)를 이용해서 접근할 수도 있었지만 그보다는 배열의 인덱스로 접근하는게 좋을거 같아서 입력받은 문자열 s를 char [] 형태로 변경했다.

### 풀이

- 완전탐색을 이용해서 접근해보았다. 입력받은 문자열의 최대길이가 5만이지만, 시간복잡도는 O(N^2)이라고 생각했지만, 밑에 해설을 보니 O(N^3)이였다. 이 부분은 다시 공부해야겠다.
- 기본적으로 탐색은 i번째 인덱스로부터 시작해서 j=i+1을 n까지 순차적으로 순회를 한다.
- 첫번째 for문이 시작될때는 만들어지는 subString을 초기화해야되기때문에 StringBuilder를 초기화하고 i번째 인덱스를 넣는다.
- 두번째 for문에서 arr[i]와 arr[j]가 다르고, StringBuiler에 arr[j]가 포함되어 있지 않으면 StringBuiler에 요소를 추가시키고 subString의 size를 증가시킨다.
- 위의 조건문이 맞지 않을 경우 바로 break;로 for문 순회를 중지시키고 size와 결과가 저장되있는 result의 길이를 비교한다.



## Leetcode에서 제안한 2가지 방법

### 풀이1 브루드포스

- 모든 subString에 대하여 중복되는 값이 있는지 없는지 체크한다.
- boolean allUnique(String substring)이라는 메서드를 만들고, 모든 subString이 unique일때 true를 리턴한다. 만약에 true를 반환하는 경우, 결과값을 업데이트한다.

```java
public class Solution {
  public static int leetCode1(String s) {
    int n = s.length();
    int ans = 0;
    for (int i = 0; i < n; i++)
      for (int j = i+1; j <= n; j++)
        if (allUnique(s, i, j)) ans = Math.max(ans, j-i);
    return ans;
  }

  public static boolean allUnique(String s, int start, int end) {
    Set<Character> set = new HashSet<>();
    for (int i = start; i < end; i++) {
      Character ch = s.charAt(i);
      if (set.contains(ch)) return false;
      set.add(ch);
    }
    return true;
  }
}
```

#### 시간복잡도 분석

- [i, j)까지 subString이 unique인지 체크해야되기때문에 시간복잡도는 N^3으로 볼 수 있다.
- 공간복잡도는 O(k)로 k는 Set의 사이즈



### 풀이2 슬라이싱 윈도우

- 개요
  - 슬라이싱 윈도우는 주로 array/string 문제들에서 공통적으로 사용된다. 여기서 window는 start부터 end-1까지 범위에 있는 element를 말한다.
  - 슬라이싱 윈도우는 결국, 윈도우가 2개의 범위안에서 일정한 범위로 미끄러지는걸 이야기한다.
  - slide[i, j)가 right방향으로 1씩 이동하면 [i+1, j+1)과 같이 일정한 방향으로 양쪽이 다 이동하는걸 말한다.

- 우리는 반복해서 subString이 중복됬는지 확인한다. 하지만 이 부분은 필요하지가 않다. 만약 인덱스 i부터 j-1까지의 subString이 이미 중복이 없다고 체크됬으면 우리는 s[j]가 subString에 있는지 없는지만 체크하면 된다.
-  이렇게하면 시간복잡도는 O(N^2)까지 줄일 수 있다.
- HashSet을 슬라이싱 윈도우에 사용한다면 check하는데 O(1)만에 가능하다.

```java
public static int leetCode2(String s) {
  int n = s.length();
  Set<Character> set = new HashSet<>();
  int ans = 0, i = 0, j = 0;
  while (i < n && j < n) {
    if (!set.contains(s.charAt(j))) {
      set.add(s.charAt(j++));
      ans = Math.max(ans, j-i);
    } else {
      set.remove(s.charAt(i++));
    }
  }
  return ans;
}
```

- i와 j의 시작값은 같다.
- HashSet에는 현재의 window를 담고있고, j를 중복이 없을때까지 오른쪽 방향으로 슬라이싱할 예정이다. 만약에 s[j]가 set에 있다면, j-i 까지의 최대 subString의 값을 저장한다.
- 시간복잡도 : O(2n) = O(n). 최악의 경우 i,j가 각각의 문자열을 2번씩 방문하는 경우.



### 느낀점

- 아직까지 완전히 반복문, 제어문을 깔끔하게 사용하는 방법을 잘 모르겠다. 문제를 풀면서 조금씩 사용하는 방식에 대해서 개선을 해봐야겠다. 처음에는 많이 지져분했지만, 우선은 정답을 제출하고 추가적인 리팩토링 작업을 하니 좀 더 가독성 좋은코드가 되었다.
- 알고리즘을 잘하는 친구한테 물어보니, Leetcode에서 제안한 위의 슬라이싱 윈도우의 경우 슬라이싱윈도우가 아닌 투포인터라고 이야기를 해줬다. 아직까지 둘의 차이와 언제써야되는지는 잘모르지만, 오늘 문제를 투포인터방식과 브루트포스 방식으로 풀었을때의 속도차이만큼은 명확하게 느꼈다. 370ms vs 10ms였다... 너무나도 차이가 나기때문에 투포인터 방식으로 다른 문제들을 더 풀어봐야겠다.
