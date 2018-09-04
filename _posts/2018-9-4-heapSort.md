---
layout: post
title: 2018-9-4 HeapSorting
---

힙정렬
---

- 최악의 경우 시간복잡도 O(nlogn)
- Sorts in place : 추가배열 불필요
- 이진힙(Binary heap) 자료구조를 사용

![](/Users/jaeyeonkim/Desktop/heap1.png)
![](/Users/jaeyeonkim/Desktop/heap2.png)

- 트리에서 부모가 없는 노드를 Root노드라고 한다. 자식이 없는 노드를 leaf노드라고 한다.
- 이진트리 : 각 노드가 최대 2개의 자식노드를 가지는 트리구조이다.
- Full binary tree : 모든 레벨에 노드들이 꽉 차있는 형태
- 이 강의에서는 Max heap을 사용한다.

![](/Users/jaeyeonkim/Desktop/heap3.png)

- (a)에 있는 3개 모두 Complete Binary Tree이다.
- (b)에 있는 3개는 트리구조이지만 Heap은 아니다. MaxHeap, MinHeap 둘 다 아니다.
- (c)는 nonHeap

![](/Users/jaeyeonkim/Desktop/heap4.png)

- a의 구조가 complete binary tree이기때문에 b의 배열처럼 표현을 해도 괜찮다.

![](/Users/jaeyeonkim/Desktop/heap5.png)

- heap정렬을 하기위해서는 max-heapify라는 연산을 해야한다. 그것을 위한 조건은 다음과 같다.
1. 트리가 complete binary tree구조
2. root를 기준으로 subtree들 자체만으로도 heap이여야한다.
3. 유일하게 루트노드만 heap property를 만족안한다.

### heapify과정

![](/Users/jaeyeonkim/Desktop/heap6.png)

- 루트노드인 4와 그 밑에 16의 자리를 바꾼다.
- 4와 8의 자리를 바꾼다.
- 4와 5의 자리를 바꾼다.
- 매 순간 heapProperty가 만족하는지 확인을 해야한다.

### Recursion을 통해 heapify연산하기

![](/Users/jaeyeonkim/Desktop/heapify.png)

- i를 기준으로 하는 SubTree에서 왼쪽 subtree와 오른쪽 subtree 모두 다 heap을 만족할때, 전체 subtree에 대해서 heapify 연산을 한다.

1. 만약에 자식이 없으면 heapify종료
2. 자식이 존재를 한다면, 큰 자식을 k라고 한다.
3. 부모 A[i] >= 자식 A[k]이면 heapify를 종료한다. 반대이면 교환을 한다.

### 반복문을 이용해서 heapify연산하기

![](/Users/jaeyeonkim/Desktop/heapify2.png)

### 정렬할 배열을 힙으로 만들기

![](/Users/jaeyeonkim/Desktop/heap8.png)

- 정렬을 하기 위해서는 우선 heap으로 만들어야한다.
- length[A] : 정렬할 데이터의 갯수(배열의 길이)
- 맨 마지막 노드의 index : n // n의 부모노드 : n/2번째 노드
-  heapify를 한번 하는데 걸리는 시간 logN 하지만 반복문이 n/2번 돌기때문에 총 시간복잡도는 O((n/2)logN))이다. 음... 하지만 heap을 만드는데, 걸리는 시간복잡도는 O(n)이다.
- 배열의 길이가 N일때, heapify의 시작은 N/2부터 시작이다. 그 다음은 (N/2) -1을 한다.

1. 주어진 배열로 힙을 만든다.
2. 힙에서 최대값(루트)를 가장 마지막 값과 바꾼다.
3. 힙의 크기가 1줄어든 것으로 간주. 가장 마지막값은 힙의 일부가 아닌것으로 간주
4. 루트노드에 대해서 heapify(1)을 한다.
5. 반복한다.
