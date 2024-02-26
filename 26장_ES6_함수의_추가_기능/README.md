## 🗂️ 26장. ES6 함수의 추가 기능

### 목차
- [26.1 함수의 구분](#26.1)
- [26.2 메서드](#26.2)
- [26.3 화살표 함수](#26.3)
- [26.4 Rest 파라미터](#26.4)
- [26.5 매개변수 기본값](#26.5)

<br />

### 26.1 함수의 구분<a name="26.1"></a>
---
- ES6 이전의 모든 함수는 `일반 함수` 및 `생성자 함수`, `메서드`로서 호출이 가능
- 즉, callable(호출할 수 있는 함수 객체)이면서 constructor(인스턴스를 생성할 수 있는 함수 객체)
- 편리한 것 같지만 실수 유발 가능성 존재, 성능적 손해

```js
var foo = function () {
  return 1;
};

// 일반적인 함수로서 호출
foo(); // -> 1

// new 연산자와 함께 생성자 함수로서 호출
new foo(); // -> foo {}

// 객체에 바인딩되어 메서드로서 호출
var obj = { foo: foo };
obj.foo(); // -> 1
```

#### ✅ ES6에서는 함수를 `사용 목적`에 따라 세 가지 종류로 명확히 구분
|ES6함수 의 구분 | constructor | prototype | super | arugments |
|--------|:----------:|:--------:|:----------:|:-------:|
|일반 함수(Normal) | O | O | X | O |
|메서드(Method) | X | X | O | O |
|화살표 함수(Arrow) | X | X | X | X |

➡️ 일반 함수는 함수 선언문이나 함수 표현식으로 정의한 함수

➡️ 일반 함수는 constructor이지만 `메서드와 화살표 함수`는 `non-constructor`

### 26.2 메서드<a name="26.2"></a>
---
#### ✅ ES6에서 메서드는 `메서드 축약 표현으로 정의된 함수`만을 의미
#### ✅ ES6에서 메서드는 인스턴스를 생성할 수 없는 `non-constructor`이므로 prototype 프로퍼티가 없고, 프로토타입도 생성하지 않음

```js
const obj = {
  x: 1,
  // foo는 메서드
  foo() { return this.x; },
  // bar에 바인딩된 함수는 메서드가 아닌 일반 함수
  bar: function() { return this.x; }
};

console.log(obj.foo()); // 1
console.log(obj.bar()); // 1

new obj.foo(); // -> TypeError: obj.foo is not a constructor
new obj.bar(); // -> bar {}

// obj.foo는 constructor가 아닌 ES6 메서드이므로 prototype 프로퍼티가 없음
obj.foo.hasOwnProperty('prototype'); // -> false

// obj.bar는 constructor인 일반 함수이므로 prototype 프로퍼티가 있음
obj.bar.hasOwnProperty('prototype'); // -> true
```

#### ✅ ES6에서 메서드는 자신을 바인딩한 객체를 가리키는 `[[HomeObject]]` 내부 슬롯을 가짐
#### ✅ super 참조는 이 내부 슬롯을 사용하여 수퍼 클래스의 메소드를 참조하므로 ES6 메서드는 super 키워드 사용 가능

```js
const base = {
  name: 'Lee',
  sayHi() {
    return `Hi! ${this.name}`;
  }
};

const derived = {
  __proto__: base,
  // sayHi는 ES6 메서드
  // sayHi의 [[HomeObject]]는 sayHi가 바인딩된 객체인 derived를 가리키고
  // super는 sayHi의 [[HomeObject]]의 프로토타입인 base를 가리킴
  sayHi() {
    return `${super.sayHi()}. how are you doing?`;
  }
};

console.log(derived.sayHi()); // Hi! Lee. how are you doing?
```

➡️ ES6메서드는 본연의 기능(`super`)을 추가하고 의미적으로 맞지 않는 기능(`constructor`)은 제거!

### 26.3 화살표 함수<a name="26.3"></a>
---
#### ✅ 화살표 함수(arrow function)는 function키워드 대신 `화살표(=>, fat arrow)`를 사용하여 
#### ✅ 기존의 함수 정의 방식보다 간략하게 함수 정의하며 내부 동작도 기존의 함수보다 간략!

#### 26.3.1 화살표 함수의 정의

#### ✅ 함수 정의
- 함수 표현식으로 정의, 호출 방식은 기존과 동일
```js
const multiply = (x, y) => x * y;
multiply(2, 3); // -> 6
```

#### ✅ 매개변수 선언
- 매개변수가 여러 개인 경우 소괄호 () 안에 매개변수 선언
- 매개변수가 한 개인 경우 소괄호 생략 가능, 없는 경우 소괄호 생략 불가
```js
const arrow = (x, y) => { ... };
const arrow = x => { ... };
const arrow = () => { ... };
```

#### ✅ 함수 몸체 정의
- 함수 몸체가 하나의 문으로 구성된다면 몸체를 감싸는 중괄호 생략 가능
```js
// concise body
const power = x => x ** 2;
power(2); // -> 4

// 위 표현은 다음과 동일
// block body
const power = x => { return x ** 2; };
```

- 표현식이 아닌데 중괄호 생략 시 에러 발생
```js
const arrow = () => const x = 1; // SyntaxError: Unexpected token 'const'

// 위 표현은 다음과 같이 해석
const arrow = () => { return const x = 1; };
```

- 객체 리터럴 반환시 소괄호로 감싸줘야 함
```js
const create = (id, content) => ({ id, content });
create(1, 'JavaScript'); // -> {id: 1, content: "JavaScript"}

// 위 표현은 다음과 동일
const create = (id, content) => { return { id, content }; };
```

- 함수 몸체가 여러 개의 문으로 구성된다면 중괄호 생략 불가
```js
const sum = (a, b) => {
  const result = a + b;
  return result;
};
```

- 즉시 실행 함수로 사용 가능
```js
const person = (name => ({
  sayHi() { return `Hi? My name is ${name}.`; }
}))('Lee');

console.log(person.sayHi()); // Hi? My name is Lee.
```

- 고차 함수(Array.prototype.map 등)에 인수로 전달 가능
```js
// ES5
[1, 2, 3].map(function (v) {
  return v * 2;
});

// ES6
[1, 2, 3].map(v => v * 2); // -> [ 2, 4, 6 ]
```

#### 26.3.2 화살표 함수와 일반 함수의 차이
#### ✅ 화살표 함수는 인스턴스를 생성할 수 없는 `non-constructor`
```js
const Foo = () => {};
// 화살표 함수는 생성자 함수로서 호출 불가능
new Foo(); // TypeError: Foo is not a constructor
```

#### ✅ 중복된 매개변수 이름 선언 불가능
```js
const arrow = (a, a) => a + a;
// SyntaxError: Duplicate parameter name not allowed in this context
```

#### ✅ 화살표 함수는 함수 자체의 `this`, `arguments`, `super`, `new.target`바인딩을 갖지 않음
- 내부에서 this, arguments, super, new.target을 참조하면 스코프 체인을 통해 상위 스코프를 참조

#### 26.3.3 this

#### ✅ 화살표 함수가 일반 함수와 구별되는 가장 큰 특징은 this

```js
class Prefixer {
  constructor(prefix) {
    this.prefix = prefix;
  }

  add(arr) {
    // add 메서드는 인수로 전달된 배열 arr을 순회하며 배열의 모든 요소에 prefix를 추가
    // ①
    return arr.map(function (item) {
      return this.prefix + item; // ②
      // -> TypeError: Cannot read property 'prefix' of undefined
    });
  }
}

const prefixer = new Prefixer('-webkit-');
console.log(prefixer.add(['transition', 'user-select']));
```
> 프로토타입 메서드 내부인 ①에서 this는 메서드를 호출한 객체(prefixer)을 가리키는데, Array.prototype.map의 인수로 전달한 콜백 함수의 내부인 ②에서 this는 undefined를 가리킴(Array.prototype.map 메서드가 콜백 함수를 일반함수로서 호출하기 때문!)

> 일반 함수로서 호출되는 모든 함수 내부의 this는 전역 객체를 가리키는데 클래스 내부의 모든 코드에는 strict mode가 암묵적으로 적용되기 때문에, Array.prototype.map 메서드의 콜백 함수에도 strict mode가 적용되고 strict mode에서 일반 함수로서 호출된 모든 함수 내부의 this에는 undefined가 바인딩 된다!

➡️ `콜백 함수 내부의 this문제` 발생 (②와 ①이 서로 다른 값을 가리키고 있기 때문에 TypeError 발생!)
➡️ 과거 콜백 함수 내부의 this 문제는 다음과 같이 해결

1. add 메서드를 호출한 prefixer 객체를 가리키는 this를 일단 회피시킨 후에 콜백 함수 내부에서 사용
2. Array.prototype.map의 두 번째 인수로 add 메서드를 호출한 prefixer 객체를 가리키는 this를 전달
3. Fuction.prototype.bind 메서드를 사용하여 add 메서드를 호출한 prefixer 객체를 가리키는 this를 바인딩

#### ✅ 화살표 함수를 사용하여 해결
```js
class Prefixer {
  constructor(prefix) {
    this.prefix = prefix;
  }

  add(arr) {
    return arr.map(item => this.prefix + item);
  }
}

const prefixer = new Prefixer('-webkit-');
console.log(prefixer.add(['transition', 'user-select']));
// ['-webkit-transition', '-webkit-user-select']
```
> 화살표 함수는 함수 자체의 this 바인딩을 갖지 않기 때문에 상위 스코프의 this를 그대로 참조(lexical this)



#### ✅ 메서드를 화살표 함수로 정의하는 것은 피해야 한다 ❗❗❗
```js
// Bad
const person = {
  name: 'Lee',
  sayHi: () => console.log(`Hi ${this.name}`)
};

// sayHi 프로퍼티에 할당된 화살표 함수 내부의 this는 상위 스코프인 전역의 this가 가리키는
// 전역 객체를 가리키므로 이 예제를 브라우저에서 실행하면 this.name은 빈 문자열을 갖는
// window.name과 같다. 전역 객체 window에는 빌트인 프로퍼티 name이 존재한다.
person.sayHi(); // Hi

// Good
const person = {
  name: 'Lee',
  sayHi() {
    console.log(`Hi ${this.name}`);
  }
};

person.sayHi(); // Hi Lee
```

#### 26.3.4 super
#### ✅ 화살표 함수는 함수 자체의 super 바인딩을 갖지 않고, 상위 스코프의 `super`를 참조
```js
class Base {
  constructor(name) {
    this.name = name;
  }

  sayHi() {
    return `Hi! ${this.name}`;
  }
}

class Derived extends Base {
  // 화살표 함수의 super는 상위 스코프인 constructor의 super를 가리킴
  sayHi = () => `${super.sayHi()} how are you doing?`;
}

const derived = new Derived('Lee');
console.log(derived.sayHi()); // Hi! Lee how are you doing?
```

#### 26.3.5 arguments
#### ✅ 화살표 함수는 함수 자체의 arguments 바인딩을 갖지 않고, 상위 스코프의 `arguments`를 참조
```js
(function () {
  // 화살표 함수 foo의 arguments는 상위 스코프인 즉시 실행 함수의 arguments를 가리킴
  const foo = () => console.log(arguments); // [Arguments] { '0': 1, '1': 2 }
  foo(3, 4);
}(1, 2));

// 화살표 함수 foo의 arguments는 상위 스코프인 전역의 arguments를 가리킴
// 하지만 전역에는 arguments 객체가 존재하지 않고 arguments 객체는 함수 내부에서만 유효
const foo = () => console.log(arguments);
foo(1, 2); // ReferenceError: arguments is not defined
```

### 26.4 Rest 파라미터<a name="26.4"></a>
---
**💡 Rest 파라미터(나머지 매개변수)는 매개변수 이름 앞에 세개의 점 ...을 붙여서 정의한 매개변수**

#### 26.4.1 기본 문법
#### ✅ 함수에 전달된 인수들의 목록을 배열로 전달받음
```js
function foo(...rest) {
  // 매개변수 rest는 인수들의 목록을 배열로 전달받는 Rest 파라미터
  console.log(rest); // [ 1, 2, 3, 4, 5 ]
}

foo(1, 2, 3, 4, 5);
```

#### ✅ 일반 매개변수와 함께 사용 가능
-> 먼저 선언된 매개변수에 할당된 인수를 제외한 나머지 인수들이 들어오기 때문에, `제일 마지막에 선언`
```js
function foo(param, ...rest) {
  console.log(param); // 1
  console.log(rest);  // [ 2, 3, 4, 5 ]
}

foo(1, 2, 3, 4, 5);

function bar(param1, param2, ...rest) {
  console.log(param1); // 1
  console.log(param2); // 2
  console.log(rest);   // [ 3, 4, 5 ]
}

bar(1, 2, 3, 4, 5);
```

#### ✅ 단 하나만 선언 가능
```js
function foo(...rest1, ...rest2) { }

foo(1, 2, 3, 4, 5);
// SyntaxError: Rest parameter must be last formal parameter
```
#### ✅ 함수 정의 시 선언한 매개변수 개수를 나타내는 함수 객체의 length 프로퍼티에 영향을 주지 않음
```js
function foo(...rest) {}
console.log(foo.length); // 0

function bar(x, ...rest) {}
console.log(bar.length); // 1

function baz(x, y, ...rest) {}
console.log(baz.length); // 2
```

#### 26.4.2 Rest 파라미터와 arguments 객체
**💡 arguments 객체는 함수 호출 시 전달된 인수들의 정보가 담겨있는 유사 배열 객체**

#### ✅ 함수 내부에서 지역 변수처럼 사용 가능
```js
// 매개변수의 개수를 사전에 알 수 없는 가변 인자 함수
function sum() {
  // 가변 인자 함수는 arguments 객체를 통해 인수를 전달받음
  console.log(arguments);
}

sum(1, 2); // {length: 2, '0': 1, '1': 2}
```

#### ✅ Rest 파라미터는 가변 인자 함수의 인수 목록을 배열로 직접 전달 가능
```js
function sum(...args) {
  // Rest 파라미터 args에는 배열 [1, 2, 3, 4, 5]가 할당
  return args.reduce((pre, cur) => pre + cur, 0);
}
console.log(sum(1, 2, 3, 4, 5)); // 15
```
➡️ 화살표 함수는 함수 자체의 arguments 객체를 갖지 않기 때문에, 화살표 함수로 가변 인자 함수를 구현해야 할 때는 반드시 Rest 파라미터 사용

### 26.5 매개변수 기본값<a name="26.5"></a>
---
- ES6에서 도입

#### ✅ 함수 내에서 수행하던 인수 체크 및 초기화 간소화 가능
```js
function sum(x = 0, y = 0) {
  return x + y;
}

console.log(sum(1, 2)); // 3
console.log(sum(1));    // 1
```

#### ✅ 매개변수에 인수를 전달하지 않은 경우와 undefined를 전달한 경우에만 유효
```js
function logName(name = 'Lee') {
  console.log(name);
}

logName();          // Lee
logName(undefined); // Lee
logName(null);      // null
```

#### ✅ Rest 파라미터에는 기본값 지정 불가능
```js
function foo(...rest = []) {
  console.log(rest);
}
// SyntaxError: Rest parameter may not have a default initializer
```

#### ✅ 함수 정의 시 선언한 매개변수 개수를 나타내는 함수 객체의 length 프로퍼티와 arguments 객체에 아무런 영향을 주지 않음
```js
function sum(x, y = 0) {
  console.log(arguments);
}

console.log(sum.length); // 1

sum(1);    // Arguments { '0': 1 }
sum(1, 2); // Arguments { '0': 1, '1': 2 }
```