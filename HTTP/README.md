이 글은 'HTTP 완벽가이드'라는 책을 읽으면서 제가 이해한 내용을 바탕으로 정리한 글입니다. 
혹시 이 글을 보게 되신다면 비판적인 시각으로 보시는 것을 추천 드립니다.

<img src="http://image.yes24.com/goods/15381085/XL" alt="HTTP 완벽 가이드" style="zoom:67%;" />

---





## HTTP 개관

### 1. 웹 클라이언트 서버

- 웹 서버는 HTTP 프로토콜로 의사소통하기 떄문에 보통 HTTP 서버라고 불림
- 웹 서버는 인터넷의 데이터를 저장하고, HTTP 클라이언트가 요청한 데이터를 제공



### 2. 리소스

- 웹 서버는 웹 리소스를 관리하고 제공
- 웹 리소스는 웹 콘텐츠의 원천



#### 1. 미디어 타입

- HTTP는 웹에서 전송되는 객체 각각에 신중하게 MIME 타입이라는 데이터 포맷 라벨을 붙임
  - MIME(Multipurpose Internet Mail Extensions)은 원래 각기 다른 전자메일 시스템 사시에서 메시지가 오갈 때 겪는 문제점을 해결하기 위해 설계하였음
- 웹 브라우저는 서버로부터 객체를 돌려받을 때, 다룰 수  있는 객체인지 MIME 타입을 통해 확인
  - HTML : text/html
  - JPEG : image/jpeg
  - GIF : image/gif

#### 2. URI

- 웹 서버 리소스는 각자 이름을 갖고 있기 때문에, 클라이언트는 관심있는 리소스를 지목할 수 있음
- 서버 리소스 이름은 통합 자원 식별자(uniform resource identifier, URI)라고 불림
- 정보 리소스를 고유하게 식별하고 위치를 지정할 수 있음
- URI에는 URL과 URN이 있음

1. **URL**

   - 통합 자원 지시자

   - 특정 서버의 한 리소스에 대한 구체적인 위치를 서술함

     ```
     http://www.yahoo.com/images/logo.gif
     야후 로고의 URL
     ```

     - URL의 첫 번째 부분은 스킴(scheme)이라고 불리는데, 리소스에 접근하기 위해 사용되는 프로토콜을 서술

     - 두 번째 부분은 서버의 인터넷 주소를 가르킴

     - 마지막은 웹 서버의 리소스를 가르킴

2. **URN**

   - uniform resouce name, 유니폼 리소스 이름
   - URN 은 콘텐츠를 이루는 한 리소스에 대해, 리소스의 위치에 영향 받지 않는 유일무이한 이름 역할
   - 효율적인 동작을 위해 리소스 위치를 분석하기 위한 인프라 지원이 필요한데, 인프라가 부재하기에 기술발전이 늦춰지고 있음



### 3. 트랜잭션

- HTTP 트랜잭션은 요청 명령과 응답 결과로 구성되어 있음
- 이 상호작용은 HTTP 메시지라고 불리는 정형화된 데이터 덩어리를 이용해 이루어짐
- 웹 브라우저는 시각적으로 풍부한 웹 페이지를 가져올 때 대량의 HTTP 트랜잭션을 수행
- '웹페이지'는 보통 하나의 리소스가 아닌 리소스의 모음

#### 1. 메서드

- 모든 HTTP 요청 메시지는 한 개의 메서드를 갖음
- 메서드는 서버에게 어떤 동작을 취해져야 하는지 말해줌

| HTTP 메서드 | 설명                                                         |
| :---------- | ------------------------------------------------------------ |
| GET         | 서버에서 클라이언트로 지정한 리소스를 보내라                 |
| PUT         | 클라이언트에서 서버로 보낸 데이터를 지정한 이름의 리소스로 저장하라 |
| DELETE      | 지정한 리소스를 서버에서 삭제하라                            |
| POST        | 클라이언트 데이터를 서버 게이트웨이 애플리케이션으로 보내라  |
| HEAD        | 지정한 리소스에 대한 응답에서, HTTP 헤더 부분만 보내라       |

#### 2. 상태 코드

- 모든 HTTP 응답 메시지는 상태 코드와 함께 반환



### 4. 메시지

- 웹 클라이언트에서 웹 서버로 보낸 HTTP메시지를 요청 메시지라고 부름
- 서버에서 클라이언트로 가는 메시지는 응답 메시지라고 부름![HTTP 완벽 가이드 3장: HTTP 메시지 :: 금손이 프론트엔드 개발자가 되려고 오늘도 존버중](https://blog.kakaocdn.net/dn/zGcNG/btrsTc02sW6/xWdXUK5lRBvInoNTgz3PPk/img.png)
  - 시작줄
    - 요청 : 무엇을 해야하는지
    - 응답 : 무슨일이 일어났는지
  - 헤더
  - 본문
    - 요청 : 웹 서버로 데이터를 실어 보냄
    - 응답 : 본문은 클라이언트로 데이터를 반환



### 5. TCP 커넥션

#### 1. TCP/IP

- TCP는 다음을 제공
  - 오류 없는 데이터 전송
  - 순서에 맞는 전달 ( 데이터는 언제나 보낸 순서대로 도착한다)
  - 조각나지 않는 데이터 스트림 (언제든 어떤 크기로든 보낼 수 있음)
- TCP / IP는 TCP와 IP가 층을 이루는, 패킷 교환 네트워크 프로토콜 집합
- TCP/IP는 각 네트워크와 하드웨어의 특성을 숨기고, 어떤 종류의 컴퓨터나 네트워크든 서로 신뢰성 있는 의사소통을 하게 해줌

#### 2. 접속, IP 주소 그리고 포트번호

- TCP 커낵션을 맺는 것은 다른 회사 사무실에 있는 누군가에게 전화를 거는 것과 비슷
  - 먼저 회사의 전화번호를 누름 (TCP)
  - 전화를 걸고자 하는 상대방이 쓰는 번호를 누름(포트번호)
- URL을 활용하여 IP주소를 알 수 있으며, 포트번호가 없으면 기본값은 80이다.



### 6. 웹의 구성요소

#### 1.프록시

- 프록시는 클라이언트와 서버 사이에 위치하여, 클라이언트의 모든 HTTP 요청을 받아 서버에 전달
- 주로 보안을 위해 사용하며, 웹 트래픽 흐름 속에서 신뢰할 수 있는 중개자 역할을 함. 또한, 요청과 응답을 필터링함

#### 2. 캐시

- 웹 캐시와 캐시 프록시는 자신을 거쳐 가는 문서들 중 자주 찾는 것의 사본을 저장 해 두는, 특벼한 종류의 HTTP프록시 서버

#### 3. 게이트 웨이

- 게이트웨이는 다른 서버들의 중개자로 동작하는 특별한 서버
- 주로 HTTP 트래픽을 다른 프로토골로 변환하기 위해 사용

#### 4. 터널

- 두 커낵션 사이에서 RAW데이터를 열어보지 않고 그대로 전달해주는 HTTP 애플리케이션
- 비 HTTP 데이터를 하나 이상의 HTTP 연결을 통해 그대로 전송해주기 위해 사용



​	