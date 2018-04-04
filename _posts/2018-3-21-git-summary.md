---
layout: post
title: 2018-3-21 TIL git
---

## github을 통한 협업 방식

### pull request 배우기

1. github 저장소 만들기
2. 클론
3. 파일 작성
> README.md
> index.html index.js index.css
4. 커밋 푸시


### git 기본 명령어

1. git add : working direct의 변경사항을 index에 올린다.
2. git commit : index에 올려진 내용을 로컬저장소에 올린다.
3. git push : 로컬저장소에 있는 내용을 원격저장소로 올린다.
4. origin이 원격저장소 주소이다. github에 있는 내주소가 origin이다.
5. origin/master는 원격저장소에 있는 마스터브렌치를 이야기한다.



### 브렌치 바꾸기
- git branch branch-name
- git branch > 현재브렌치 확인
- git checkout branch-name // change the branch.
- git push -u origin gh-pages //
- git remote add cs https://github.com/code-squad/level1-js // cs라는 원격저장소를 추가한다. why? 게임으로 치면 북미서버, 한국서버를 만들겠다는 의미이다. 하나는 내가 full로 권한을 가지고 있고, 다른하나는 권한이 없다.
:exclamation: 내 아이디가 있는 복사본 프로젝트를 클론한다.
포크 후에는 동일한 저장소가 내 아이디에도 생긴다!
```
export githubid="my_id";
git clone https://github.com/my_id/level1-js;
```

### 원격 저장소 추가
origin말고 추가 원격 저장소를 만든다.
```
$ git remove -v // check the remote repository
$ git remote add cs https://github.com/code-squad/leve1-js
```

### 내 작업 브렌치 만들기

```
$ git branch my_id // git branch jimmy
$ git checkout jimmy
```

### 프로젝트 하위 디렉토리 생성

```
$ mkdir jimmy
$ cd /jimmy
$ cp -a /Users/jimmy/Desktop/project/git-study-pull_request/* jimmy

```
### gh-pages 만들기
```
$ git branch gh-pages
$ git checkout gh-pages
$ git push -u origin gh-pages
```
이렇게 하고 나면
```
https://myid.github.com/proj_name
```
에서 볼 수 있다.
```
index.html
```
이 꼭 있어야 합니다.


### 커밋 및 푸시

```
$ git add .
$ git commit -m "[jimmy]" Init project"
$ git push -u origin jimmy
```

### 풀리퀘
github가서 풀리퀘를 보내고, 리뷰 과정을 걸쳐 머지가 이루어 집니다.
지금까지 내 저장소의 작업내용(커밋)
github가서 풀리퀘 보내고, 리뷰과정을 걸쳐 머지가 이루어진다.

>src : code-squad/level1-js : master
>target : jimmy/level1-js : jimmy
jimmy가 최신이다.


### cs-마스터랑 합치기

```
$ git checkout master
$ git fetch cs // cs원격저장소만 로컬로 가져와 주세요.
$ git merge cs/master
```


#### 오늘의 느낀점

- 최근에 git에서 충돌이 잦았었다. 그래서 git의 개념에 대해서 공부를 다시했다. 특히 학원에서는 노트북 집에서는 데스크탑으로 작업을 하는데, git의 origin저장소와 upstream저장소, local저장소의 개념에 대해서 익숙하지가 않았다. push를 통해서 local에 있는 코드가 원격 origin저장소로 업로드가 되야 다른작업환경에서도 수정코드를 pull해 올 수 있다는것을 다시 알게되었다....
