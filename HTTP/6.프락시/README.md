이 글은 'HTTP 완벽가이드'라는 책을 읽으면서 제가 이해한 내용을 바탕으로 정리한 글입니다. 
혹시 이 글을 보게 되신다면 비판적인 시각으로 보시는 것을 추천 드립니다.

<img src="http://image.yes24.com/goods/15381085/XL" alt="HTTP 완벽 가이드" style="zoom:67%;" />

---

## 프락시

- 웹 프락시 서버는 중개자
- 프락시는 클라이언트와 서버 사이에 위치하여 그들 사이의 HTTP 메시지를 정리하는 중개인처럼 동작



#### 1. 웹 중개자

- 웹 프락시 서버는 클라이언트의 입장에서 트랜잭션을 수행하는 중개인
- 웹 서버이기도 하고 웹 클라이언트이기도 함



1. **개인 프락시와 공유 프락시**
   - 프락시 서버는 하나의 클라이언트가 독점적으로 사용할 수도 있고, 여러 클라이언트가 공유할 수 있음
2. **프락시 대 게이트웨이**
   - 프락시는 같은 프로토콜을 사용하는 둘 이상의 애플리케이션을 연결
   - 게이트웨이는 서로 다른 프로토콜을 사용하는 둘 이상을 연결
   - 상용 프락시는 게이트웨이의 기능도 종종 구현하기 때문에 차이점이 모호함



#### 2. 프락시 사용 이유

- 보안을 개선하고, 성능을 높여주며, 비용 절약 가능
- 모든 HTTP 트래픽을 들여다보고 건드릴 수 있기 때문에, 프락시는 부가적인 가치를 주는 여러 유용한 웹 서비스를 구현하기 위해 트래픽을 감시하고 수정 가능
  - 어린이 필터
  - 문서 접근 제어자
  - 보안 방화벽
  - 웹 캐시
  - 대리 프락시
  - 콘텐츠 라우터
  - 트랜스 코더
  - 익명화 프락시



#### 3. 프락시는 어디에 있는가

- **프락시 서버 배치** 
  - **출구 프락시**
    - 로컬 네트워크와 인터넷 사이를 오가는 트래픽을 제어하기 위해 로컬 프락시 네트워크 출구에 위치
  - **접근 프락시**
    - 고객으로부터의 모든 요청을 종합적으로 처리하기 위해 ISP접근 지점에 위치
      - ISP는 사용자들의 다운로드 속도 개선 및 인터넷 대역폭 비용을 줄이기 위해 많이 찾음
  - **대리 프락시**
    - 네드퉈크의 가장 끝 웹 서버의 바로 앞에 위치
    - 웹 서버로 향하는 모든 요청을 처리하고 필요할 때, 서버에 자원 요청
    -  웹 서버의 이름과 IP주소로 가장하기 때문에, 모든 요청은 이 프락시로 가게됨
  - 네트워크 교환 프락시
    - 캐시를 이용해 인터넷 교차로의 혼잡을 완화하고 트래픽 흐름을 감시하기 위해, 충분한 처리 능력을 갖춘 프락시가 네트워크 사이의 인터넷 피어링 교환 지점들에 놓일 수 있음
- **어떻게 프락시가 트래픽을 처리하는가**
  - **클라이언트 수정**
    - 웹 클라이언트는 수동, 혹은 자동 프락시 설정을 지원
    - 클라이언트는 http 요청을 바로 그리고 의도적으로 원 서버가 아닌 프락시로 보냄
  - **네트워크 수정**
    - 네트워크 인프라를 가로채서 웹 트래픽을 프락시로 가도록 조정
    - 일반적으로 http 트래픽을 지켜보고 가로채어 클라이언트 모르게 트래픽을 프락시로 보내는 스위칭 장치와 라우팅 장치가 필요
    - 인터셉트 프락시라고 부름
  - **DNS 이름공간 수정**
    - 웹 서버 앞에 위치하는 프락시 서버인 대리 프락시는 웹 서버의 이름과 IP 주소를 자신이 직접 사용
  - **웹 서버 수정**
    - 클라이언트 요청을 프락시로 리다이렉트 하도록 설정



#### 4. 클라이언트 프락시 설정

1. **수동 설정**
   - 프락시를 사용하겠다 명시적으로 설정
2. **브라우저 기본 설정**
   - 브라우저 벤더나 배포자는 브라우저를 소비자에게 전달하기 전에 프락시를 미리 설정해 놓을 수 있음
3. **프락시 자동 설정**
   - 자바스크립트 프락시 자동 설정 (PAC) 파일에 대한 URI 를 제공할 수 있음
4. **WPAD 프락시 발견**
   1. 웹 프락시 자동발견 프로토콜을 활용



#### 5. 프락시 요청의 미묘한 특징

1. **프락시 URI는 서버  URI와 다름**
   - 서버로는 부분 URI를, 프락시로는 완전한 URI를 보낼 필요가 있음
2. **가상 호스팅에서 일어나는 같은 문제**
3. **인터셉트 프락시는 부분 URI 를 받음**
   - 클라이언트는 자신이 프락시와 대화하고 있는 것을 항상 알지 못함
   - 인터셉트 프락시는 원서버로 가는 트래픽을 가로채는 역할을 하기 때문에, 부분 URI를 받아서 처리를 해야함
4. **프락시는 프락시 요청과 서버 요청을 모두 다룰 수 있음**
   - 다목적 프락시 서버는 요청 메시지의 완전한 URI와 부분 URI를 모두 지원해야함
5. **전송 중 URI 변경**
6. **URI  클라이언트 자동확장과 호스트 명 분석**
   - 브라우저는 프락시의 존재 여부에 따라 요청 URI를 다르게 분석
7. **프락시가 없는 URI 분석**
   1. 'oreilly'를 uri창에 입력한다면 기본 스킴을 'http://'로, 기본 포트를 '80', 기본 경로를 '/'로 간주
   2. 브라우저는 호스트 'oreilly'를 검색 -> 실패
   3. 호스트 명을 자동 확장 후 DNS에 'www.oreilly.com'의 주소분해를 요청 -> 성공
8. **명시적 프락시를 사용할 때 URI 분석**
   - 프락시가 명시적으로 설정되었으므로, 브라우저는 DNS를 사용해 프락시 서버를 찾음 -> 성공
9. **인터셉트 프락시를 이용한 URI 분석**
   1. 'oreilly'를 uri창에 입력한다면 기본 스킴을 'http://'로, 기본 포트를 '80', 기본 경로를 '/'로 간주
   2. 브라우저는 호스트 'oreilly'를 검색 -> 실패
   3. 호스트 명을 자동 확장 후 DNS에 'www.oreilly.com'의 주소분해를 요청 -> 성공
   4. 인터셉트 프락시를 통해 첫 번째 시도는 원서버가 아닌 프락시 서버에 의해 종료됨 -> 클라이언트는 웹 서버와 대화했다고 믿지만, 웹 서버는 살아 있지 않음
   5. 프락시가 원 서버와 상호작용할 준비가 되었을 때, 이미 죽은 IP주소가 아닌 다른 IP주소를 시도하여 연결



#### 6. 메시지 추적

1. **Via 헤더**
   - 메시지가 지나는 각 중간 노드 (프락시나 게이트웨이)의 정보를 나열
2. **TRACE 메서드**
   - 요청 메시지를 프락시의 연쇄를 따라가면서 어떤 프락시를 지나가고 어떻게 각 프락시가 요청 메시지를 수정하는지 관찰/추척 할 수 있도록 해줌
   - TRACE 응답이 도착 했을 때, 클라이언트는 서버가 받은 메시지와 그 메시지가 지나간 프락시들의 목록을 검사할 수 있음



#### 7. 프락시 상호 운용성

1. **지원하지 않는 헤더와 메서드 다루기**

   - 프락시는 이해할 수 없는 헤더 필드는 반드시 그래도 전달해야함
   - 같은 이름의 헤더 필드가 여러 개 있는 경우에는 그들의 상대적인 순서도 유지해야함
   - 지원하지 않는 메서드를 통과시킬 수 없는 프락시는 대부분에 네트워크에서 살아남지 못함

2. **OPTIONS : 어떤 기능을 지원하는지 알아보기**

   
