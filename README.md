# Sehee

## 🎞 Javascript 면접 대비

### 💊 parsing은 무엇인가요?
- 텍스트 문서를 토큰으로 분해하여 트리 구조의 자료구조의 파스 트리를 생성하는 과정
- 토큰들의 집합을 구문 분석하여 AST(추상적 구문 트리)를 생성

### 💊 요소 노드를 취득하는 여러 방법을 말하고 그 차이를 설명하세요.

### 💊 Node.prototype.textContent와 Node.prototype.innerText의 차이를 설명하세요.

### 💊 바닐라JS로 복수 노드를 생성 후 추가한다고 할 때 어떻게 하는게 좋을지 설명해주세요.

### 💊 www.naver.com 을 주소창에 입력하고 엔터를 누른뒤 브라우저 화면에 내용이 출력될때까지 브라우저 내부에서 일어나는 작업 과정을 설명해주세요.
- 브라우저 렌더링은 HTML, CSS, JavaScript 등의 웹 페이지 자원을 브라우저가 화면에 그리는 과정
- 브라우저 렌더링 원리와 순서는 크게 다음과 같은 단계로 구성
- 먼저 DOM을 생성하고, HTML 문서를 파싱하여 DOM 트리를 생성
- 이때, HTML 태그를 노드로 변환하고, 노드간의 계층 관계를 형성
- 두 번째로 CSSOM을 생성, 브라우저는 CSS 파일을 파싱하여 CSSOM 트리를 생성
- 이때, CSS 속성을 노드로 변환하고, 노드간의 계층 관계를 형성
- 세 번째로 DOM트리와 CSSOM을 결합하여 렌더 트리를 생성
- 이때, 실제 화면에 표시될 요소만을 선택하여 렌더 트리를 형성
- 브라우저는 렌더 트리를 이용하여 각 요소의 크기와 위치를 계산하는 과정인 레이아웃을 거쳐 화면에 요소를 그리는 페인팅 과정
- 이때, 요소의 배경, 테두리, 글자 등을 그림

### 💊 http 프로토콜에 대해 설명해주세요.
- HTTP(HyperText Transfer Protocol)
- HTTP/1.1는 커넥션 당 하나의 요청과 응답만 처리
- HTTP/2.0는 커넥션 당 여러 개의 요청과 응답 가능, 여러 리소스의 동시 전송이 가능해서 HTTP/1.1에 비해 페이지 로드 속도가 약 50% 정도 빠르다고 알려져 있음

### 💊 리플로우와 리페인트에 대해 설명해주세요.
- 리플로우와 리페인트는 요소가 시각적으로 변경되었을 때, 변화를 계산하여 화면에 그려주는 작업
-  DOM이 시각적으로 변경되면 리플로우가 발생하여 렌더트리를 재생성하고, 생성된 렌더트리를 기반으로 요소를 화면에 그리는 리페인트가 발생
- `리플로우`는 요소의 너비, 높이, 위치 등이 변경되어 렌더트리를 재생성(비용이 큰 작업)
- `리페인트`는 변경된 요소를 화면에 그려주는 작업

### 💊 html script 태그의 async/defer 어트리뷰트의 차이점을 설명해주세요.

### 💊 HTMLCollection과 NodeList의 차이점에 대해 설명해주세요.
- HTMLCollection과 NodeList는 모두 유사 배열 객체이면서 이터러블, 둘 다 length 프로퍼티를 가지므로 객체를 배열처럼 접근할 수 있고 반복문을 돌 수 있음
- 그러나 유사 배열 객체이기 때문에 자바스크립트에서 제공하는 배열 객체의 메소드는 사용 불가능
- NodeList는 HTMCollection과 다르게 NodeList.prototype.forEach 메서드를 상속받아 사용 가능

### 💊 XSS란 무엇인가요? XSS의 공격 방식, 해결법 등을 간략히 설명해주세요.
- XSS(Cross Site Scripting), 웹 사이트의 관리자가 아닌 악의적인 목적을 가진 제 3자가 악성 스크립트를 삽입하여 의도하지 않은 명령을 실행시키거나 세션 등을 탈취할 수 있는 취약점
- XSS 공격을 막기 위해서는 입력 값들을 유효성 검증하고, 특수문자들을 제외하는 정규식을 통해서 제거
- 서버에서 CSP(Content-Security-Policy)정책을 설정하여, 허용된 스크립트만 실행되도록 제한
- HTTP 대신에 신뢰할 수 있는 HTTPS를 사용하여 통신 프로토콜을 암호화

### 💊 트리 자료구조에 대해 설명해주세요.
- 트리 (Tree)란 노드들이 나무 가지처럼 연결된 비선형 계층적 자료구조
- 데이터를 순차적으로 저장하지 않기 때문에 비선형 자료구조, 재귀적 자료구조
- 노드 간에 부모 자식 관계를 갖고 있는 계층형 자료구조이며 모든 자식 노드는 하나의 부모 노드만 가짐

### 💊 DOM이란 무엇인가요?
- DOM(Document Object Model), tree 형식의 자료구조
- 이 객체 모델은 문서 내의 모든 요소를 정의하고, 각각의 요소에 접근하는 방법을 제공

### 💊 자바스크립트의 객체지향 방식을 설명해주세요.

### 💊 프로토타입에 대해 아는대로 설명해주세요.

### 💊 일급 객체란 무엇인가요?
- 일급객체(First-class Object), 다른 객체들에 일반적으로 적용 가능한 연산을 모두 지원하는 객체
- 무명의 리터럴로 생성 가능, 런타임에 생성 가능
- 변수나 자료구조(객체, 배열 등)에 저장 가능
- 함수의 매개변수에 전달 가능
- 함수의 반환값으로 사용 가능

### 💊 호이스팅에 대해 설명해주세요.

- 호이스팅은 변수와 함수 선언이 스코프(scope)의 최상단으로 올려져 실행되는 것을 의미
- 이로 인해 선언 전에 사용하는 경우 오류가 발생할 가능성 존재

### 💊 클로저(closure)에 대해 설명해주세요.

- 함수가 선언될 때 자동으로 생성되는 렉시컬 환경에 대한 설명
- 이러한 렉시컬 환경은 스코프 체인(scope chain)을 형성하게 되는데, 스코프 체인은 함수가 선언될 때의 모든 변수와 함수를 포함하는 렉시컬 스코프(lexical scope)를 형성
- 외부 함수가 실행 되고 반환된 후에도 외부 함수의 범위 내의 함수에 체이닝을 할 수 있는 함수
- 정보를 은닉하기 위해서 주로 사용