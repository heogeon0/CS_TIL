이 글은 'HTTP 완벽가이드'라는 책을 읽으면서 제가 이해한 내용을 바탕으로 정리한 글입니다. 
혹시 이 글을 보게 되신다면 비판적인 시각으로 보시는 것을 추천 드립니다.

<img src="http://image.yes24.com/goods/15381085/XL" alt="HTTP 완벽 가이드" style="zoom:67%;" />

---

## 캐시

- 웹 캐시는 자주 쓰이는 문서의 사본을 자동으로 보관하는 HTTP 장치다
- 불필요한 데이터 전송을 줄여서, 네트워크 요금으로 인한 비용을 줄여줌
- 네트워크 병목을 줄여줌



#### 1. 불필요한 데이터 전송

- 데이터 전송은 값 비싼 네트워크 대역폭을 잡아먹고, 전송을 느리게 만들며, 웹 서버에 부하를 줌
- 캐시를 활용하면 서버 응답은 캐시에 보관되며, 캐시된 사본이 뒤이은 요청들에 대한 응답으로 사용될 수 있기 때문에, 원 서버가 중복해서 트래픽을 주고받는 낭비가 줄어듬



#### 2. 적중과 부적중

- 캐시 적중 : 캐시에 요청이 도착했을 때, 만약 그에 대응하는 사본이 있다면 그를 이용해 요청이 처리

- 캐시 부적중 : 대응하는 사본이 없다면 그냥 원 서버로 전달

  

1. **재검사**
   - 원 서버 콘텐츠는 변경될 수 있기 때문에, 캐시는 반드시 그들이 갖고 있는 사본이 여전히 최신인지 서버를 통해 점검해야함
   - HTTP는 서버로부터 전체 객체를 가져오지 않고도 콘텐츠가 여전히 신선한지 빠르게 검사할 수 있는 특별한 요청을 정의했음
   - 대부분의 캐시는 클라이언트가 사본을 요청하였으며 그 사본이 검사를 할 필요가 있을 정도로 충분히 오래된 경우 재검사
   - 재검사 시 콘텐츠가 변경되지 않았다면, 서버는 아주 작은 304 Not Modified 응답을 보냄. 그 사본이 여전히 유효함을 알게 된 캐시는 즉각 사본이 신선하다고 임시로 다시 표시한 뒤 사본을 클라이언트에 제공
   - 재검사를 위해 가장 많이 쓰이는 해더는 If-Modified-Since 헤더임
     - GET 요청에 이 헤더를 추가하면 캐시된 시간 이후에 변경된 경우에만 사본을 보내달라는 의미가 됨
   - GET If-Modified-Since 요청이 서버에 도착한 경우 일어나는 일
     - 재검사 적중
       - 객체가 변경되지 않았다면 HTTP 304 Not Modified 응답을 보냄
     - 재검사 부적중
       - HTTP 200 OK 응답을 클라이언트에게 보내며, 콘텐츠 전체를 함께 보냄
     - 객체 삭제
       - 만약 서버 객체가 삭제 되었다면, 서버는 404 Not Found 응답을 돌려보내며, 캐시는 사본을 삭제
2. **적중률**
   - 바이트 적중률
     - 캐시를 통해 제공된 모든 바이트의 비율
   - 적중과 부적중의 구별
     - Date 헤더를 이용
     - Age 헤더 이용



#### 3. 캐시 토폴로지

- 개인 전용 캐시 : 한 명에게만 할당된 캐시
- 공용 캐시 : 공유된 캐시



1. **개인 전용 캐시**
   - 개인 전용 캐시는 많은 에너지나 저장 공간을 필요로 하지 않으므로, 작고 저렴할 수 있음
   - 웹 브라우저는 개인 전용 캐시를 내장하고 있음
   - 대부분의 브라우저는 자주 쓰이는 문서를 개인용 컴퓨터의 디스크와 메모리에 캐시해 놓고, 사용자가 캐시 사이즈와 설정을 수정할 수 있도록 허용
2. **공용 프락시 캐시**
   - 공용 캐시는 캐시 프락시 서버 혹은 더 흔히 프락시 캐시라고 불리는 특별한 종류의 공유된 프락시 서버
   - 프락시 캐시는 로컬 캐시에서 문서를 제공하거나, 혹은 사용자의 입장에서 서버에 접근
   - 여러 사용자가 접근하기 때문에, 불필요한 트래픽을 줄일 수 있는 더 많은 기회가 있음
3. **프락시 캐시 계층**
   - 작은 캐시에서 캐시 부적중이 발생했을 때 더 큰 부모 캐시가 그 '걸러 남겨진' 트래픽을 처리하도록 하는 계층을 만드는 방식이 합리적인 경우가 많음
4. **캐시망, 컨텐츠 라우팅, 피어링**
   - 캐시망의 프락시 캐시는 복잡한 방법으로 서로 대화하여, 어떤 부모 캐시와 대화할 것인지, 아니면 요청이 캐시를 완전히 우회해서 원 서버로 바로 가도록 할 것인지에 대한 캐시 커뮤니케이션 결정을 동적으로 내림
   - 복잡한 캐시 사이의 관계는, 서로 다른 조직들이 상호 이득을 위해 그들이 캐시를 연결하여 서로를 찾아 볼 수 있도록 해줌
   - 선택적인 피어링을 지원하는 캐시는 형제 캐시라고 함
     - HTTP 는 형제 캐시를 지원하지 않음



#### 4. 캐시 처리 단계

1. **요청 받기**
   - 캐시는 네트워크로 부터 도착한 요청 메시지를 읽음
2. **파싱**
   - 캐시는 메시지를 파싱하여 URL과 헤더들을 추출
3. **검색**
   - 캐시는 로컬 복사본이 있는지 검사하고, 사본이 없다면 사본을 받아옴
4. **신선도 검사**
   - 캐시는 캐시된 사본이 충분히 신선한지 검사, 신선하지 않는다면 변경사항이 있는지 물어봄
5. **응답생성**
   - 캐시는 캐시된 응답을 원 서버에서 온것처럼 보이고 싶기 때문에, 서버 응답 헤더를 토대로 응답생성
   - 캐시는 클라이언트에 맞게 헤더를조정
     - 클라이언트가 HTTP/1.1응답을 기대하는 상황에서 1.0응답을 봔한했다면, 캐시가 이를 조정해야함
   - Date 헤더를 수정하면 안됨
6. **전송**
   - 캐시는 네트워크를 통해 응답을 클라이언트에 돌려줌
7. **로깅**
   - 선택적으로, 캐시는 로그파일에 트랜잭션에 대해 서술한 로그 하나를 남김



#### 5. 사본을 신선하게 유지

- **문서 만료**

  - HTTP는 Cache-Control과 Expires라는 특별한 헤더들을 이용해서 원 서버가 각 문서에 유효기간을 붙일 수 있게 해줌

- **유효기간과 나이**

- **서버 재검사**

  - 캐시가 원 서버에게 문서가 변경되었는지의 여부를 물어볼 필요가 있음을 의미하는 '서버 재검사'라고 부름
    - 재검사 결과 콘텐츠가 변겨오디었다면, 캐시는 그 문서의 새로운 사본을 가져와 오랜된 데이터 대신 저장한 뒤 클라이언트에게도 보내줌
    - 재검사 결과 콘텐츠가 변경되지 않았다면, 캐시는 새 만료일을 포함한 새 헤더들 만 가져와서 캐시안의 헤더들을 갱신

- **조건부 메서드와의 재검사**

  - HTTP는 캐시가 서버에게 '조건부 GET'이라는 요청을 보낼 수 있도록 해줌
  - 이 요청은 서버가 갖고 있는 문서가 캐시가 갖고 있는 것과 다른 경우에만 객체 본문을 보내라고 하는 것
  - 신선도 검사와 객체를 받아오는 것은 하나의 조건부 GET으로 결함
  - 조건부 GET은 GET 요청 메세지에 조건부 헤더를 추가함으로써 시작

- **If-Modified-Since : 날짜 재검사** 

  - IMS 요청은 서버에게 리소스가 특정 날짜 이후로 변경된 경우에만 요청한 본문을 보내달라고함

  -  ````
     If-Modified-Since : <캐시된 마지막 수정일>
     ````

- **If-None-Match : 엔터티 태그 재검사**

  - 캐시가 객체에 대한 여려 개의 사본을 갖고 있는 경우, 그 사실을 서버에게 알리기 위해 하나의 If-None-Match 헤더에 여러 개의 엔터티 태그를 포함 시킬 수 있음

- **약한 검사기와 강한 검사기**

  - 서버는 때때로 모든 캐시된 사본을 무효화시키지 않고 문서를 고칠 수 있도록 허용하고 싶은 경우가 있음
  - HTTP/1.1은 비록, 콘텐츠가 조금 변경되었더라도 "그 정도면 같은 것"이라고 주장할 수 있도록 해주는 '약한 검사기'를 지원

  ```
  ETag : W/"v.26"
  If-None-Match: W/"v2.6"
  ```

  - 강한 엔터티 태그는 대응하는  엔터티 값이 어떻게 바뀌든 매번 반드시 같이 바뀌어야함
  - 약한 엔터티 태그는 대응하는 엔터티에 유의미한 변경이 있을 때마다 같이 변경





#### 6. 캐시 제어

- HTTP는 문서가 만료되기 전까지 얼마나 오랫동안 캐시 될 수 있게 할 것인지 서버가 설정할 수 있는 여러가지 방법 정의
  - Cache-Control : no-store
    - 캐시가 응답의 사본을 만드는 것을 금지
  - Cache-Control : no-cache
    - 로컬 캐시 저장소에 저장할 수 있지만 재검사 하지 않고서는 클라이언트로 제공 할 수 없음
  - Cache-Control : max-age
    - 서버로부터 온 이후로 최대 시간
  - Cache-Control : must-revalidate
    - 성능을 개선하기 위해 신선하지 않은 객체를 제공할 수 있도록 설정가능함
    - 서버에서 이를 막기 위해 만료 정보를 정확히 따르길 원한다면 다음과 같은 헤더 사용
  - Expires
    - 더 이상 사용하지 않기를 권하는 최대 날짜