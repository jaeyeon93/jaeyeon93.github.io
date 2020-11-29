---
layout: post
title: 2020-11-29 릿코드 문제풀이 Container With Most Water
comments: true
---

[문제링크](https://leetcode.com/problems/container-with-most-water/)

- 사각형의 면적을 이용해서 최대 면적을 구하는 문제였다. 처음에는 브루드포스 방식을 이용해서 접근했고, 나중에 풀이를 보니 투포인터 방식을 이용하면 O(n)시간동안 문제를 해결할 수 있다고 설명이 되어있어서 함께 풀이를 올린다.

```java
public class MostWater {
    public static int maxArea(int[] height) {
        int result = Integer.MIN_VALUE;
        for (int i = 0; i < height.length; i++)
            for (int j = i+1; j < height.length; j++) {
                int h = Math.min(height[i], height[j]);
                int w = Math.abs(j-i);
                if (h*w > result)
                    result = h*w;
            }
        return result;
    }

    // Using Two-Pointer
    public static int solution(int [] height) {
        int result = 0, l = 0, r = height.length-1;
        while (l < r) {
            result = Math.max(result, Math.min(height[l], height[r]) * (r-l));
            if (height[l] < height[r])
                l++;
            else
                r++;
        }
        return result;
    }

    public static void main(String[] args) {
        int [] height1 = {1, 1};
        System.out.println(maxArea(height1));
        int [] height2 = {4, 3, 2, 1, 4};
        System.out.println(maxArea(height2));
        int [] height3 = {1, 2, 1};
        System.out.println(maxArea(height3));
    }
}

```
### 풀이
- 말그대로 완전탐색을 이용해서 문제를 해결하는 방식이다. 순차적으로 탐색하면서 height[i], height[j]에서 최소의 값이 높이고 되고, 너비는 j-i가 된다. 혹시몰라서 Math.abs를 통해 절대값 처리를 했다.
- 투포인터를 이용한 방식이다. left, right를 이용하였고 left는 0에서부터, right는 가장 끝에서부터 접근을 한다. 넓이를 구하는 방식이야 기존에 result와 Math.max, Math.min을 이용하여 높이를 구하고 너비를 구하는것도 r-l은 이전과 같다. 단지 if문 안에 있는 제약식으로 left, right값을 처리해주면 O(n)시간만에 문제를 해결할 수 있다.
