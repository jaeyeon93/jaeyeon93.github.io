---
layout: post
title: 2018-5-25 Network Chapter3 HTTP메세지와 정보
---

### HTTP 메세지

- HTTP에서 교환하는 메세지를 HTTP메세지라고한다.
- 리퀘스트 측에서 보내는 메세지를 리퀘스트메시지, 리스폰스 측에서 보내는 메세지를 리스폰스메세지라고 한다.
- HTTP메세지를 구분하면 메세지 헤더와 메세지 바디로 구성이 되어있다.

![](https://github.com/jaeyeon93/jaeyeon93.github.io/blob/master/images/network/chapter3/3-1.jpg?raw=true)

- 리퀘스트라인 : 사용하는 메소드와 레퀘스트 URI와 HTTP버전이 포함된다.
- 상태라인 : 상태코드와 설명, HTTP버전이 포함
- 헤더필드 : 리퀘스트와 리스폰스의 여러 조건과 속성 등을 나타내는 헤더필드가 포함된다. 헤더필드에는 일반헤더필드, 리퀘스트헤더필드, 리스폰스헤더필드, 엔티티헤더필드 총 4가지의 헤더필드가 있다.

![](https://github.com/jaeyeon93/jaeyeon93.github.io/blob/master/images/network/chapter3/3-2.jpg?raw=true)

### 인코딩

- 인코딩 : 변환
- 전송할 때 인코딩을 하면 다량의 액세스를 효율적으로 처리를 한다. 대신 컴퓨터는 CPU 등의 리소스를 많이 소비하게된다.

#### 메세지 바디와 엔티티 바디의 차이
1. 메세지 : HTTP통신의 기본 단위로 8비트로 구성되고 통신을 통해서 전송
2. 엔티티 : 리스폰스와 리퀘스트의 페이로드(부가물)로 전송되는 정보로 엔티티 헤더필드와 엔티티 바디로 구성이된다.

##### HTTP메시지 바디의 역할은 리퀘스트와 리스폰스에 관한 엔티티바디를 운반하는 일이다.

![](https://github.com/jaeyeon93/jaeyeon93.github.io/blob/master/images/network/chapter3/3-3.jpg?raw=true)
- 파일을 첨부하는 경우 용량을 줄이기 위해서 압축을 하는데, 이런 전송방법을 콘텐츠코딩이라고 한다.
- 콘텐츠코딩은 엔티티의 정보를 유지한 상태로 압축을 한다.

![](https://github.com/jaeyeon93/jaeyeon93.github.io/blob/master/images/network/chapter3/3-4.jpg?raw=true)

- 엔티티 바디를 분할하는 기능을 청크코딩이라고 한다.
- 청크코딩은 엔티티 바디를 청크(덩어리)로 분해를 한다.

#### 멀티파트

- 메일의 경우에는 여러개의 파일을 첨부를 한다. 텍스트, 영상, 이미지 등
- HTTP는 멀티파트에 대응을 하고 있어서 하나의 메세지 바디 내부에 여러 엔티티를 포함시켜 보낼 수 있다.
- mulipart/form-data : Web폼으로부터 파일 업로드에 사용
- multipart/byteranges : 상태코드 206 리스폰스 메세지가 복수 범위의 내용을 포함하는 때에 사용

```
//multipart/form-data
Content-Type: multipart/form-data; boundary=AaB03x
--AaBo3x
Content-Disposition: form-data; name="field1"

Joe Blow
--AaB03x
Content-Disposition: form-data; name="pics"; filename="file1.txt"
Content-Type: text/plain

... (file1.txt데이터) ...
--AaB03x
```

```
//multiplart/byteranges
HTTP/1.1 206 Parital Content
Date: Fri, 13 Jul 2012 02:45:26 GMT
Last-Modified: Fri, 31 Aug 2007 02:02:20 GMT
Content-Type: multipart/byteranges; boundary=THIS_STRING_SEPARATES

--THIS_STRING_SEPARATES
Content-Type: application/pdf
Content-Range: bytes 500-900/8000

...(지정한 범위 데이터)...
--THIS_STRING_SEPERATES
Content-Type: application/pdf
Content-Range: bytes 7000-7999/8000

...(지정한 범위 데이터)...
--THIS_STRING_SEPERATES--

```
- HTTP로 멀티파트를 사용할 때는 Content-Type헤더 필드를 사용한다.
- 각각의 엔티티를 구분하기 위해서 boundary문자열을 사용
- 각 엔티티의 선두에는 boundary 문자열 앞에 -- 를 삽입한다. ex) --AaB03x, --THIS_STRING_SEPERATES
- 멀티파트 마지막에는 "--THIS_STRING_SEPERATES--" 위의 문자열 마지막 부분에 --를 삽입해서 마무리를 한다.

##### 일부분만 받는 레인지리퀘스트


![](https://github.com/jaeyeon93/jaeyeon93.github.io/blob/master/images/network/chapter3/3-5.jpg?raw=true)

- 예전에는 인터넷의 속도가 느려서 다운로드 중간에 끊기면 처음부터 다시 다운로드를 받아야했다. 이러한 문제를 해결하기 위해서 리줌(resume)이라는 기능이 생겼다. 리줌을 통해서 이전에 다운로드를 한 곳에서부터 다운로드를 재개할 수 있다.
- 이 기능을 구현하기 위해서 엔티티의 범위를 지정해서 다운로드를 해야한다.
- 범위를 지정하여 리퀘스트 하는 것을 레인지리퀘스트라고 한다.


### 콘텐츠 네고에이션

![](https://github.com/jaeyeon93/jaeyeon93.github.io/blob/master/images/network/chapter3/3-6.jpg?raw=true)

- 같은 콘텐츠이지만 여러개의 페이지를 지닌 웹페이가 있다. 이러한 경우는 언어가 다를 때 많이 사용한다.
- 브라우저가 같은 URI에 액세스할 때에 각각 영어판, 한국어판 웹페이지를 표시한다. 이와 같은 구조를 콘텐츠 네고시에이션이라고 한다.
- 콘텐츠 네고시에이션이란 클라이언트와 서버가 제공하는 리소스의 내용에 대해 교섭하는 것이다.

#### 콘텐츠 네고에이션의 판단기준

1. Accpet
2. Accept-Charset
3. Accept-Encoding
4. Accept-Language
5. Content-Language

#### 콘텐츠 네고시에이션의 종류

##### 1. 서버 구동형 네고시에이션 : 서버 측에서 리퀘스트 헤더 필드의 정보를 참고해서 자동적으로 처리

##### 2. 에이전트 구동형 네고시에이션 : 클라리언트 측에서 콘텐츠 네고시에이션을 하는 구조. 유저가 수동으로 선택한다.

##### 3. 트랜스페이런트 네고시에이션 : 서버와 클라이언트가 각각 콘텐츠 네고시에이션을 하는 방식
