---
layout: post
title: 2019-08-16 CSS 심화
---

- animations, transitions, transformations, media query에 대해서 배운다.

## transition

- 트랜지션은 state가 바뀔 때 적용이 된다. hover, active, focus, visited가 있다.

```
<head>
    <meta charset="UTF-8">
    <title>Transitions</title>
    <style>
        .box {
            background-color: green;
            color: white;
            transition:all .5s ease-in-out;
        }
        .box:hover {
            background-color: red;
            color: blue;
        }
        .box:active {
            background-color: blue;
            color: white;
        }
    </style>
</head>
<body>
<span class="box">
    Text
</span>
</body>
```

- transition:all을 하면 해당 효과가 일어날때 다 바뀐다. 예를 들어 transition:color 또는 transition:background-color를 하면 hover나 active가 일어날때, color, background-color만 바뀐다.

## transformations

- [참조문서](https://developer.mozilla.org/en-US/docs/Web/CSS/transform)
- html문서의 element를 변경, 모습이 변하는 효과

```
<head>
    <meta charset="UTF-8">
    <title>Transformation</title>
    <style>
        .box{
            width: 500px;
            height: 500px;
            background-color: red;
            transition: transform .5s;
        }
        .box:hover {
            transform: rotate(45deg);
        }
    </style>
</head>
<body>
<div class="box"></div>
</body>
```

- transition에 transfrom효과를 넣으면 좋다.

## Animations

- 계속 효과를 반복하려면? 애니메이션을 생성하면 된다.
- 키프레임은 css에 애니메이션을 생성했다는 것을 가르켜준다. from에서 to가 필요하다.

```
<head>
    <meta charset="UTF-8">
    <title>Animation</title>
    <style>
        .box {
            width: 100px;
            height: 100px;
            background-color: red;
            animation: 1.5s scaleAndRotateSquare infinite ease-in-out;
        }
        @keyframes scaleAndRotateSquare {
            from{
                transform: none;
            }
            to {
                transform: rotate(1turn) scale(.5, .5);
            }
        }
    </style>
</head>
<body>
<div class="box">

</div>
</body>

    <style>
        .box {
            width: 100px;
            height: 100px;
            background-color: red;
            animation: 1.5s scaleAndRotateSquare infinite ease-in-out;
        }
        @keyframes scaleAndRotateSquare {
            0% {
                transform: none;
            }
            50% {
               transform: rotate(1turn) scale(.5, .5);
                background-color: blue;
            }
            100% {
                transform: none;
                background-color: red;
            }
        }
    </style>
```

- step이 많으면 %단게별로 하고, 스텝이 2단계면 from-to를 쓴다.

## Media query

- 큰 스크린을 위한 css, 작은 화면을 위한 css가 있다.

```
<head>
    <meta charset="UTF-8">
    <title>Media Query</title>
    <style>
        body {
            background-color: green;
        }
        @media screen and (min-width: 320px) and (max-width: 640px){
            body {
                background-color: blue;
            }
        }
    </style>
</head>
```

- @media screen에서 min-width와 max-width를 기준으로 브라우저 별로 css를 설정할 수 있다.
