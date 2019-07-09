---
layout: post
title: 2019-07-09 Callback
---

- JS에서 함수도 객체이기때문에, 함수를 다른 함수의 인자처럼 사용할 경우에는 오직 함수의 정의만 넘기면 된다.
- **즉 함수의 이름만 넘기면 된다. 함수라고 해서 () 같은 것을 붙여줄 필요가 없다**

```
setInterval(callback, 1000)
```

### 콜백함수는 클로저

- 다른 함수의 인자로 콜백을 전달할 때, 전달받은 함수의 특정시점에 그 콜백함수가 동작한다. 전달 받은 함수가 이미 콜백함수를 내부에서 정의한것처럼.
- 콜백은 클로저와 같다. 콜백함수는 콜백함수를 포함한 함수 내부의 인자에 접근이 가능하고 심지어 전역함수에도 접근이 가능하다. **즉 기존 함수가 가진 스코프에서 새로운 내부 스코프가 추가 되는 것이다.**


## 콜백함수의 적용 기본 원칙

### 이름이나 익명의 함수를 사용하라(Use Named OR Anonymous Function as callbacks)


```
function logStuff (userData) {
if ( typeof userData === "string") {
console.log(userData);
  } else if ( typeof userData === "object") {
	for (var item in userData) {
	console.log(item + ": " + userData[item]);
    }
  }
}

// 2개의 인자를 받아서 마지막에 콜백함수를 호출
function getInput(options, callback) {
	allUserData.push(options);
    callback(options)
}

getInput({name : 'Rich', speciality : 'js'}, logStuff)
```
### 콜백함수가 실행 되기전에 함수임을 명확하게 하기 : 콜백함수가 인자로 전달되어 함수 내부에서 실행될 때 전달받은 인자가 함수인지를 명확하게 하자.

```
function getInput(options, callback) {
	allUserData.push(options);

    if (typeof callback === 'function') {
    	callback(options);
    }
}
```

### this를 사용한 메서드를 콜백으로 사용시 문제


- 콜백함수가 this객체를 사용하는 메서드인 경우 반드시 this객체의 컨택스트를 보호하도록 콜백함수를 수정해야한다.
- 예를들어 전역함수에 인자로 콜백함수가 전달된 경우에는 this가 window객체를 가리키게 만들거나 또는 콜백함수를 감싸고 있는 메서드를 가리키게 해야한다.

```
let clientData = {
  id: 094545,
  fullName: 'Not set',

  setUserName: function(firstName, lastName) {
    console.log(`setUsername에 전달받은 ${firstName}, ${lastName}`);
    this.fullName = firstName + ' ' + lastName;
  }
}

function getUserInput(firstName, lastName, callback) {
  callback(firstName, lastName);
}

getUserInput('Barack', 'Obama', clientData.setUserName);

console.log(clientData.fullName);

console.log(global.fullName);
//result
setUsername에 전달받은 Barack, Obama
this value in clientData [object global]
Not set
Barack Obama
```
- clientData.setUserName을 실행하면 예상과 달리 this.name이 세팅이 되지 않는다. 이는 getUserInput메서드가 글로벌 함수이기 때문이다. this가 global(window)이기때문이다.
- this바인딩이 js의 문제중 하나이다. call과 apply를 통한 this를 보호해야한다. 이 함수들은 this객체를 유지하고 내부 인자들을 함수로 전달하는 역할을 한다.

```
let clientData = {
  id: 094545,
  fullName: 'Not set',
  setUserName: function(firstName, lastName) {
    console.log(`setUsername에 전달받은 ${firstName}, ${lastName}`);
    console.log(`this value in clientData ${this}`);
    this.fullName = firstName + ' ' + lastName;
  }
}

function getUserInput2(firstName, lastName, callback, callbackObj) {
  callback.apply(callbackObj, [firstName, lastName]);
}
getUserInput2('jimmy', 'kim', clientData.setUserName, clientData);
```
- call함수는 항상 첫번째 인자로 this객체를 사용한다. 나머지 인자들은 콤마를 기준으로 구분
- Apply의 경우 첫번째 인자로 this객체를 사용하고, 마지막 파라미터는 배열로 존재.
- 위의 형태로 this를 매핑할 수 있다.

# this

- js는 함수도 객체로 취급을 하기 때문에 애매함이 있다.
- 함수는 실행이 가능한 코드와 연결된 객체라는 개념으로 접근을 하면 좋다.
- 함수는 객체이므로 확장이 가능하다.

```
function hello(x) {
  console.log('hello ' + x);
}

hello('first');

hello['woman'] = 'Beautiful lady';

console.log(hello.woman)
//result
hello first
Beautiful lady
```

```
var myDog = {
  name: 'Badoogi',
  bark: function() {
    console.log('wa wa aw');
  },

  callDog: function() {
    console.log(this.name + ' Come on');
  }
}

console.log(myDog.callDog());

```
- 이 객체 안에서의 this는 myDog라는 객체이다. this가 저 객체만 가리키고 있나? No => this는 this를 둘러싸고 있는 객체만을 가리키는 상태가 아니기 때문이다. !!!
- this는 this가 포함된 값을 부르는 객체를 가리키도록 변경이 가능하다.

```
var speech = 'this is a global value';

var william = {
  'speech': 'williamShakespeare said~~~',
  'sayIt': displayQuote
};

function displayQuote() {
  console.log(this.speech + '!!!');
  // 이곳의 this는 정적인 값이 아닌 함수를 호출하는 객체에 따라 다른 this를 가진다.
}

william.sayIt();
displayQuote();

//result
williamShakespeare said~~~!!!
undefined!!!
```
- displayQuote()라는 함수를 단독으로 실행시키면, this는 브라우저에서는 window 콘솔에서는 global을 가리키게 된다.
- 콘솔에서는 결과가 undefined!!!이지만 크롬에서는 this is global value!!로 나온다.

#### JS함수에서 객체를 가지지 않는 상태의 함수를 부르는 일은 결코 없어야 한다.

```
console.log('isNaN??' + isNaN(NaN));

function changeNaN() {
  this.isNaN = function() {
    return 'not anymore';
  }
}

changeNaN();

console.log('isNaN??' + isNaN(NaN));

```
- 객체를 가지지 않는 changeNaN함수를 호출했더니 this가 global객체를 가리키게 되어 전역변수 자체 값을 바꿔버렸다. 이 문제를 해결하기 위해 call, apply함수를 사용한다. 즉 다른 객체에 의해 함수가 호출되는 것이 아닌 그 함수 자체가 실행이 되게 만드는 것이다.


### call(thisObj[, arg1[, arg2[, [, argN]]]])
- 여기서 thisObj는 선택사항으로 현재의 객체로 사용이 될 객체이다.
