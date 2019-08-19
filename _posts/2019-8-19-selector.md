---
layout: post
title: 2019-8-19 CSS Selector
---

### 배경

HTML5 이전에는 HTML에 style을 컨트롤 할 수 있는 태그가 존재하여 css없이도 스타일 표현이 가능했다. HTML5부터는 HTML은 정보의 구조화, CSS는 스타일정의로 역할이 구분되었다.

## 용어

### 셀렉터(Selector, 선택자)
- css는 html 엘리먼트의 style을 정의하는데 사용된다. 이를 위해 HTML요소를 선택할 수 있어야한다.
- 셀렉터는 스타일을 적용하고 싶은 HTML 요소를 선택하기 위한 수단이다.

![](https://poiemaweb.com/img/css-syntax.png)

- 위의 문법을 Rule Set(or Rule)라고 한다. 셀렉터에 의해 선택된 html요소를 어떻게 렌더링할지 지시한다. Css rule set은 HTML 문서에 속해 있는 셀렉터를 통해 모든 h1요소를 선택한 후 스타일을 정의한다.
- 스타일 시트(Style Sheet) : RuleSet의 집합

```
// html
<!DOCTYPE html>
<html>
<head>
  <style>
    h1 { color: red; }
    p  { color: blue; }
  </style>
</head>
<body>
  <h1>Hello World!</h1>
  <p>This paragraph is styled with CSS.</p>
</body>
</html>

//css
h1, p { color: red; }
```
- 복수개의 셀렉터를 지정하고 쉼표(,)로 구분한다.

### 프로퍼티
- 셀렉터로 html요소를 선택하고 {}내에 프로퍼티 값을 지정한다. 프로퍼티는 [표준스펙](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference#Keyword_index)에 나와있는것을 사용하고 세미콜론으로 구분한다.

```
p {
  color: ...;
  font-size: ...;
}
```

### 값(속성값)
- style을 적용하기 위해 프로퍼티를 사용했다. 프로퍼티에서 사용할 수 있는 값을 **키워드나 크기단위, 색상표현단위** 등의 [특정단위](https://poiemaweb.com/css3-units)로 지정해야한다.


## HTML과 CSS연동
- html은 css를 포함할 수 있다. css를 가지고 있지 않으면 브라우저에서 기본으로 적용하는 CSS(user agent stylesheet)에 의해 렌더링 된다.
- 아래같은 방법으로 css를 html에 연동한다.

### Link Style
- 외부에 있는 css를 로드한다. 가장 일반적
```
// .html
<head>
    <link rel="stylesheet" href="css/style.css">
</head>

// .css
h1 { color: red; }
p  { background: blue; }
```

### Embeding style
- html의 \<head>태그 아래 \<style>태그안에 css를 포함.

```
head>
    <style>
      h1 { color: red; }
      p  { background: aqua; }
    </style>
</head>
```

### inline style
- html요소의 style프로퍼티에 넣는다. 하지만 잘 안쓴다.

```
<body>
    <h1 style="color: red">Hello World</h1>
    <p style="background: aqua">This is a web page.</p>
</body>
```

### Reference
[CSS사용빈도](https://developer.microsoft.com/en-us/microsoft-edge/platform/usage/)

# 셀렉터

#### 전체셀렉터
| 패턴 | description |
| - | - |
| * | HTML 문서 내의 모든 요소를 선택한다. html 요소를 포함한 모든 요소가 선택된다. (head 요소도 포함된다)|
```
<style>
    /* 모든 요소를 선택 */
    * { color: red; }
</style>
```

#### 태그셀렉터
| 패턴 | description |
| - | - |
| 태그명 | 지정된 태그명 |

```
<style>
    /* 모든 p 태그 요소를 선택 */
    p { color: red; }
</style>
```

#### id셀렉터
| 패턴 | description |
| - | - |
| #id 어트리뷰트값 | id값으로 일치하는걸 선택. id는 유일 |

```
<head>
  <style>
    /* id 어트리뷰트 값이 p1인 요소를 선택 */
    #p1 { color: red; }
  </style>
</head>
<body>
  <h1>Heading</h1>
  <div class="container">
    <p id="p1">paragraph 1</p>
  </div>
</body>
```

#### class셀렉터
| 패턴 | description |
| - | - |
| .class 어트리뷰트값 | class어트리뷰트값으로 일치하는걸 선택. class어트리뷰트는 중복가능 |
```
<head>
  <style>
    /* class 어트리뷰트 값이 container인 모든 요소를 선택 */
    /* color 어트리뷰트는 자식 요소에 상속된다. */
    .container { color: red; }
    /* not supported in IE */
    #p2 { color: initial; }
  </style>
</head>
<body>
  <h1>Heading</h1>
  <div class="container">
    <p id="p1">paragraph 1</p>
    <p id="p2">paragraph 2</p>
  </div>
  <p>paragraph 3</p>
</body>
```

#### 어트리뷰트 셀렉터
| 패턴 | description |
| - | - |
| 셀렉터[어트리뷰트] | 지정된 어트리뷰트를 갖는 모든 요소를 선택 |
```
<head>
  <style>
    /* a 요소 중에 href 어트리뷰트를 갖는 모든 요소 */
    a[href] { color: red; }
  </style>
</head>
<body>
  <a href="http://www.poiemaweb.com">poiemaweb.com</a><br>
</body>
```
| 패턴 | description |
| - | - |
| 셀렉터[어트리뷰트="값"] | 지정된 어트리뷰트를 갖고 지정된 값을 가지는 모든 요소를 선택 |
```
<!DOCTYPE html>
<html>
<head>
  <style>
    /* a 요소 중에 target 어트리뷰트의 값이 "_blank"인 모든 요소 */
    a[target="_blank"] { color: red; }
  </style>
</head>
<body>
  <a href="http://www.poiemaweb.com">poiemaweb.com</a><br>
  <a href="http://www.google.com" target="_blank">google.com</a><br>
  <a href="http://www.naver.com" target="_top">naver.com</a>
</body>
</html>
```

| 패턴 | description |
| - | - |
| 셀렉터[어트리뷰트^="값"] | 지정된 어트리뷰트 값으로 시작되는 요소를 선택 |
```
<head>
  <style>
    /* a 요소 중에 href 어트리뷰트 값이 "https://"로 시작하는 요소 */
    a[href^="https://"] { color: red; }
  </style>
</head>
<body>
  <a href="https://www.test.com">https://www.test.com</a><br>
  <a href="http://www.test.com">http://www.test.com</a>
</body>
```

| 패턴 | description |
| - | - |
| 셀렉터[어트리뷰트$="값"] | 지정된 어트리뷰트 값으로 끝나는 요소를 선택 |
```
<head>
  <style>
    /* a 요소 중에 href 어트리뷰트 값이 ".html"로 끝나는 요소 */
    a[href$=".html"] { color: red; }
  </style>
</head>
<body>
  <a href="test.html">test.html</a><br>
  <a href="test.jsp">test.jsp</a>
</body>
```

### 복합셀렉터

#### 후손셀렉터

![](https://poiemaweb.com/img/descendant-child.png)
- 자신보다 1-level위에 있으면 부모요소, 아래에 있으면 자식요소라고 한다.
- n level하위에 있으면 후손요소
- 후손 셀렉터는 셀렉터A의 모든 하위요소 중 셀렉터B와 일치되는 요소를 선택
```
expression : 셀렉터A 셀렉터B
<head>
  <style>
    /* div 요소의 후손요소 중 p 요소 */
    div p { color: red; }
  </style>
</head>
<body>
  <h1>Heading</h1>
  <div>
    <p>paragraph 1</p>
    <p>paragraph 2</p>
    <span><p>paragraph 3</p></span>
  </div>
  <p>paragraph 4</p>
</body>
```

#### 자식셀렉터
- 셀렉터A의 모든 자식요소중 셀렉터B와 일치하는 요소를 선택

```
expression : 셀렉터A > 셀렉터B

<head>
  <style>
    /* div 요소의 자식요소 중 p 요소 */
    div > p { color: red; }
  </style>
</head>
<body>
  <h1>Heading</h1>
  <div>
    <p>paragraph 1</p>
    <p>paragraph 2</p>
    <span><p>paragraph 3</p></span>
  </div>
  <p>paragraph 4</p>
</body>

```
- 자손요소(n-level)은 안된다.

#### 형제동위셀렉터
- 형제관계에서 뒤에 일치하는 요소를 선택
![](https://poiemaweb.com/img/Sibling_Combinator.png)

```
expression : 셀렉터A + 셀렉터B

<html>
<head>
  <style>
    /* p 요소의 형제 요소 중에 p 요소 바로 뒤에 위치하는 ul 요소를 선택한다. */
    p + ul { color: red; }
  </style>
</head>
<body>
  <div>A div element.</div>
  <ul>
    <li>Coffee</li>
    <li>Tea</li>
    <li>Milk</li>
  </ul>

  <p>The first paragraph.</p>
  <ul>
    <li>Coffee</li>
    <li>Tea</li>
    <li>Milk</li>
  </ul>

  <h2>Another list</h2>
  <ul>
    <li>Coffee</li>
    <li>Tea</li>
    <li>Milk</li>
  </ul>
</body>
</html>
```
- 쭈우욱 뒤가 아니라 바로 뒤에 있는 요소만 !!! 적용이 된다.

#### 일반형제 셀렉터
- 셀렉터A의 형제중 셀렉터A 뒤에 일치하는 셀렉터B 요소를 모두 선택
```
expression : 셀렉터A ~ 셀렉터B
<html>
<head>
  <style>
    /* p 요소의 형제 요소 중에 p 요소 뒤에 위치하는 ul 요소를 모두 선택한다.*/
    p ~ ul { color: red; }
  </style>
</head>
<body>
  <div>A div element.</div>
  <ul>
    <li>Coffee</li>
    <li>Tea</li>
    <li>Milk</li>
  </ul>

  <p>The first paragraph.</p>
  <ul>
    <li>Coffee</li>
    <li>Tea</li>
    <li>Milk</li>
  </ul>

  <h2>Another list</h2>
  <ul>
    <li>Coffee</li>
    <li>Tea</li>
    <li>Milk</li>
  </ul>
</body>
</html>
```

### 가상클래스셀렉터(Pseudo-Class-Selector)

가상클래스는 요소의 특정 상태에 따라 스타일을 정의한다. 가상클래스는 .대신 콜론(:)을 사용한다.
- 마우수가 올라와 있을때
- 링크를 방문하기 전후
- 포커스가 들어와 있을때

#### 링크셀렉터, 동적셀렉터

|pseudo-class|Description|
|-|-|
|:link|셀렉터가 방문안한 링크|
|:visited|셀렉터가 방문한 링크|
|:hover|셀렉터에 마우스가 올라와 있을때|
|:acrive|셀렉터가 클릭된 상태일때|
|:focus|셀렉터에 포커스가 들어와 있을때|
```
<!DOCTYPE html>
<html>
<head>
  <style>
    /* a 요소가 방문하지 않은 링크일 때 */
    a:link { color: orange; }

    /* a 요소가 방문한 링크일 때 */
    a:visited { color: green; }

    /* a 요소에 마우스가 올라와 있을 때 */
    a:hover { font-weight: bold; }

    /* a 요소가 클릭된 상태일 때 */
    a:active { color: blue; }

    /* text input 요소와 password input 요소에 포커스가 들어와 있을 때 */
    input[type=text]:focus,
    input[type=password]:focus {
      color: red;
    }
    </style>
  </head>
<body>
  <a href="#" target="_blank">This is a link</a><br>
  <input type="text" value="I'll be red when focused"><br>
  <input type="password" value="I'll be red when focused">
</body>
</html>
```
