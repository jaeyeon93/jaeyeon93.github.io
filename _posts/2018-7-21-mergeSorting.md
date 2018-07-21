합병정렬
----

### 분할정복법

- 분할 : 해결하고자 하는 문제를 작은 크기의 동일한 문제들로 분할
- 정복 : 각각의 작은 문제를 순환적으로 해결
- 합병 : 작은 문제의 해를 합하여(merge) 원래 문제에 대한 해를 구함

![](https://github.com/jaeyeon93/jaeyeon93.github.io/blob/master/images/algorithm/mergeSort/mergeSort_1.png?raw=true)

- 두개의 정렬된 리스트를 하나로 합치는 부분을 코드로 작성을 해야한다.

![](https://github.com/jaeyeon93/jaeyeon93.github.io/blob/master/images/algorithm/mergeSort/mergeSort_2.png?raw=true)

![](https://github.com/jaeyeon93/jaeyeon93.github.io/blob/master/images/algorithm/mergeSort/mergeSort_3.png?raw=true)

- 두개의 정렬이 각각 정렬이 되어있다.
- 가장 작은 값은 두개의 정렬의 각각 앞쪽값이다.
- 2개의 정렬된 정렬을 하나의 추가배열을 만들어서 이용을 한다.
- i는 첫번째 배열에서 가장 작은 값, j는 두번째 배열에서 가장 작은 값, k는 합쳐진 배열에서 가장 작은 값을 의미한다.
- A[i]와 B[j]를 비교해서 C[0]의 값을 구한다.

![](https://github.com/jaeyeon93/jaeyeon93.github.io/blob/master/images/algorithm/mergeSort/mergeSort_4.png?raw=true)

![](https://github.com/jaeyeon93/jaeyeon93.github.io/blob/master/images/algorithm/mergeSort/mergeSort_5.png?raw=true)


- mergeSort는 기본적으로 Recursion이다. 기본적으로 매개변수를 지정해야한다.
- mergeSort의 매개변수 A[]는 배열이고, p는 시작인덱스, r은 마지막인덱스를 나타낸다.
- p >= r의 경우 데이터가 1이거나 0이다. 정렬할 필요가 없어진다.
- 분할해서 문제를 풀어야하므로 배열을 반으로 쪼개야된다. 그래서 p,r의 중간값인 q를 설정한다. 가운데값은 홀수냐 짝수냐에 따라 다르다.
- p~q까지 recursion으로 정렬하고, q+1~r까지 정렬을 한다.
- merge는 배열A의 p~q까지의 정렬된 배열과, q+1~r까지의 정렬된 배열을 합쳐서 하나의 배열로 만드는 것이다.

![](https://github.com/jaeyeon93/jaeyeon93.github.io/blob/master/images/algorithm/mergeSort/mergeSort_6.png?raw=true)
![](https://github.com/jaeyeon93/jaeyeon93.github.io/blob/master/images/algorithm/mergeSort/mergeSort_7.png?raw=true)


```
public class MergeSort {

    public static void merge(int [] data, int p, int q, int r) {
        int i = p;
        int j = q+1;
        int k = p;
        int [] tmp = new int[data.length];
        // tmp라는 합쳐질 배열의 이름
        while (i <= q && j <= r) {
            // i<=q는 아직 앞쪽에 데이터가 남아있다는 뜻이다. j<=r은 뒷쪽배열에 아직 데이터가 있다.
            if (data[i] <= data[j])
                tmp[k++] = data[i++];
            // data[i]가 data[j]보다 작으면 data[i]가 tmp[k]로 내려오고, k와 i의 값을 1씩 증가시킨다.
            tmp[k++] = data[j++];
            // data[i]와 data[j]를 비교를 해서 더 작은값을 tmp[k]에다가 넣는다.
        }
        // 위의 while문을 빠져나온것은 i가 q를 넘어갔거나. j가 r을 넘어간것이다.

        while (i <= q)
            tmp[k++] = data[i++];
        // 앞쪽 배열에 데이터가 남아있다면 그대로 데이터를 옮겨라
        while (j <= r)
            tmp[k++] = data[j++];
        // 뒤쪽 배열에 데이터가 남아있으면 그대로 데이터를 옮겨라

        //tmp에 있는 데이터를 원래데이터 data[]에 그대로 다시 옮겨줘야한다.
        for (int a = p; a <= r; a++)
            data[a] = tmp[a];
    }
}
```
![](https://github.com/jaeyeon93/jaeyeon93.github.io/blob/master/images/algorithm/mergeSort/mergeSort_8.png?raw=true)


- n개의 데이터를 정렬하는데 걸리는 시간은, T(n/2)이 2번 필요하다. merge하는데 필요한 시간은 n이다.
- T(n) = 2T(n/2) + n이다.
