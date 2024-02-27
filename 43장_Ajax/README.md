## 🗂️ 43장. Ajax

### 목차
- [43.1 Ajax란?](#43.1)
- [43.2 JSON](#43.2)
- [43.3 XMLHttpRequest](#43.3)
<br />

### 43.1 Ajax란?<a name="43.1"></a>
---
#### ✅ `Ajax(Asynchronous JavaScript and XML)`: 자바스크립트를 사용하여 `브라우저가 서버에게 비동기 방식으로 데이터를 요청`하고, 서버가 응답한 데이터를 수신하여 `윂페이지를 동적으로 갱신`하는 프로그래밍 방식

- 브라우저 호스트 Web API인 `XMLHttpRequest` 객체 기반으로 동작
- `XMLHttpRequest`: HTTP 비동기 통신을 위한 메서드와 프로퍼티 제공

➡️ 서버로부터 웹페이지의 변경에 필요한 데이터만 비동기 방식으로 전송받아 웹페이지를 변경할 필요가 없는 부분은 다시 렌더링하지 않고, 변경할 필요가 있는 부분만 한정적으로 렌더링하는 방식이 가능해짐

### 43.2 JSON<a name="43.2"></a>
---
#### ✅ JSON(JavaScript Object Notation): 클라이언트와 서버 간의 HTTP 통신을 위한 텍스트 데이터 포맷

#### 43.2.1 JSON 표기 방식
- JSON은 자바스크립트의 객체 리터럴과 유사하게 `키`와 `값`으로 구성된 순수한 텍스트
- 키는 반드시 **큰따옴표**로 묶고 문자열에도 큰따옴표 사용

#### 43.2.2 JSON.stringify
- 객체/배열을 JSON 포맷의 문자열로 변환
- `직렬화(Serializing)`: 클라이언트가 서버로 객체를 전송하기 위해 객체를 문자열화하는 것

#### 43.2.3 JSON.parse
- JSON 포맷의 문자열을 객체로 변환
- 서버로부터 클라이언트에게 전송된 JSON 데이터는 문자열
- `역직렬화(Deserializing)`: 문자열을 객체로서 사용하기 위해 JSON 포맷의 문자열을 객체화하는 것

### 43.3 XMLHttpRequest<a name="43.2"></a>
---

#### 43.3.1 XMLHttpRequest 객체 생성
#### ✅ XMLHttpRequest 생성자 함수 호출하여 생성
- 브라우저 환경에서만 정상적으로 실행됨 (XMLHttpRequest 객체는 Web API)

```js
//XMLHttpRequest 객체의 생성
const xhr = new XMLHttpRequest();
```

#### 43.3.2 XMLHttpRequest 객체의 프로퍼티와 메서드
#### ✅ XMLHttpRequest 객체의 프로토타입 프로퍼티

`readyState`: HTTP 요청의 현재 상태 나타내는 정수 (XMLHttpRequest 정적 프로퍼티를 값으로 가짐)
     
  - UNSENT: 0
  - OPENED: 1
  - HEADERS_RECEIVED: 2
  - LOADING: 3
  - DONE: 4

`status`: HTTP 요청에 대한 응답 상태(HTTP 상태 코드)를 나타내는 정수 ex. 200

`statusText`: HTTP 요청에 대한 응답 메시지 나타내는 문자열 ex. "OK"

`responseType`: HTTP 응답 타입 ex. document, json, text, blob, arraybuffer

`response` : HTTP 요청에 대한 reponse body (responseType에 따라 타입 다름)

`responseText` : 서버가 전송한 HTTP 요청에 대한 응답 문자열

#### ✅XMLHttpRequest 객체의 이벤트 핸들러 프로퍼티

`onreadystatechange` : readyState 프로퍼티 값이 변경된 경우

`onloadstart` : HTTP 요청에 대한 응답 받기 시작한 경우

`onprogress` : HTTP 요청에 대한 응답을 받는 도중 주기적으로 발생

`onabort` : abort 메서드에 의해 HTTP 요청이 중단된 경우

`onerror` : HTTP 요청에 에러가 발생한 경우

`onload` : HTTP 요청이 성공적으로 완료한 경우

`ontimeout` : HTTP 요청 시간이 초과한 경우

`onloadend` : HTTP 요청이 완료한 경우 (HTTP 요청 성공/실패 시 발생)

#### ✅ XMLHttpRequest 객체의 메서드

`open` : HTTP 요청 초기화

`send` : HTTP 요청 전송

`abort` : 이미 전송된 HTTP 요청 중단

`setRequestHeader` : 특정 HTTP 요청 헤더의 값을 설정

`getResponseHeader` : 특정 HTTP 요청 헤더의 값을 문자열로 변환

#### ✅ XMLHttpRequest 객체의 정적 프로퍼티

- UNSENT: 0, open 메서드 호출 이전
- OPENED: 1, open 메서드 호출 이후
- HEADERS_RECEIVED: 2, send 메서드 호출 이후
- LOADING: 3, 서버 응답 중 (응답 데이터 미완성 상태)
- DONE: 4, 서버 응답 완료

#### 43.3.3 HTTP 요청 전송

#### ✅ XMLHttpRequest.prototype.open 메서드

➡️ HTTP 요청 초기화

```js
xhr.open(method, url[, async])
```

`method` : HTTP 요청 메서드

- GET: index/retrieve, 모든/특정 리소스 취득, 페이로드 X
- POST: create, 리소스 생성, 페이로드 O
- PUT: replace, 리소스 전체 교체, 페이로드 O
- PATCH: modify, 리소스 일부 수정, 페이로드 O
- DELETE: delete, 모든/특정 리소스 삭제, 페이로드 X
        
`url`: HTTP 요청 전송할 URL

`async` : 비동기 요청 여부, 옵션 - 기본값 true

#### ✅ XMLHttpRequest.prototype.setRequestHeader 메서드

➡️ 필요에 따라 특정 HTTP 요청의 헤더 값 설정, 반드시 open 메서드 호출 이후에 호출

➡️ 자주 사용하는 HTTP 요청 헤더

`Content-type`: request body에 담아 전송할 데이터의 MIME 타입의 정보 표현
- text, application, multipart

`Accept`: 서버가 응답할 데이터의 MIME 타입 지정, Accept 헤더를 설정하지 않으면 send 메서드가 호출될 때 Accept 헤더가 */*으로 전송

#### ✅ XMLHttpRequest.prototype.send 메서드

➡️ open 메서드로 초기화된 HTTP 요청을 서버에 전송

- GET 요청 메서드; 데이터를 URL의 일부분인 쿼리 스트링으로 서버에 전송
- POST 요청 메서드; 데이터(페이로드)를 request body에 담아 전송

> 페이로드가 객체인 경우 반드시 JSON.stringify 메서드를 사용하여 직렬화한 다음 전달

#### 43.3.4 HTTP 응답 처리
➡️ 서버가 전송한 응답 처리 위해 XMLHttpRequest 객체가 발생시키는 이벤트 캐치해야 함
- onreadystatechange 이벤트 캐치
- onload 이벤트 캐치