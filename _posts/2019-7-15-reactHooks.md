---
layout: post
title: 2019-07-16 React Hooks
---

- 리액트 hooks는 주로 setState를 할 필요가 없을때 함수형으로 사용했다.
- 함수형을 쓰면서 state, ref를 사용할 수 있게 해준게 react hooks이다.

```
<script type="text/babel">
        const GuGuDan = () => {
          const [first, setFist] = React.useState(Math.ceil(Math.random()*9));
          const [second, setSecond] = React.useState(Math.ceil(Math.random()*9));
          const [value, setValue] = React.useState('');
          const [result, setResult] = React.useState('');
        }
    </script>
```

- 선언과 동시에 초기화를 위와 같은 방식으로 한다.
- React.useState(초기값);
- hooks에서는 useRef라는 것으로 DOM에 접근을 한다.


```
//Hooks
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <script crossorigin src="https://unpkg.com/react@16/umd/react.development.js"></script>
    <script crossorigin src="https://unpkg.com/react-dom@16/umd/react-dom.development.js"></script>
    <script src="https://unpkg.com/babel-standalone@6/babel.min.js"></script>
    <title>React Hooks</title>
</head>
<body>
<div id="root"></div>

<script type="text/babel">
  const GuGuDan = () => {
    const [first, setFirst] = React.useState(Math.ceil(Math.random() * 9));
    const [second, setSecond] = React.useState(Math.ceil(Math.random() * 9));
    const [value, setValue] = React.useState('');
    const [result, setResult] = React.useState('');
    const inputRef = React.useRef(null);

    const onSubmitForm = (e) => {
      e.preventDefault();
      console.log('submit버튼에 누른값', e.target.value);
      if (parseInt(value) === first * second) {
        setResult('정답 ' + value);
        console.log('value : ', value);
        setFirst(Math.ceil(Math.random() * 9));
        setSecond(Math.ceil(Math.random() * 9));
        setValue('');
        inputRef.current.focus();
      } else {
        setResult('땡');
        setValue('');
        inputRef.current.focus();
      }
    };

    const onChangeInput = (e) => {
      setValue(e.target.value);
    };

    return (
      <React.Fragment>
        <div>{first} 곱하기 {second}는?</div>
        <form onSubmit={onSubmitForm}>
            <input ref={inputRef} onChange={onChangeInput} value={value} />
            <button>입력</button>
        </form>
        <div id="result">{result}</div>
      </React.Fragment>
    )
  }
</script>
<script type="text/babel">
  ReactDOM.render(<GuGuDan/>, document.querySelector('#root'));
</script>
</body>
</html>
```

- this.state가 빠졌다.
- render()을 하면 함수자체가 통째로 다시 실행이 된다. 그래서 느릴수가 있다.
- html속성에서 class를 사용할 수 있는데, 리액트에서는 속성으로 class를 못쓰고 className을 쓴다. label의 for속성도 for문으로 인식할 수 있기때문에 labelFor로 쓴다.

# Webpack

- 웹팩을 왜쓸까?
- html만 있어도 JS를 쓸 수 있는데, 실제로는 컴포넌트가 엄청나게 많다.
- 컴포넌트마다 스크립트를 하나씩 만들면, 하나의 html파일에 코드만 몇백줄 몇천줄이 되고 유지보수에 힘들다.
- 페이스북은 컴포넌트가 몇개일까??. 예전에 2만개였다.
- webpack을 통해 여러개의 js파일을 하나의 js파일로 합쳐준다. babel도 적용시키고. 여러가지로 유용하다.
- webpack을 하려면 Nodejs를 알아야한다. node는 js실행기다. 리액트를 할때 노드를 알아야하는것은 서버를 안다보다는 실행기를 이해를 해야한다.

```
npm i -D webpack webpack-cli
```

- -D는 개발할때는 쓴다는 의미
- package.json에 dependencies는 실제 서비스에 쓰이는것들, devDependencies는 개발에서만 쓰이는것들


```
//webpack.config.js

//client.jsx
const React = require('react');
const ReactDom = require('react-dom');
```

- createReactApp도 굉장히 편리하지만 직접해봐야한다.

```
const React = require('react');
const { Component } = React;
class WordReply extends Component {
  state = {

  }

  render() {
    return
  }
}

module.exports = WordReply;

```
- class분리를 할때 위와 같은 방법으로 쪼갠다.
- module.export = name을 하면 외부에서 불러올 수 있다.
- html sciprt src에서는 js파일을 하나만 인식을 한다. 만약에 2만개면???... 그래서 웹팩을 사용한다.
- 웹팩은 webpack.config.js로 모든게 돌아간다.

```
const path = require('path');

module.exports = {
  name: 'word-reply-setting', // 불필요
  mode: 'development', // 실서비스 : production
  devtool: 'eval',
  resolve: {
    extensions: ['.js', '.jsx']
  },
  // entry : 입력, output : 출력. enrty와 output이 가장중요하다.
  entry: {
    app: ['./client'],
  },
  output: {
    path: path.join(__dirname, 'dist'), // path.join하면 경로를 합쳐준다. __dirname현재 폴더경
    filename: "app.js"
  } // dist에 app.js라는 파일이 생긴다.
}



```
- entry app : []에 합치고 싶은 파일들을 넣는다.
- 콘솔에 webpack을 치면 된다.
- client.jsx에서 WordReply.jsx를 불러오고 있는데, 웹팩이 알아서 다 파악을 한다. 안적어도 된다.
- resolve를 사용하면 app에 있는 ./client.jsx, client.js등을 찾는다.

```
$webpack
```
- 미리 명령어로 등록을 해야 사용할 수 있다.
- 2가지 방법. 명령어로 등록을 하거나. package.json script에 적는다.
- npm run dev를 한다.
