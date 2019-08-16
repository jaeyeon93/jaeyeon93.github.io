---
layout: post
title: 2019-8-15 Promise 복습
---

- 비동기 : 멀티태스킹, 한가지 이상의 것을 한번에 처리하는게 아니라 단지 스위치를 빠르게 전환하는거. 하지만 컴퓨터는 2가지 일을 동시에 처리를 할 수 있다.(wifi도 연결, 배터리 충전, 영상재생 ...)

```
const mul = new Promise((resolve, reject) => {
    resolve(2);
});

mul
.then(number => {
    console.log(number * 2);
})
.then(otherNumber => {
    console.log(otherNumber * 2);
})
//result
4
NaN
```

- 첫번째 then에서는 인자가 전달이 됬지만, 두번째 인자에서는 전달이 안됬다. 첫번째 then에서 return을 해줘야한다.

```
const mul = new Promise((resolve, reject) => {
    resolve(2);
});

const timesTwo = (number) => number * 2;


mul
    .then(timesTwo)
    .then(timesTwo)
    .then(timesTwo)
    .then(timesTwo)
    .then(timesTwo)
    .then(() => {
        throw Error('Something is wrong');
    })
    .then((last) => console.log(last))
    .catch(err =>
```

- 예외가 발생했을때 .catch를 통해 해결을 할 수 있다.

### Promise.all

- 여러개의 api에서 응답을 기다릴때. Promise.all을 사용하면 한개의 Promise 리스트로 응답한다.
- promise가 전부다 resolve되야 응답을 준다.

```
const p1 = new Promise((resolve) => setTimeout(resolve, 5000, 'first'));
const p2 = new Promise((resolve) => setTimeout(resolve, 1000, 'second'));
const p3 = new Promise((resolve) => setTimeout(resolve, 3000, 'third'));

const motherPromise = Promise.all([p1, p2, p3]);

motherPromise.then(values => console.log(values));
//result
[ 'first', 'second', 'third' ]
```

- first의 5초가 끝날때까지 응답을 안준다.
- 하나라도 reject가 되면 응답을 안해준다. motherPromise도 reject가 된다.

### Promise.Race

- Promise all과 사용법이 같다. 여러개의 promise중에 하나라도 resolve, reject가 되면 된다.
- 어느것이 되든 상관이 없을때 promise.race를 사용하면 된다.

### Promise.finally

- reject, resolve에 상관없이 최종적으로 무언가를 하고 싶을때.

```
const p1 = new Promise((resolve, reject) => {
    setTimeout(reject, 1000, 'first');
})
    .then(value => console.log(value))
    .catch(e => console.log(e))
    .finally(() => console.log('hello world')
```

- node환경에서 실행을 하면 finally is not a function이라는 이슈가 생긴다. 그러므로 크롬에서 돌리자.

### 느낀점

- 콜백에서 비동기 처리를 하기 위해서 처음 Promise를 썼었다. gmail api를 사용하면서 여러개의 비동기를 처리해야되는 상황이 발생했었고, 이때 해결하기 위해서 async await과 promise.all을 사용했었다. 어떤 경우에 사용하면 될지를 알고나서 개념을 학습하니까 이해가 잘됬다.
