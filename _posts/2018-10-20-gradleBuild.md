---
layout: post
title: 2018-10-20 GradleBuild Error
---

- AWS에 배포작업을 하면서 GradleBuild를 하는데, 에러가 발생을 했다.
```
jaeyeonkim@jaeyeonui-iMac  ~/Desktop/stock-crawler   autoBuild  gradle -v

------------------------------------------------------------
Gradle 4.7
------------------------------------------------------------

Build time:   2018-04-18 09:09:12 UTC
Revision:     b9a962bf70638332300e7f810689cb2febbd4a6c

Groovy:       2.4.12
Ant:          Apache Ant(TM) version 1.9.9 compiled on February 2 2017
JVM:          1.8.0_151 (Oracle Corporation 25.151-b12)
OS:           Mac OS X 10.14 x86_64


$gradle build -x test
$./gradelew clean build -x test
```

- 현재 gradle4.7버전을 사용하고있는데, gradle build는 가능하지만 ./gradlew를 이용해서 빌드를 하면 계속 에러가 발생을 했다.

- 에러는 Could not find method compileOnly() for argument 였다.
- Gradle 2.x버전에서는 build.gralde파일에 의존성을 추가할때, provieded를 사용을 했었다.
- 하지만 Gradle 4.x에서는 더이상 provided메소드를 지원을 안하고, compileOnly()를 사용을 해서 빌드를 한다.

#### 왜 gradle build는 되고, ./gradle build는 안됬을까?

- 배포를 처음해보는 내 입장에서는 gradle이 그렇게 중요한가? 라는 인식이 없었다. 단순히 build.gradle에 의존성을 좀 더 깔끔하게 추가하기 위해서 maven이 아닌 gradle을 사용을 했었다.
- 찾아보니 ./gradlew의 경우 그라디언트 래퍼라는 것을 사용을 한다. 여기서 말하는 래퍼는 프로젝트의 일부이고, gralde의 설치를 용이 하게 해준다. 만약에 wrapper가 없으면 사용자가 직접 brew install gradle처럼 명령어를 쳐서 gradle을 직접 설치해야한다.
- 아직까지는 잘 이해를 못했지만, gradlew를 사용해면 wrapper라는 것을 사용을 한다.

```
## build.gradle
task wrapper(type: Wrapper) {
    gradleVersion = '4.0'
}

dependencies {
    compile group: 'org.telegram', name: 'telegrambots', version: '4.1'
    compile group: 'org.projectlombok', name: 'lombok', version: '1.16.20'
    compile group: 'com.github.seratch', name: 'jslack', version: '1.1.6'
    compile group: 'me.ramswaroop.jbot', name: 'jbot', version: '4.0.0'
    compileOnly 'javax.websocket:javax.websocket-api:1.1'
    compile group: 'org.glassfish.tyrus.bundles', name: 'tyrus-standalone-client', version: '1.13.1'
}

```
- wrapper를 사용하기 위해서는 build.gradle파일에 task warpper를 추가하면된다.
- 에러의 원인은 저기에 있었다.... gradle의 버전은 4.7을 사용하는데, wrapper의 버전이 이전에는 2.0을 사용했기때문에 계속 에러가 발생을 했던것이다.
- wrapper의 버전을 바꾸고 gradle wrapper명령어를 다시 입력해서 래퍼의 버전을 바꾸고, 다시 ./gradlew 명령어를 이용하니 정상적으로 빌드가 되었다.
- 이틀동안 이 문제로 엄청나게 고민하고 구글링을 했는데, 함께 고민을 하고 문제해결에 도움을 주신 Brian에게 감사를 표합니다 :)
