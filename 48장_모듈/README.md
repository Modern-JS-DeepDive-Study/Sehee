## 🗂️ 48장. 모듈

### 목차
- [48.1 모듈의 일반적 의미](#48.1)
- [48.2 자바스크립트와 모듈](#48.2)
- [48.3 ES6 모듈(ESM)](#48.3)
<br />


### 48.1 모듈의 일반적 의미<a name="48.1"></a>
---
#### ✅ 모듈: `애플리케이션을 구성하는 개별적 요소`로서 재사용 가능한 코드 조각

- 일반적으로 모듈은 `기능`을 기준으로 파일 단위로 분리하는데 이때 모듈이 성립하려면 자신만의 `파일 스코프(모듈 스코프)`를 가질 수 있어야 함

#### ✅ 캡슐화 상태: 자신만의 파일 스코프를 갖는 모듈의 자산(모듈에 포함되어 있는 변수, 함수, 객체 등)은 기본적으로 `비공개 상태`

- 다른 모듈에서 접근할 수 없고 개별적 존재로서 애플리케이션과 분리되어 존재
- 그러나 애플리케이션과 완전히 분리되어 개별적으로 존재하는 모듈은 `재사용 불가능`하며 존재의 의미를 잃기 때문에

#### ✅ export: 공개가 필요한 자산에 한정하여 명시적으로 선택적 공개 가능
- 공개된 모듈의 자산은 다른 모듈에서 재사용 가능하며 `공개된 모듈의 자산을 사용하는 모듈`은 `모듈 사용자(module consumer)`

#### ✅ import: 모듈 사용자는 모듈이 공개한 자산 중 일부 또는 전체를 선택해서 자신의 스코프 내로 불러들여 재사용 가능

<img src="./images/48_1.PNG" width=500px>

> 모듈은 애플리케이션과 분리되어 `개별적으로 존재`하다가 필요에 따라 `다른 모듈에 의해 재사용`

> `기능별`로 분리되어 개별적인 파일로 작성되기 때문에 코드의 단위를 명확히 분리하여 애플리케이션 구성 가능, `재사용성`이 좋아서 `개발 효율성`과 `유지보수성` 증대 가능
### 48.2 자바스크립트와 모듈<a name="48.2"></a>
---

- 자바스크립트는 `웹페이지의 단순 보조 기능`을 처리하기 위한 제한적잉 용도를 목적으로 만들어져 `파일스코프`, `import`, `export` 지원 ❌
- 자바스크립트 파일을 여러 개의 파일로 분리하여 script 태그로 로드해도 분리된 자바스크립트 파일들은 결국 하나의 자바스크립트 파일 내에 있는 것처럼 동작, `모든 자바스크립트 파일은 하나의 전역을 공유`

#### ✅ CommonJS & AMD(Asynchronous Module Definition)의 제안
- 자바스크립트 런타임 환경인 `Node.js`는 모듈시스템의 (사실상)표준인 CommonJS를 채택, 독자적인 진화를 거쳐 현재 100%동일하지는 않지만 기본적으로 CommonJS 사양을 따르고 있음
- 즉, Node.js는 표준 사양은 아니지만 모듈 시스템을 지원하고 `파일별로 독립적인 파일 스코프`를 가짐

### 48.3 ES6 모듈(ESM)<a name="48.2"></a>
---
- ES6에서는 클라이언트 사이드 자바스크립트에서도 동작하는 모듈 기능을 추가(IE를 제외한 대부부분의 브라우저에서 사용 가능)

#### ✅ ES6 모듈(`ESM`)의 사용법은 `script`태그에 `type="module"` 어트리뷰트를 추가하면 로드된 자바스크립트 파일은 모듈로서 동작
- 일반적인 자바스크립트 파일이 아닌 ESM임을 명확히 하기 위해 `mjs 확장자` 사용 권장
- 기본적으로 strict mode 적용

```html
<script type="module" src="app.mjs"></script>
```

#### 48.3.1 모듈 스코프
- ESM은 독자적인 모듈 스코프를 갖고, ESM이 아닌 일반 자바스크립트 파일은 `script` 태그로 분리해서 로드해도 독자적인 모듈 스코프를 갖지 않음
- 모듈 내에서 선언한 식별자는 모듈 외부에서 참조 불가능(모듈 스코프가 다르기 때문에!)


```html
<script type="module" src="foo.mjs"></script>
<script type="module" src="bar.mjs"></script>
```

```mjs
// foo.mjs
const x = 'foo';
console.log(x); // foo

// bar.mjs
console.log(x); // ReferenceError: x is not defined
```

#### 48.3.2 export 키워드
- 모듈은 `독자적인 모듈 스코프`를 갖기 때문에 모듈 내부에서 선언한 모든 식별자는 기본적으로 해당 모듈 내부에서만 참조 가능
- 모듈 내부에서 선언한 식별자를 외부에 공개하여 다른 모듈들이 재사용할 수 있게 하려면 `export`키워드 사용
- `export`키워드는 선언문 앞에 사용하고 변수, 함수, 클래스 등 모든 식별자 export 가능!

```mjs
const pi = Math.PI;

function square(x) {
  return x * x;
}

class Person {
  constructor(name) {
    this.name = name;
  }
}

// 변수, 함수, 클래스를 하나의 객체로 구성하여 export
export { pi, square, Person };
```

#### 48.3.3 import 키워드
- 다른 모듈에서 공개한 식별자를 자신의 모듈 스코프 내부로 로드하려면 `import` 키워드를 사용
- 다른 모듈이 export한 식별자 이름으로 import 해야 하며 ESM의 경우 파일 확장자 생략 불가능!

```mjs
// app.mjs
import { pi, square, Person } from './lib.mjs';

console.log(pi);      // 3.141592653689793
console.log(square); // 100
console.log(new Person('Lee')); // Person { name : 'Lee' }
```

```html
// app.mjs는 애플리케이션의 진입점이므로 반드시 script 태그로 로드
// 하지만 lib.mjs는 app.mjs의 import 문에 의해 로드되는 의존성이므로 따로 script 태그로 로드하지 않아도 됨
<script type="module" src="app.mjs"></script>
```

- 모듈이 export한 식별자 이름을 일일이 지정하지 않고 하나의 이름으로 한 번에 import 가능 

  `import * as lib from './lib.mjs';`

- 모듈이 export한 식별자 이름을 변경하여 import 가능

  `import { pi as PI, square as sq, Person as P } from './lib.mjs';`

- 모듈에서 하나의 값만 export한다면 `default` 키워드 사용 가능 -> 기본적으로 이름 없이 하나의 값을 export
- default 키워드를 사용하는 경우 var, let, const 키워드는 사용 불가능!
- default 키워드와 함께 export한 모듈은 `{} 없이 임의의 이름으로 import`
  
  `export default -> x * x;`

  `import square from './lib.mjs';`