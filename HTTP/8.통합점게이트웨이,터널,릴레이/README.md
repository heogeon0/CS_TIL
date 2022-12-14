이 글은 'HTTP 완벽가이드'라는 책을 읽으면서 제가 이해한 내용을 바탕으로 정리한 글입니다. 
혹시 이 글을 보게 되신다면 비판적인 시각으로 보시는 것을 추천 드립니다.

<img src="http://image.yes24.com/goods/15381085/XL" alt="HTTP 완벽 가이드" style="zoom:67%;" />

---

## 통합점 : 게이트웨이, 터널, 릴레이

- **학습내용**
  - 게이트웨이 : 서로 다른 프로토콜과 애플리케이션 간의 HTTP 인터페이스다
  - 애플리케이션 인터페이스 : 서로 다른 형식의 웹 애플리케이션이 통신하는데 사용
  - 터널 : HTTP 커넥션을 통해 HTTP가 아닌 트래픽을 전송하는데 사용
  - 릴레이 : 단순한 HTTP 프락시로, 한번에 한 개의 홉에 데이터를 전달하는데 사용



#### 1.  게이트웨이

- 웹에 더 복잡한 리소스를 올려야 할 필요가 생기며, 모든 리소스를 한 개의 애플리케이션으로 처리 불가해짐
- 인터프리터 같이 리소스를 받기 위한 경로를 안내하는 역할을 하는 게이트웨이를 고안 해냄
- 게이트웨이는 리소스와 애플리케이션을 연결



1. **클라이언트 측 게이트웨이와 서버 측 게이트웨이**
   - 웹 게이트웨이는 한쪽에서는 HTTP로 통신하고 다른 한쪽에서는 HTTP가 아닌 다른 프로토콜로 통신
     - 서버 측 게이트웨이는 클라이언트와 HTTP로 통신하고, 서버와는 외래 프로토콜로 통신
     - 클라이언트 측 게이트웨이는 클라이언트와 외래 프로토콜로 통신하고, 서버와는 HTTP로 통신



#### 2. 프로토콜 게이트웨이

1. **HTTP/*서버 측 웹 게이트웨이**
   - 서버 측 웹 게이트웨이는 클라이언트로부터 HTTP 요청이 원 서버 영역으로 들어오는 시점에 클라이언트 측의 HTTP 요청을 외래 프로토콜로 전환

2. **HTTP/HTTPS : 서버 측 보안 게이트웨이**
   - 기업 내부의 모든 웹 요청을 암호화함으로써 개인 정보 보호와 보안을 제공하는데 게이트웨이를 사용
   - 클라이언트는 일반 HTTP를 사용하여 웹을 탐색 할 수 있지만, 게이트웨이는 자동으로 사용자의 모든 세션을 암호화
3. **HTTPS/HTTP : 클라이언트 측 보안 가속 게이트웨이**
   - HTTPS/HTTP 게이트웨이는 보안 가속기로 유명
   - HTTPS/HTTP 게이트웨이는 웹 서버의 앞단에 위치하고, 보이지 않는 인터셉트 게이트웨이나 리버스 프락시 역할을 함
   - 원 서버보다 더욱 효율적으로 보안 트래픽을 복호화하는 하드웨어를 내장하여 원 서버의 부하를 줄여줌



#### 3. 리소스 게이트웨이

- 게이트웨이의 가장 일반적인 형태인 애플리케이션 서버는 목적지 서버와 게이트웨이를 한 개의 서버로 결함
- 게이트웨이를 통해야 받을 수 있는 리소스 요청이 들어오면, 서버는 헬퍼 애플리케이션을 생성하여 요청 처리
- 헬퍼 애플리케이션은 필요한 데이터를 전달 받음



1. **공용 게이트웨이 인터페이스**
   - 공용 게이트웨이 인터페이스(CGI)는 최초의 서버 확장이자 지금까지도 널리 쓰이는 서버 확장
   - 웹에서 동적인 HTML, 신용카드 처리, 데이터베이스 질의 등을 제공하는 데 사용



#### 4. 애플리케이션 인터페이스와 웹 서비스

#### 5. 터널

- 웹 터널은 HTTP 프로토콜을 지원하지 않는 애플리케이션에 HTTP 애플리케이션을 사용해 접근하는 방법 제공
- 웹 터널을 사용하면 HTTP 커넥션을 통해서 HTTP가 아닌 트래픽을 전송할 수 있고, 다른 프로토콜을 HTTP 위에 올릴 수 있음



1. **CONNECT로 HTTP 터널 커넥션 맺기**

   - 웹 터널은 HTTP의 CONNECT 메서드를 사용하여 커넥션을 맺음

   - CONNECT 메서드는 터널 게이트웨이가 임의의 목적 서버와 포트에 TCP 커넥션을 맺고 클라이언트와 서버 간에 오는 데이터를 무조건 전달하기 요청

   1. **CONNECT 요청**
   2. **CONNECT 응답**

2. **데이터 터널링, 시간, 커넥션 관리**

   - 터널을 통해 전달되는 데이터는 게이트웨이에서 볼 수 없어서, 게이트웨이는 패킷의 순서나 흐름에 대한 어떤 가정도 할 수 없음
   - 터널이 일단 연결되면, 데이터는 언제 어디로든 흘러갈 수 있음
   - 클라이언트는 성능을 높이기 위해 CONNECT 요청을 보낸 다음, 응답을 받기 전에 터널 데이터를 전송할 수 있음

3. **SSL 터널링**

   - SSL 같이 암호화된 프로토콜은 정보가 암호화되어 있기 떄문에 낡은 프락시에서는 처리되지 않음
   - 터널을 사용하면 SSL트래픽을 HTTP 커넥션으로 전송하여 80 포트의 HTTP만을 허용하는 방화벽을 통과할 수 있음
   - 이는 보안 SSL 트래픽이 방화벽을 통과하는 데 유용하게 사용될 수 있음.

4. **SSL 터널링 vs HTTP/HTTPS 게이트웨이**

   - HTTPS 프로토콜은 다른 프로토콜과 같은 방식으로 게이트웨이를 통과할 수 있음
   - 원격 HTTPS 서버와 SSL 세션을 시작하는 게이트웨이를 두고 클라이언트 측의 HTTPS 트랜잭션을 수행하는 방식 
   - 응답은 프락시가 받아서 복호화하고 난 후에, HTTP를 통해 클라이언트로 전송
   - 위 방식의 단점
     - 클라이언트-게이트웨이 사이에는 보안이 적용되지 않는 일반 HTTP 커넥션이 맺어져 있음
     - 프락시가 인증을 담당하고 있기 때문에, 클라이언트는 원격 서버에 SSL 클라이언트 인증을 할 수 없음
     - 게이트웨이는 SSL을 완벽히 지원해야함
   - SSL 터널링을 사용하면, 프락시에 SSL을 구현할 필요 없이 SSL 요청은 웹 서버간에 생성



#### 6. 릴레이

- HTTP 릴레이는 HTTP 명세를 완전히 준수하지는 않는 간단한 HTTP 프락시
- 모든 헤더와 메서드 로직을 수행하지 않고 맹목적으로 트래픽을 전달하는 간단한 프락시를 구현하는 방식이 유용할 때가 있음
- 데이터를 맹목적으로 전달하도록 구현하기는 쉽기 때문에, 단순 필터링이나 진단 혹은 콘텐츠 변환을 하는데 사용

