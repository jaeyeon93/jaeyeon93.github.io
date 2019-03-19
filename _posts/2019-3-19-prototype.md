---
layout: post
title: 2019-03-19 JS Prototype
---
프로토타입
=

#### Java, C++과 같은 클래스기반 객체지향 프로그래밍에서는 객체생성 이전에 클래스를 정의하고 객체(인스턴스)를 생성한다.
- 프로토타입 기반 객체지향프로그래밍에서는 클래스없이도 객체를 생성할 수 있다.
- JS의 모든 객체는 자신의 부모역할을 담당하는 객체와 연결되어 있다. 이 부모객체를 Prototype(프로토타입)객체라고 한다.
- Prototype객체는 생성자 함수에 의해 생성된 각각의 객체에 공유 프로퍼티를 제공하기 위해 사용된다.

```
var student = {
  name: 'Lee',
  score: 90
};

// student에는 hasOwnProperty 메소드가 없지만 아래 구문은 동작한다.
console.log(student.hasOwnProperty('name')); // true

console.dir(student);
```

![](https://poiemaweb.com/img/printout_student_obj_from_chrome.png)

- JS의 모든객체는 [[Prototype]]이라는 인터널슬롯을 가진다. [[Prototype]]값은 null 또는 객체이며 상속을 구현할때 사용된다. [[Prototype]]객체의 데이터프로퍼티는 get액세스를 위해 상속되어 자식 객체의 프로퍼티처럼 사용할 수 있다. 하지만 set액세스를 허용되지 않는다.
- [[Prototype]]의 값은 Prototype객체이며 \_\_proto\_\_accessor prorperty로 접근할 수 있다. \_\_proto\_\_프로퍼티에 접근하면 내부적으로 Object.getPrototypeOf가 호출되어 프로토타입 객체를 반환한다.
- student객체는 \_\_proto\_\_프로퍼티로 자신의 부모객체(프로토타입 객체)인 Object.prototype을 가리키고 있다.

```
student.__proto__ === Object.prototype // true
```
- 객체를 생성할 때 프로토타입은 결정된다. 결정된 프로토타입객체는 다른 임의의 객체로 변경할 수 있다. => 부모객체인 프로토타입을 동적으로 변경할 수 있다. 이것을 활용하여 상속을 구현가능.


## [[Prototype]] vs prototype 프로퍼티

- 모든객체는 자신의 프로토타입 객체를 가리키는 [[Prototype]]인터널 슬록을 가지고 있다. 이것은 상속을 위해 사용한다.
- 함수도 객체이므로 [[Prototype]]인터널 슬롯을 가지고 있다. 함수는 일반객체와 달리 prototype프로퍼티도 소유하게 된다.
#### prototype프로퍼티는 프로토타입 객체를 가리키는 [[Prototype]] 인터널 슬롯과 다르다. prototype프로퍼티와 [[Prototype]]은 모두 프로토타입 객체를 가리키지만 관점의 차이가 있다.

```
function Person(name) {
    this.name = name;
}

let foo = new Person('foo');

console.dir(Person); // prototype 프로퍼티가 있다.
console.dir(foo); // prototype 프로퍼티가 없다
```

- [[Prototype]]
  - 함수를 포함한 모든 객체가 가지고 있는 인터널 슬롯이다.
  - 객체입장에서 자신의 부모역할을 하는 프로토타입객체를 가리키며 함수객체의 경우 Function.prototype을 가리킨다.

```
Person.__proto__ === Function.__proto__; // true
```

- prototype프로퍼티
	- 함수객체만 가지고 있는 프로퍼티이다.
	- 함수객체가 생성자로 사용될 때 이 함수를 통해 생성될 객체의 부모 역할을 하는 객체(프로토타입)을 가리킨다.


```
console.log(Person.prototype === foo.__proto__); // true
```

## constructor 프로퍼티

- 프로토타입 객체는 constructor프로퍼티를 갖는다. constructor프로퍼티는 객체의 입장에서 자신을 생성한 객체를 가리킨다.
- 위의 예제에서 Person()생성자 함수에 의해 생성된 객체를 foo라 하자. foo객체를 생성한 객체는 Person()생성자 함수이다. 이때 foo객체 입장에서 자신을 생성한 객체는 Person()생성자함수이며, foo객체의 프로토타입객체는 Person.prototype이다. 따라서 프로토타입 객체 Person.prototype의 constructor프로퍼티는 Person()생성자 함수를 가리킨다.

```
console.log(Person.prototype.constructor === Person);
// Person()생성자 함수에 의해 생성된 객체는 Person()생성자 함수이다.

console.log(foo.constructor === Person);
// foo 객체를 생성한 객체는 Person()생성자함수이다.

console.log(Person.constructor === Function);
// Person()생성자 함수를 생성한 객체는 Function()생성자 함수이다.
```

## Prototype chain

##### 프토타타입 체인 :  JS에서 특정메소드나 프로퍼티에 접근할때, 해당객체에 접근하려는 프로퍼티나 메소드가 없으면 [[Prototype]]이 가리키는 링크를 따라 프로토타입객체의 프로퍼티나 메소드를 차례대로 검색한다.

```
console.log(student.hasOwnProperty('name')); // true
console.log(student.__proto__ === Object.prototype); // true
console.log(Object.prototype.hasOwnProperty('hasOwnProperty')); // true
```
- student는 hasOwnProperty메소드가 없지만, student객체의 Prototype객체인 Object.prototype의 메소드 hasOwnProperty를 호출하였기때문에 가능하다.

### 객체리터럴 방식으로 생성된 객체의 프로토타입 체인

- 객체 리터럴 방식으로 생성된 객체는 내장함수인 Object()생성자 함수로 객체를 생성.
- Object()생성자 함수이다. Object()생성자 함수는 일반객체와 달리 prototype프로퍼티가 있다.
	- prototype프로퍼티는 함수객체가 생성자로 사용될 때 이 함수를 통해 생성된 객체의 프로토타입 객체를 가리킨다.
	- [[Prototype]]은 객체의 입장에서 프로토타입객체를 가리킨다.

```
let person = {
    name: 'jimmy',
    gender: 'male',
    sayHello : function () {
        console.log('Hi, My name is ' + this.name);
    }
};

console.dir(person);
console.log(person.__proto__ === Object.prototype);
console.log(Object.prototype.constructor === Object);
console.log(Object.__proto__ === Function.prototype);
console.log(Function.prototype.__proto__ === Object.prototype);
```
![](https://poiemaweb.com/img/object_literal_prototype_chaining.png)

#### 결론적으로 객체 리터럴을 사용하여 객체를 생성한 경우 프로토타입객체는 Object.prototype이다.

### 생성자함수로 생성된 객체의 프로토타입 체인

- 생성자 함수로 객체를 생성하기 위해서는 함수정의를 해야한다. 함수표현식으로 함수를 정의할 때 함수 리터럴 방식을 사용한다.

```
let square = function (number) {
    return number * number;
};

```
- 함수선언식, 함수표현식 모두 함수 리터럴 방식을 사용한다. 함수리터럴 방식은 Function()생성자 함수로 함수를 생성하는것을 단순화 한것이다.

#### 3가지 함수 정의 방식은 결국 Function()생성자 함수를 통해 함수객체를 생성한다. 어떠한 방식을 사용하든 함수객체의 prototype객체는 Function.prototype이다. 생성자 함수도 함수객체이므로 생성자 함수의 prototype객체는 Function.prototype이다.

객체생성방식 | 엔진의 객체생성 | 인스턴스의 prototype객체
------|--------- | -------
객체리터럴 | Object()생성자함수 | Object.prototype
Object()생성자함수 | Object()생성자함수 | Object.prototype
생성자함수 | 생성자함수 | 생성자함수이름.prototype

```
function Person(name, gender) {
    this.name = name;
    this.gender = gender;
    this.sayHello = function () {
        console.log('Hi, My name is ' + this.name);
    };
}

let foo = new Person('Lee', 'male');

console.dir(Person);
console.dir(foo);

console.log(foo.__proto__ === Person.prototype);
console.log(Person.prototype.__proto__ === Object.prototype);
console.log(Person.prototype.constructor === Person);
console.log(Person.__proto__ === Function.prototype);
console.log(Function.prototype.__proto__ === Object.prototype);

```

![](https://poiemaweb.com/img/constructor_function_prototype_chaining.png)

## 프로토타입 객체의 확장

- 프로토타입 객체도 일반객체처럼 프로퍼티 추가/삭제를 할 수 있다. 이때 변경된 프로퍼티는 즉시 프로토타입체인에 반영된다.

```
function Person(name) {
    this.name = name;
}

let foo = new Person('jim');

Person.prototype.sayHello = function () {
    console.log('Hi! my name is ' + this.name);
};

foo.sayHello();
```
- 생성자함수 Person은 프로토타입 객체 Person.prototype과 prototype프로퍼티에 의해 바인딩되어있다. Person.prototype객체에 sayHello메소드를 추가하였다. 바로 프로토타입체인에 반영이 되고, 생성자 함수 Person에 의해 생성된 모든 객체는 프로토타입 체인에 의해 부모객체인 Person.prototyp의 메소드를 사용할 수 있게 된다.

![](https://poiemaweb.com/img/extension_prototype.png)

## 원시타입의 확장

```
let str = 'test';

console.log(typeof str); // string
console.log(str.constructor === String); // true
console.dir(str); // test

let strObj = new String('test');
console.log(typeof strObj); // object
console.log(strObj.constructor === String); //true
console.dir(strObj); // String
```
- 원시타입문자열과 String()생성자 함수로 생성한 문자열 객체의 타입은 다르다. 원시타입은 객체가 아니므로 프로퍼티나 메서드를 가질 수 없다. 하지만 원시타입으로 프로퍼티를 호출할 때 원시타입과 연관된 객체로 일시적으로 변환되어 프로토타입 객체를 공유하게 된다.

![](https://poiemaweb.com/img/string_constructor_function_prototype_chaining.png)


## 프로토타입 객체의 변경

- 객체를 생성할때 프로토타입은 결정된다. 결정된 프로토타입 객체는 다른 임의의 객체로 변경할 수 있다. 이러한 특징을 활용하여 객체의 상속을 구현할 수 있다.
- 프로토타입 객체변경 시점 이전에 생성된 객체
	- 기존 프로토타입 객체를 [[Prototype]]에 바인딩한다.

- 프로토타입 객체 변경 시점 이후에 생성된 객체
	- 변경된 프로토타입 객체를[[Prototype]]에 바인딩한다.

```
function Person(name) {
    this.name = name;
}

let foo = new Person('Lee');

// change prototype
Person.prototype = { gender : 'male'};

let bar = new Person('Kim');

console.log(foo.gender); // undefined
console.log(bar.gender); // male

console.log(foo.constructor); // f Person(name)
console.log(bar.constructor); // f Object
```
![](https://poiemaweb.com/img/changing_prototype.png)

- constructor프로퍼티는 Person()생성자 함수를 가리킨다
- 프로토타입 객체 변경 후, Person(0생성자 함수의 Prototype프로퍼티가 가리키는 프로토타입 객체를 일반 객체로 변경하면서 Person.prototoype.constructor 프로퍼티도 삭제되었다. 따라서 프로토타입 체인에 의해 bar.constructor의 값은 프로토타입 체이닝에 의해 Object.prototype.consturctor 즉 Object()생성자 함수가 된다.

## 프로토타입 체인 동작 조건

- 객체의 프로퍼티 값을 할당하는 경우, 프로토타입 체인이 동작하지 않는다. 이는 객체에 해당 하는 프로퍼티가 있는 경우, 값을 재할당하고 해당 프로퍼티가 없는 경우 해당 객체에 프로퍼티를 동적으로 추가하기 때문이다.

```
function Person(name) {
    this.name = name;
}

// change prototype
Person.prototype = { gender : 'male'};

let foo = new Person('Lee');
let bar = new Person('bar');

console.log(foo.gender); // male
console.log(bar.gender); // male

// 1. foo 객체에 gender프로퍼티가 없으면 프로퍼티 추가
// 2. foo 객체에 gender프로퍼티가 있으면 프로퍼티값 할당

foo.gender = 'female';

console.log(foo.gender); // female
console.log(bar.gender); // male
```

![](https://poiemaweb.com/img/condition_prototype_chaining.png)

- foo객체의 gender프로퍼티 값을 할당하면 프로토타입 체인이 발생하여 Person.prototype객체의 gender프로퍼티에 값을 할당하는 것이 아니라 foo객체에 프로퍼티를 동적으로 추가한다.
