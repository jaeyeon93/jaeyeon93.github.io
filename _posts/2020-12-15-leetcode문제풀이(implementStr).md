---
layout: post
title: 2020-12-15 LeetCode 문제풀이 implement strStr()
comments: true
---

- [문제링크](https://leetcode.com/problems/implement-strstr/)

### 설명
- 문자열 haystack과 needle이 파라미터로 입력받는다. haystack에서 needle이 포함되어있는 최초의 index를 리턴해준다. Java String의 indexOf()와 유사한 기능이다.

```Java
public class ImplementStr {
    public static int strStr(String haystack, String needle) {
        if (needle.equals("")) return 0;
        if (haystack.equals("")) return -1;
        for (int i = 0; i < haystack.length(); i++) {
            if (i + needle.length() > haystack.length()) break;
            for (int j = 0; j < needle.length(); j++) {
                if (haystack.charAt(i+j) != needle.charAt(j)) break;
                if (j == needle.length()-1)
                    return i;
            }
        }
        return -1;
    }

    public static void main(String[] args) {
        System.out.println(strStr("hello", "ll"));
        System.out.println(strStr("aaaaa", "bba"));
        System.out.println(strStr("", ""));

        System.out.println(strStr("", "a")); // expected -1;
        System.out.println(strStr("aaa", "a")); // expected 0;

        System.out.println(strStr("aaa", "aaaa")); // expected -1
        System.out.println(strStr("a", "a")); // expected 0
    }
}
```

### 설명
- 만약 needle이 ""라면 0을 리턴, haystack이 ""라면 -1을 리턴한다.
- haystack이 "hello"이고 needle이 "ll"라고 할때, haystack을 0부터 순차적으로 순회한다. haystack의 i번째 값이 needle의 값과 같다면 haystack.charAt(i+j)와 needle.charAt(j)가 계속해서 같을것이다. 중간에 다르다면 haystack.charAt(i+j) != needle.charAt(j)부분에서 break;로 걸리고 계속해서 같다면 j가 nedle.length-1번 인덱스까지 간다.
- 만약 j가 needle.length-1까지 간다면 포함되므로 처음 인덱스의 시작인 i를 리턴해주고 아니면 -1을 리턴한다.
