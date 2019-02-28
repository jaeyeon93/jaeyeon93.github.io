---
layout: post
title: 2019-02-28 Python기초 공부하기
---

### 배운것
- 파이썬 기초문법
- 파이썬 가상환경


파이썬 환경설정
=

### 가상환경
- 격리된 라이브러리 설치 공간
- 가상환경을 활용하면, 별도의 디렉토리에 나누어서 라이브러리를 설치한다.
- 라이브러리 : 특정기능을 지원해주는 코드뭉치
```
$ python3 -m venv myvenv
```
- 가상환경 디렉토리를 만든다.

#### 가상환경사용하기
- 위의 명령어를 통해 myvenv라는 디렉토리가 만들어졌다. 한 디렉토리에 여러개의 가상환경 디렉토리를 구성할 수 있지만, 보통 1개만 사용한다.
- 만들어진 가상환경을 활성화를 해야한다.

##### 가상환경을 활성화하기
```
jaeyeonkim@jaeyeonui-iMac  ~/Desktop/django  source myvenv/bin/activate
(myvenv)  jaeyeonkim@jaeyeonui-iMac  ~/Desktop/django 
```
- 가상환경이 활성화가 되면 터미널에 (myvenv)로 표시가 된다.
- deactivate 명령어를 입력하면 가상환경이 비활성화가 된다.

#### 장고 설치하기

- 가상환경을 먼저 활성화를 시켜야한다. 그래야만 개발할 디렉토리에만 장고가 설치가 되고 다른 프로젝트를 진행할때 충돌이 발생하지 않는다.
```
$pip install "django<2"
$django-admin --version
```
- django 2.0미만의 최신 버전으로 설치를 하겠다.
- 장고의 버전을 확인한다.

#### Jupyter Notebook설치
```
$python3 -m pip install jupyter
```

## Python 기초문법

### 파이썬

```
str.upper()
str.lower()
len(str)
str.title()
```
- 문자열끼리 덧셈 곱셈이 가능하다. 나눗셈은 안된다.


#### 예외

```
len(3024)
---------------------------------------------------------------------------
TypeError                                 Traceback (most recent call last)
<ipython-input-7-333274bc6bf1> in <module>
----> 1 len(3024)

TypeError: object of type 'int' has no len()


len(str(12345))
>>>5
```
- 문자는 길이의 개념이 있지만, 숫자는 길이의 개념이 없기때문에 에러가 난다.
- 에러가 발생하면 예외처리를 해야한다.
- str() 함수는 대상을 문자열로 변환한다.
- int() 함수는 대상을 정수로 변환한다.
- int("문자열", 진법)을 통해 특정 진법으로 변환이 가능하다.

#### 리스트
- 리스트 : 서로 다른 객체들을 일렬로 나열한(순서대로) 자료구조
```
lottery = [3,4,42,12]
lottery
len(lottery)
lottery.sort()
//[3, 4, 12, 42]

lottery.reverse()
//[42,12,4,3]
lottery.append(199)
//[42, 12, 4, 3, 199]
lottery.insert(0, 200)
//[200, 42, 12, 4, 3, 199]
lottery[0]
//200
lottery[8]
// error
lottery.pop(0)
// 200
```
- sort()메서드를 사용하면 그 뒤에 리스트가 정렬된 형태로 바뀐다.
- list.insert(index, value) : 해당 인덱스에 값을 삽입한다.
- list.pop(index) : 해당 인덱스에 값을 삭제한다.

#### 딕셔너리
- 딕셔너리를 임의의 순서를 가지고 있다. 인덱스가 아닌 key로 값을 찾는다. 키는 문자열이든, 숫자든 상관이 없다.

```
participant = {'name': 'Ola', 'country': 'Poland', 'favorite_numbers': [7, 42, 92]}
// {'name': 'Ola', 'country': 'Poland', 'favorite_numbers': [7, 42, 92]}

김철수성적 = {
    '국어':90,
    '수학':80,
    '영어':90

}
(김철수성적['국어'] + 김철수성적['영어'] +김철수성적['수학']) / 3
//86.66666666666667
```

- 정렬이 필요할때는 리스트를 사용하고, 키-값이 서로 연관되어 있거나, 효과적으로 키를 사용해서 어떤 값을 찾을때는 딕셔너리를 사용한다.
- 딕셔너리를 리스트와 유사하지만, 변경(mutable)할 수 있다. 딕셔너리가 만들어진 후에도, 그 값을 마음대로 변경할 수 있다는 뜻이다. 리스트, 딕셔너리 모두 다 변경(mutable)이다.


#### 비교하기

```
5 > 2 // true
3 < 1 // false
1 == 1 // true
5 != 2 // true
```
- x > y : x는 y보다 크다
- x < : x는 y보다 작다
- x <= : x가 y보다 작거나 같다
- x >= : x가 y보다 크거나 같다

```
6 > 2 and 2 < 3 // true
3 > 2 and 2 < 1 // false
3 > 2 or 2 < 1 // true
```
- and : and 연산자는, 두 값 모두 참(True)일 경우에만 결과가 참(True)이 됩니다.
- or : or 연산자는, 둘 중 하나만 참(True)일 경우에 결과가 참(True)이 됩니다.

#### Boolean(불리언)

- 불리언의 객체 두가지이다. True(참), False(거짓)
- 파이썬은 항상True라고 써야 이해합니다. 첫 글자가 대문자고 나머지는 소문자여야만, Boolean으로 이해합니다. true, TRUE, tRUE는 모두 틀린 표현이에요. -- True만 올바른 표현입니다. (False도 마찬가지입니다)

#### 조건문

- if...elif...else의 형태이다.
- if에서 들여쓰기가 되면 if에 속한 본문이다.
- 중간조건을 넣기위해서 elif 조건: ..
- elif는 내가 원하는만큼 넣을 수 있다.

#### 주석
- 주석은 #으로 시작하는 줄이다. 코드의 역할을 설명하기 위해사용


#### 함수선언하기

```
def hi():
    print('Hi there')
    print('How are you');

hi()
//result
Hi there
How are you

```
- hi라는 이름의 함수를 선언했다.
- 함수이름()을 통해 함수를 호출한다.

```
def hi(name):
  if name == 'Ola':
      print('Hi ola')
  elif name == 'Sonja':
      print('Hi Sonja')
  else:
      print('Hi anonymous')

hi('Ola');
//result
Hi ola
```
- hi(name)을 선언함으로서 위의 hi는 사라졌다. 따라서 hi함수는 늘 name이라는 인자 1개가 필요하다.

```
def hi(name):
    print('Hi ' + name + '!')

hi('jimmy')
//Hi jimmy !
```

#### 반복문
```
girls = ['Amy', 'Rachel', 'Monica', 'Ola', 'Jamie']

for girl in girls:
    print(girl);
//result
Amy
Rachel
Monica
Ola
Jamie

```

- for...in 문을 통해서 리스트안을 순회한다.

```
for i in range(1,10):
    print(i)
1
2
3
4
5
6
7
8
9

```
- range라는 빌트인메소드를 통해서 range(i,j) i이상 j미만의 정수범위를 순회할 수 있다.


### 느낀점
- 요기요를 지원하게 되서 요기요에서 사용하는 python, django에 대해서 학습을 시작했다. 처음 프로그래밍을 배웠을때 사용했던 언어가 파이썬이였다. 그전에는 가상환경이나 환경설정 등에서 많은 어려움이 있었는데, 스프링과 자바의 학습비용이 높았던 것을 감안하면 파이썬은 그것보다는 편안하다. 일주일동안 배포를 목표로 학습해야겠다.
