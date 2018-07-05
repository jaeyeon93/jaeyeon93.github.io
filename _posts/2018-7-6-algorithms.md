---
layout: post
title: 2018-7-6 알고리즘 종류들
---

기본적인 정렬 알고리즘
----

#### 다양한 종류의 정렬 알고리즘
![](https://github.com/jaeyeon93/jaeyeon93.github.io/blob/master/images/algorithm/sorting/algorithms.png?raw=true)

- 성능의 측면에서는 QuickSort, MergeSort, HeapSort가 빠르다.

### 기본적인 정렬 알고리즘

#### Selection Sort
![](/Users/jaeyeonkim/Desktop/selectionSort.png)

- 기본적으로 가장 큰 값을 찾는다.
- 가장 큰 값을 가장 끝 값과 바꾼다.
- 나머지 작업을 반복한다.
- 이 과정을 마지막 2개의 값이 남을 때까지 반복을 한다.

![](/Users/jaeyeonkim/Desktop/selectionSort_2.png)

- 모든 경우의 수가 같기때문에 최악의 경우 최선의 경우 평균의 경우가 같다.

#### Bubble Sort

![](/Users/jaeyeonkim/Desktop/bubbleSort.png)

- 기본적인 아이디어는 Selection Sort와 같다.
- 최대값을 찾아서 마지막자리와 바꾸는 방법이 SelectionSort와 차이가 있다.
- 1번째 값과 2번째 값을 비교해서 자리를 바꾼다. 2번째와 3번째와 비교를 한다.. ... 그리고 n-1과 n과 자리를 바꾼다. 그 결과로 가장 큰 값이 마지막으로 된다. 이 경우의 한 사이클을 도는데, 시간복잡도는 n-1이다.
- 배열의 길이가 n일때
```
for(int i = 0; i <= n-1; i++) {
	compare(arr[i], arr[i+1]);
}
```
![](/Users/jaeyeonkim/Desktop/bubbleSort_2.png)

- 첫사이클은 n-1까지 반복, 두번째사이클은 n-2까지 반복....

#### Insertion Sort

![](/Users/jaeyeonkim/Desktop/insertionSort.png)

- 정렬할 데이터가 위와같이 6개라면, 첫번째 데이터만 봤을때는 이미 정렬이 되어있다.
- 1,2번째 데이터만을 봤을때는 서로 정렬을 한다.
- 13을 추가해서 12,15,13에 대해서 정렬을 한다.
![](/Users/jaeyeonkim/Desktop/insertionSort_2.png)

- k-1번째까지 정렬이 된상태에서 k번째에 4를 삽입을 한다.
- 4가 들어갈 자리를 찾아야한다.
- 4가 들어갈 위치를 찾기위해서는 1.앞에서부터 찾는다. 2.뒤에서부터찾는다.
- 4의 위치를 찾았더라도 배열이기때문에 뒤에 있는 배열을 한칸씩 이동해야한다.
- 앞에서부터 비교를 하면 데이터를 한칸씩 미뤄야한다.
- 뒤에서부터 비교를 하는게 좀 더 효율적이다.
1. insert할 값을 tmp라는 임시변수에 저장을 한다.
2. tmp값을 바로 앞에 값부터 순차적으로 비교를 한다.
3. 앞에 값이 tmp값보다 크면 한칸 뒤로 shift한다.

![](/Users/jaeyeonkim/Desktop/insertionSort_3.png)

- 운이 좋으면 한번만에 끝난다.
- 최악의 경우 모든값과 다 비교를 한다. i-1번의 비교를 한다.
- 최선의 경우는 한번만 비교를 하면된다. Bubble, Selection정렬의 경우 최악과 최선의 경우의 시간복잡도가 같지만, SelectionSort의 경우는 다르다.
