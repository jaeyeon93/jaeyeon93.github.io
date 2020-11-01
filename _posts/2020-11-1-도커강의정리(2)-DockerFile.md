---
layout: post
title: 2020-11-1 도커 강의정리 도커파일
comments: true
---

## Dockerfile 작성

![스크린샷 2020-10-04 오후 10.37.35](/Users/jaeyeonkim/Library/Application Support/typora-user-images/스크린샷 2020-10-04 오후 10.37.35.png)

- 도커파일을 활용하여 도커이미지를 만들고, 도커이미지를 통해 도커 컨테이너를 만든다.

```dockerfile
FROM node:12

RUN npm install

CMD ["node", "server.js"]
```

- From : node12라는 베이스 이미지를 가져온다.
- RUN : 추가적인 셸 명령어
- CMD : 컨테이너 실행시 최초 명령어



```shell
$docker build .
//result
npm WARN saveError ENOENT: no such file or directory, open '/package.json'
npm notice created a lockfile as package-lock.json. You should commit this file
```

- 에러난다.



### package.json이 없다는 이유?

- 베이스 이미지를 사용해서 임시컨테이너를 만들고, 실제 컨테이너를 사용하게 된다.
- 노드 이미지를 컨테이너에 넣고 npm install을 할때, package.json에 없다고 나온다.
  - package.json이 컨테이너에 있지않고, 컨테이너 밖에 없다.
  - 임시컨테이너에는 node 베이스 이미지는 존재하지만, server.js, package.json은 임시컨테이너 바깥에 존재해서 위의 에러가 발생
- COPY 명령어를 사용해야한다.

```dockerfile
FROM node:12
COPY package.json ./
RUN npm install
CMD ["node", "server.js"]
```

- package.json을 현재 디렉토리로 이동해야한다.

- docker build . => 이미지를 만든다.
- 이미지가 성공적으로 만들어지고 docker run을 하면 server.js를 찾을 수 없다고 에러가 난다.
  - server.js와 다른 파일들도 복사해야한다.



```dockerfile
FROM node:12
COPY ./ ./
RUN npm install
CMD ["node", "server.js"]
```

- Copy ./ ./는 현재디렉토리에 있는 모든 파일을 복사한다.
- docker run images를 하면 정상적으로 실행되지만, 설정한 8080로 요청을 보내면 응답이 안온다. => connection refused

### 포트매핑 : 생성한 이미지에 접근이 안되는이유

```shell
$docker run -p 49160:8080 imageName
```

- 네트워크도 로컬 네트워크에 존재하던 것을 컨테이너 내부 네트워크와 연결해야한다.

 ![스크린샷 2020-10-04 오후 10.58.53](/Users/jaeyeonkim/Library/Application Support/typora-user-images/스크린샷 2020-10-04 오후 10.58.53.png)

- 컨테이너 내부 애플리케이션은 8080이 돌고있다.
- **컨테이너 내부에서 8080을 할당해도 우리가 외부에서 8080으로 접속할 수 있는게 아니다. 포트매핑이 필요**
- -p옵션을 통해 포트매핑을 해야한다.
- -p 49160:8080 => 외부에서 49160포트로 접속하면 컨테이너 내부 8080포트로 접속한다.



### Work directory명시

```dockerfile
FROM node:12
COPY ./ ./
RUN npm install
CMD ["node", "server.js"]
```

- 이미지안에 애플리케이션 소스코드를 가지고 있는 디렉토리 생성.
- **왜 따로 work directory가 필요한가? 노드 이미지의 Root디렉토리에는 home, bin, dev와 같은 파일들이 존재한다. Dockerfile을 이용해서 이미지를 만들고 COPY명령어를 실행하면 복사된 파일들이 Root디렉토리에 복사가 된다.**
- workdir을 지정하지 않고 그냥 copy할때 생기는 문제점
  - 원래 이미지파일과 같은 이름이 있다면? 중복되는 디렉토리나 파일이 있으면 기존에 존재하던 파일,폴더를 덮어쓰운다.
  - 모든 파일을 한 디렉토리에 존재하면 정리가 안된다.
  - 애플리케이션 소스코드는 따로 workdir를 만들어서 관리한다.

```dockerfile
FROM node:12
WORKDIR /usr/src/app
COPY ./ ./
RUN npm install
CMD ["node", "server.js"]
```

- 도커 이미지를 다시 빌드하고 실행해서 셸로 들어가보면 /usr/src/app에 소스 파일들이 들어있는 것을 확인할 수 있다.
- **workdir를 설정하면 컨테이너에 접근할때 root부터 접근하는게 아니라 workdir부터 접근한다. 따라서 copy를 하더라도 workdir를 기준으로 접근한다.**



### 소스코드 변경으로 다시 빌드하는 것에 대한 문제점

- 소스코드를 계속 변경하면 도커 이미지도 다시 빌드하게 된다.
- 소스코드변경=>이미지다시빌드=>다시이미지실행이 무한으로 반복된다.

#### 어떻게 개선할까?

```dockerfile
# 개선전 도커파일
FROM node:12
WORKDIR /usr/src/app
COPY ./ ./
RUN npm install
EXPOSE 8080
CMD ["node", "server.js"]

# 개선후 도커파일
FROM node:12
WORKDIR /usr/src/app
COPY package.json ./
RUN npm install
RUN ./ ./
EXPOSE 8080
CMD ["node", "server.js"]
```

- 소스를 변경한 부분이 종속성과 관련없는 부분이라면, npm install단계까지는 caching된 형태로 제공이 되고. npm install 아래에 존재하는 RUN ./ ./만 변경된다.
- COPY package.json형태가 아닌 COPY ./ ./형태만 제공을 하면 소스코드가 변경될때마다 npm install을 계속하게된다. **비효율적인 방식**



### Docker volume

- 배경 : 이전에 package.json만 이용해서 소스변경과 종속성 설치를 분리했었다. 하지만 소스를 변경할때마다 **변경된 소스 부분은 다시 COPY하고 이미지를 다시 빌드하고 컨테이너를 다시 실행해야되는 불편함이 존재한다. 이런 문제를 해결하기 위해 Volume을 사용한다**

#### COPY와 Volumn방식의 차이

![스크린샷 2020-10-05 오전 12.02.18](/Users/jaeyeonkim/Library/Application Support/typora-user-images/스크린샷 2020-10-05 오전 12.02.18.png)

- 도커컨테이너가 로컬에 있는 파일들을 참조를 해서 변경사항을 반영
- 사용하기 위해서는 명령어가 길다.

```shell
$docker run -p 5000:8080 -v /usr/src/app/node_module -v $(pwd):/usr/src/app imageName or id
```

- 1번째 -v /usr/src/app/node_module은 호스트 디렉토리에 node_module이 없기때문에 컨테이너에 매핑하지 말라고 명시. 경로는 도커파일에서 설정한 workdir
- 2번째 -v pwd경로에 있는 디렉토리, 파일을 /usr/src/app 경로에서 참조

```shell
$docker run -d -p 5000:8080 -v /usr/src/app/node_modules -v $(pwd):/usr/src/app node-test

// 소스코드 변경

$docker stop imageId

// 다시실행
$docker run -d -p 5000:8080 -v /usr/src/app/node_modules -v $(pwd):/usr/src/app node-test
```

- **컨테이너를 띄우고 소스코드가 변경되면 실행중인 컨테이너를 stop을 이용해서 정상적으로 종료한다. 종료후 volumn을 이용해서 mapping을 하면 이미지를 다시 빌드하지 않더라도 변경된 코드를 반영할 수 있다.**
