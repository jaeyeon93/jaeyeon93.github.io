## HTTP메소드 정리

### 메소드를 사용해서 지시를 내리다
![](/Users/jaeyeonkim/Desktop/blog/images/network/chapter2/chapter2_5.jpeg)
- 메소드는 리소스에 어떠한 행동을 하기 원하는지를 지시하기 위해 존재한다.
- 리퀘스트 URI로 지정한 리소스에 리퀘스트를 보내는 경우 메소드를 통해서 명령을 매린다.

#### HTTP/1.0과 HTTP/1.1에서 지원하고 있는 메소드들
![](/Users/jaeyeonkim/Desktop/blog/images/network/chapter2/chapter2_6.jpeg)

#### 지속연결
![](/Users/jaeyeonkim/Desktop/blog/images/network/chapter2/chapter2_7.jpeg)
#### HTTP초기버전에서는 통신을 한번 할 때마다 TCP에 의해 연결을 종료했었다. HTML파일에 점점 많은 리소스들이 들어가자 여러번 연결을 통해서 통신량을 늘어나게 되는 경우가 발생을 했다. 그래서 지속연결을 수행한다.
- 지속연결은 어느 한 쪽이 명시적으로 연결을 종료하지 않는 이상 TCP연결을 계속 유지한다.
- 지속연결을 하면 TCP커넥션의 연결과 종료가 반복되는 오버헤드를 줄여주기 때문에 서버에 대한 부하가 줄어든다. 또 오버헤드를 줄인 만큼 HTTP리퀘스트와 리스폰스가 빠르게 완료가되서 페이지가 빨리 표시된다.

#### 파이프라인화
![](/Users/jaeyeonkim/Desktop/blog/images/network/chapter2/chapter2_8.jpeg)
- 지속연결은 여러 리퀘스트를 보낼 수 있도록 파이프라인(HTTP pipelining)화를 가능하게 한다.
- 이전에는 리퀘스트 송신 후에 리스폰스를 수신할 때까지 대기를 했다.
- 파이프라인화를 통해서 리스폰스를 기다리지 않고 바로 다음 리퀘스트를 보낼 수 있게 되었다.

#### 쿠키를 통한 상태관리
![](/Users/jaeyeonkim/Desktop/blog/images/network/chapter2/chapter2_9.jpeg)
- 리스폰스와 리퀘스트는 이전의 상태를 관리하지 않는다.
- 쿠키는 리퀘스트와 리스폰스에 쿠키 정보를 추가해서 클라이언트 상태를 파악하기 위한 시스템이다.
- 쿠키는 서버에서 리스폰스로 보내진 Set-Cookie라는 헤더 필더에 의해 쿠키를 클라이언트에 보존하게 된다. 다음 번에 클라리언트가 같은 서버로 리퀘스트를 보낼 때, 자동으로 쿠키값을 넣어서 송신을 한다. 서버는 쿠키의 상태를 통해서 이전 상태를 알 수 있다.

##### 1.리퀘스트(쿠키가 없는 상태)
```
GET /reader/ HTTP /1.1
Host: www.helloworld.com
// 헤더 필드에 쿠키가 없다
```

##### 2. 리스폰스(서버가 쿠키를 발행)
```
HTTP /1.1 200 OK
Date: Thu, 12 Jul 2017 07:12:20 GMT
Server : Apache
<Set-Cookie: sid=1342077140226724; path=/;expires=Wed, => 10-Oct-12 07:12:20 GMT>
Content-Type: text/plain; charset=UTF-8
```

##### 3. 리퀘스트(보관하던 쿠키를 자동 송신)
```
GET /image/ HTTP /1.1
Host: www.hellowrld.com
Cookie: sid=1342077140226724
```
