---
layout: post
title: 2018-3-15 TIL JS Sorting
---
## 정렬

##### 들어가기
- 컴퓨터에 무언가를 저장할때, 우리는 공간을 요청한다. 그러면 컴퓨터는 메모리를 할당을 해주고, 여러개의 원소를 저장하게되면 배열과 리스트라는 2가지 방법중 1가지를 선택해야한다.

#### 배열과 연결리스트

- 메모리안에 저장된 데이터를 삽입/삭제 하는 경우가 많다. 이 경우 어떻게 해야되나?
- 배열을 사용하면 새 원소를 추가할때 모든 자리를 한칸씩 옮겨야 된다. 여분의 공간을 미리 만들어야하는데, 추가가 안되면 쓸때없는 메모리를 낭비하게된다.
- 하지만 연결리스트를 사용하면 원소를 메모리 어느곳에나 둘 수 있다.
- 모든 원소의 값을 한 번에 읽어야 한다면 연결 리스트가 좋지만, 특정한 원소만 탐색 하고 싶다면 연결리스트는 최악이다. 처음부터 끝까지 일일이 다 찾아야 된다. 하지만 배열은 모든 원소의 주소를 다 알고있기때문에 탐색에 최적화가 되어있다.

#### 선택정렬
1. 주어진 리스트 중에 최소값을 찾는다.
2. 최솟값을 맨앞의 자리와 교환한다.
3. 맨 처음위치를 뺀 나머지 리스트를 같은 방법으로 반복한다.


```
// 1-n까지 정수가 들어있는 배열
// length : 생성될 배열의 길이
// reuturn : 배열

// length 길이 만큼의 배열을 생성한다.
function genArray(length) {
  var arr = [];
  for (var i = 0; i <+ length; i++) {
    arr[i] = i + 1;
  }
  return arr;
}

// knuth shuffle
function shuffle(arr) {
  // 각각의 원소를 뒤섞는다
  for(var i = arr.length - 1; i > 0; i--) {
    var imax = i + 1;
    var ridx = Math.floor(Math.random() * imax); // 0 ~ len-1
    var temp = arr[i];
    arr[i] = arr[ridx];
    arr[ridx] = temp;
  }
  return arr;
}


function selectionSort(arr) {
  for (var i = 0; i < arr.length - 1; i++) {
    var idx = i;
    var min = arr[i];
    for (var j = i; j < arr.length; j++) {
      if (arr[j] < min) {
        idx = j;
        min = arr[j];
      }
    }
    // 바꿔주는 지점. i, min이랑 바꿔주는 지점.
    var temp = arr[i];
    arr[i] = arr[idx];
    arr[idx] = temp;
  }
  return arr;
}

// 정렬이 됬는지 확인하는 과정
function checkSorted(arr) {
  for (var i = 0; i < arr.length - 1; i++) {
    if (arr[i] > arr[i+1]) {
      return false;
    }
  }
  return true;
}

//main(); unnamed function
(function() {
  var testArray = genArray(20);
  console.log(testArray);
  console.log(shuffle(testArray));
  console.log(selectionSort(testArray));
  console.log(checkSorted(testArray));
})()

```
