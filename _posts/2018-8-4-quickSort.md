---
layout: post
title: 2018-8-4 퀵정렬
---

Quick Sort
----

#### MergeSort와 마찬가지로 분할을 이용해서 정렬을 한다. 다만 분할 방법이 다르다.

 - 분할 : 배열을 다음과 같은 조건이 되도록 두 부분으로 나눈다. 기준값을 pivot이라고 한다.
 - elements in lower parts <= elements in upper parts
 - 정복 : 각 부분을 순환적으로 정렬한다.
 - 합병 : nothing to do
 - 어떤것을 pivot으로 잡느냐에 따라 차이가 많이 난다.
 - 피봇보다 작은값과 큰값으로 분할을 한다.

#### 분할을 했을당시에 이미 피봇을 기준으로 앞 뒤를 나누었기 때문에 합병을 했을때, 정렬을 할 필요가 없다.

![](/Users/jaeyeonkim/Desktop/quickSort-1.png)

![](/Users/jaeyeonkim/Desktop/quickSort-2.png)

- index p에서 r사이의 배열 A
- 분할은 partition메소드로 정의하고, q가 피봇의 위치이다.
- partition의 리턴값이 q이면, p~q-1까지가 피봇보다 작은배열, q+1~r까지가 피봇보다 큰배열을 나타낸다.

![](/Users/jaeyeonkim/Desktop/quickSort-3.png)

- pivot과 데이터들을 한번씩 비교하기 위해서 한바퀴를 돌 수 밖에 없다.
- i번째 자리를 기억하고 있어야한다. i는 피봇보다 작은값들의 마지막 값이다.
- i+1번째부터 j-1까지는 피봇보다 큰 값들이다.
- j번째부터 피봇하고 비교를 한다. 만약에 j가 피봇보다 크다면, 그냥 넘어가면 된다. 그러면 i+1부터 j까지 피봇보다 큰 집합인것이고, 바로 다음값인 j+1의 값하고 피봇하고 비교하면된다.
- 반면 j가 피봇보다 작으면, 앞쪽으로 보내야한다. 그러면 i+1하고 바꾸면 된다. 먼저 i값을 증가시키고, A[i] A[j]를 바꾸고, j를 1증가시키면된다.

![](/Users/jaeyeonkim/Desktop/quickSort-4.png)
![](/Users/jaeyeonkim/Desktop/quickSort-5.png)
![](/Users/jaeyeonkim/Desktop/quickSort-6.png)
- n개의 데이터를 비교하는데(partition)을 하는데 걸리는 시간복잡도는 : o(n)

#### 퀵솔트의 시간복잡도

![](/Users/jaeyeonkim/Desktop/quickSort-7.png)
- 퀵솔트에서 최악의 시간복잡도는 이미 정렬된 데이터이다. 그런경우에는 피봇이 가장큰원소(마지막)값으로 선택이 되고,
- 피봇과 나머지가 나누어 진다.
- 나머지는 index 0부터 n-1까지이고, n은 마지막값(피봇)이 된다.
- 0~n-1에 대해서 recursion을 이용해서 다시 정렬을 하게된다. T(n) = T(0) + T(n-1)의 시간복잡도가 된다.
- 피봇값이 항상 n-1개의 데이터와 비교를 해야되기때문에 T(n-1)은 항상 포함이 된다.
- 결국에 n-1개의 배열을 정렬하기 위해서는 또 index(n-1)을 피봇으로 선택을 하고, n-2만큼 시간을 들여서 정렬을 하고... 그러다보면 시간복잡도가 n^2이 된다.

##### 퀵솔트의 시간복잡도 최상의 경우

![](/Users/jaeyeonkim/Desktop/quickSort-8.png)
- 위의경우는 mergeSort와 시간복잡도가 유사하다.

![](/Users/jaeyeonkim/Desktop/quickSort-9.png)
- 각층에서 분할하는데 걸리는 시간은 n이다.
- 전체걸리는 시간은 n*(층)
- 1:9정도로 분할이 되더라도 시간복잡도는 nlog(n)이 나온다.

#### 퀵솔트의 평균 시간복잡도
![](/Users/jaeyeonkim/Desktop/quickSort-10.png)
- 피봇이 가장 작은 값일때~ 가장 큰값일때까지.
- 모든 값에 대한 확률은 1/n

#### 퀵솔트 피봇의 선택
![](/Users/jaeyeonkim/Desktop/quickSort-11.png)
- 첫번째나 마지막값을 피봇으로 선택을 하는 것은 매우 안좋다.
