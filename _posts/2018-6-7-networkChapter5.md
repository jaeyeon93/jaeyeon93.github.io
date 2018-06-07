---
layout: post
title: 2018-6-7  Network chapter5 HTTP와 연계하는 웹서버
---

웹서버
---


### 1대로 멀티 도메인을 가능하게 하는 가상 호스트
![](https://github.com/jaeyeon93/jaeyeon93.github.io/blob/master/images/network/chapter5/chapter5-1.jpg?raw=true)
- HTTP/1.1에서는 하나의 HTTP서버에 여러 개의 웹 사이트를 실행할 수 있다.
- 1대의 서버에 여러개의 도메인을 넣는 것인데, 이를 위해서는 가상호스트 기술이 필요하다.
- 가상호스트를 사용하면 물리적으로는 1대이지만, 가상으로 여러대가 있는 것처럼 설정이 가능.

#### 프록시, 게이트웨이
![](https://github.com/jaeyeon93/jaeyeon93.github.io/blob/master/images/network/chapter5/chapter5-2.jpg?raw=true)
- 프록시 : 서버와 클라이언트의 양쪽 역할을 하는 중계 프로그램, 클라이언트로 부터 리퀘스트를 받아서 서버에 전송하고, 서버로부터 리스폰스를 클라이언트에 전솔

![](https://github.com/jaeyeon93/jaeyeon93.github.io/blob/master/images/network/chapter5/chapter5-3.jpg?raw=true)
- 프록시 서버의 기본적인 동작은 클라이언트로부터 받은 리퀘스트를 다른 서버에 전송을 하는 것이다.
- 받은 리퀘스트의 URI를 변경하지 않고 그 다음 리소스를 가지고 있는 서버에 전송
- 리소스 본체를 가진 서버를 오리진 서버라고 한다.
- 오리진 서버로 부터 되돌아온 리스폰스는 프록시 서버를 경유해서 클라이언트로 간다.
- HTTP통신을 할때 여러대의 프록시서버를 사용할 수 있는데, 이를 위해서는 Via 헤더 필드에 경유한 호스트의 정보를 넣어야한다.
#### 프록시 서버를 사용하는 이유는 캐시를 사용해서 네트워크를 효율적으로 사용하는 것과 특정 사이트에 대한 엑세스제한과 엑세스로그를 획득하기 위해서이다.

#### 캐싱 프록시(Cashing Proxy)
- 프록시 서버 상에 리소스 캐시를 보존해 두는 타입
- 프록시에 다시 같은 리퀘스트가 오면 오리진 서버로부터 리소스를 획득하는 것이 아니라 캐시를 리스폰스로 되돌려 주는 역할이다.

#### 투명 프록시(Transparent Proxy)
- 프록시로부터 리퀘스트와 리스폰스를 중계할 때, 메세지를 변경하지 않는 타입.
![](https://github.com/jaeyeon93/jaeyeon93.github.io/blob/master/images/network/chapter5/chapter5-4.jpg?raw=true)
- 게이트웨이 : 다른 서버를 중계하는 서버. 클라이언트로부터 수신한 리퀘스트를 리소스를 보유한 서버인 것 처럼 수신
- 게이트웨이는 프록시와 동작이 유사하다. 게이트웨이가 있는 경우, 그 다음에 있는 서버가 HTTP이외의 서비스를 제공을 한다.
- 클라이언트와 게이트웨이 사이를 암호화를 해서 안전하게 접속을 한다.
- 게이트웨이는 데이터베이스에 접속해 SQL 쿼리를 사용하거나 결제 시스템과 연계할 때 사용을 한다.

### 캐시
![](https://github.com/jaeyeon93/jaeyeon93.github.io/blob/master/images/network/chapter5/chapter5-5.jpg?raw=true)
- 캐시는 프록시 서버와 클라이언트 로컬 디스크에 보관된 리소스의 사본을 뜻한다.
- 캐시를 사용하면 리소스를 가진 서버의 액세스를 줄일 수 있다. 그만큼 오리진 서버에 같은 데이터를 전송할 필요가 없다.
![](https://github.com/jaeyeon93/jaeyeon93.github.io/blob/master/images/network/chapter5/chapter5-6.jpg?raw=true)
- 캐시에는 유효기간이 있기때문에, 오리진 서버에 리소스의 유효성을 확인하거나 새로운 리소스를 다시 획득하러 가는 경우도 있다.
![](https://github.com/jaeyeon93/jaeyeon93.github.io/blob/master/images/network/chapter5/chapter5-7.jpg?raw=true)
- 브라우저도 캐시를 가질 수 있다.
- 브라우저에 유효한 캐시는 로컬디스크에 저장을 한다.
