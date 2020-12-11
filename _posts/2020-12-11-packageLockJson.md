---
layout: post
title: 2020-12-10 JS 데이터타입과 변수저장방식
comments: true
---
## package-lock.json

- package.json에서는 버전정보를 저장할때 version range를 사용한다. 1.2.7이라고 명시하면 1.2.7이상을 사용할거라는 의미와 같다. 하지만 동일한 package.json을 사용하여 npm install을 하여도 노드모듈이 다르게 설치될 수 있다. 특정버전의 라이브러리가 새로운 버전이 출시되고 npm install을 하면 최신버전으로 설치될 수 있기 때문이다.
- 예방하는 방법
  - npm --version으로 npm 버전을 일치시킨다.
- package.lock-json은 node_modules의 구조, package.json이 수정되고 생설될때 당시 의존성을 정확하고 구체적인 정보를 가지고 생성된다.
- package.lock-json이 존재할때 package.json의 동작방식이 변한다
- npm ci를 이용하면 package.json대신 package-lock.json을 이용하여 설치하는 명령어

## 느낀점
- node로 개발을 하면서 package.json에 대해서는 알았지만 package-lock.json이 어떤 역할을 하는지에 대해서는 몰랐다. 이번기회에 둘의 차이에 대해 알게 되었다.
