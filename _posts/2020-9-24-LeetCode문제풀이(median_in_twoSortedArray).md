---
layout: post
title: 2020-9-24 LeetCode - Median of Two sorted Array
comments: true
---

# 문제


- [문제링크](https://leetcode.com/problems/median-of-two-sorted-arrays/)
- 2개의 정렬된 배열이 주어질때, 2개의 정렬된 배열의 중앙값을 리턴해라.

```
Input: nums1 = [1,3], nums2 = [2]
Output: 2.00000
Explanation: merged array = [1,2,3] and median is 2.

Input: nums1 = [1,2], nums2 = [3,4]
Output: 2.50000
Explanation: merged array = [1,2,3,4] and median is (2 + 3) / 2 = 2.5.

Input: nums1 = [0,0], nums2 = [0,0]
Output: 0.00000

Input: nums1 = [], nums2 = [1]
Output: 1.00000

Input: nums1 = [2], nums2 = []
Output: 2.00000
```



##내풀이

```java
class Solution {
  public double findMedianSortedArrays(int[] nums1, int[] nums2) {
    int m = nums1.length;
    int n = nums2.length;
    int size = m+n;
    int [] merged = new int[size];
    // (m+n)이 홀수인경우, mergedArray에서 (m+n)/2번쨰 인덱스를 리턴.
    // (m+n)이 짝수인경우, mergedArray에서 (m+n)/2번째와 (m+n)/2-1의 리턴.
    int idx = 0;
    int i = 0;
    int j = 0;
    while (i < m && j < n) {
      if (nums1[i] < nums2[j])
        merged[idx++] = nums1[i++];
      else
        merged[idx++] = nums2[j++];
    }
    while (i < m) {
      merged[idx++] = nums1[i++];
    }
    while (j < n) {
      merged[idx++] = nums2[j++];
    }
    if (size % 2 != 0)
      return merged[size/2];
    return (double) (merged[size/2] + merged[(size/2)-1])/2;
  }
}
```

- 주어진 2개의 배열 nums1, num2의 길이가 다를 수 있고, 합쳤을때 정렬이 안됬기때문에 우선은 merged라는 배열을 만들었다.
- while문들을 통해 2개의 배열을 하나로 만들었다.
- 두 배열의 길이를 각각 m, n이라 할때 m+n이 홀수인 경우와 짝수인 경우로 나누었다.
- 홀수인 경우는 merged[m+n]으로 접근 가능하지만, 짝수인 경우 (m+n)/2과 (m+n/2)-1번째 인덱스를 더하고 2로 나누어야된다.
- 위와 같은 코드로 문제를 접근해서 통과했다

## 느낀점

- 문제의 권장사항은 시간복잡도가 O(log(m+n))이였다. 하지만 내가 접근한 방법은 O(m+n)으로 접근했다. 해설이 있기때문에 해설을 보려고 했으나. 해설이 너무나도 어려워서 아직까진 이해가 안됬다. 이 부분은 어떻게 공부를 해야될지 모르겠다.
