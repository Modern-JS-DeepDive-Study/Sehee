## 🗂️ 46장. 제너레이터와 async/await

### 목차

- [46.1 제너레이터란?](#46.1)
- [46.2 제너레이터 함수의 정의](#46.2)
- [46.3 제너레이터 객체](#46.3)
- [46.4 제너레이터의 일시 중지와 재개](#46.4)
- [46.5 제너레이터의 활용](#46.5)
- [46.6 async/await](#46.6)
  <br />

### 46.1 제너레이터란?<a name="46.1"></a>

---

#### ✅ 제너레이터(genarator): ES6 도입, 코드 블록의 실행을 `일시 중지`했다가 `필요한 시점에 재개`할 수 있는 특수한 함수

#### ✅ 제너레이터와 일반 함수의 차이점

1. 제너레이터 함수는 `함수 호출자에게 함수 실행의 제어권`을 `양도`할 수 있다.

   - 일반 함수를 호출하면 `제어권이 함수에게` 넘어가고 함수 코드를 일괄 실행! 즉, `함수 호출자는 함수롤 호출한 이후 함수 실행 제어 불가능`
   - 제너레이터 함수는 `함수 실행을 함수 호출자가 제어 가능!` 즉, 함수 호출자가 함수 실행을 일시 중지시키거나 재개 가능

2. 제너레이터 함수는 `함수 호출자와 함수의 상태`를 `주고받을 수` 있다.

   - 일반 함수를 호출하면 `매개변수`를 통해 외부에서 값을 주입받고 함수 코드를 일괄 실행하여 결과값을 함수 외부로 반환!

     즉, 함수가 실행되고 있는 동안에는 `함수 외부에서 내부로 값을 전달하여 함수의 상태 변경 불가`

   - 제너레이터 함수는 `함수 호출자와 양방향으로` 함수의 상태를 주고 받기 가능

3. 제너레이터 함수를 호출하면 `제너레이터 객체를 반환`한다.
   - 일반 함수를 호출하면 함수 코드를 일괄 실행하고 값을 반환
   - 제너레이터 함수를 호출하면 `이터러블이면서 동시에 이터레이터인 제너레이터 객체`를 반환

### 46.2 제너레이터 함수의 정의<a name="46.2"></a>

---

#### ✅ 제너레이터 함수는 `*function` 키워드로 선언, 하나 이상의 `yield`표현식을 포함

#### ✅ `애스터리스크(*)`의 위치는 `function키워드와 함수 이름 사이라면 어디나 가능`, 일관성을 위해 functrion키워드 바로 뒤 권장!

#### ✅ 제너레이터 함수는 `화살표 함수로 정의 불가능`, `new 연산자와 함께 생성자 함수로 호출 불가능`

```js
// 제너레이터 함수 선언문
function* getDecFunc() {
  yield 1;
}

// 제너레이터 함수 표현식
const genExpFunc = function* () {
  yield 1;
};

// 제너레이터 매서드
const obj = {
  *getObjMethod() {
    yield 1;
  },
};

// 제너레이터 클래스 메서드
class MyClass {
  *getClsMethod() {
    yield 1;
  }
}
```

### 46.3 제너레이터 객체<a name="46.3"></a>

---

#### ✅ 제너레이터 함수를 호출하면 `이터러블`이면서 `이터레이터`인 `제너레이터 객체`를 반환

- `제너레이터 객체`는 Symbol.iterator 메서드를 상속 받고(이터러블), value와 done 프로퍼티를 갖는 이터레이터 리절트 객체를 반환하는 next 메서드를 소유(이터레이터)

```js
// 제너레이터 함수
function* getFunc() {
  yield 1;
  yield 2;
  yield 3;
}

// 제너레이터 함수 호출 -> 제너레이터 객체 반환
const generator = getFunc();

console.log(Symbol.iterator in generator); // true (이터러블이면서 동시에 이터레이터)
console.log("next" in generator); //true (next 메서드 소유)
```

#### ✅ 이터레이터에는 없는 `return`, `throw`메서드를 가짐

- 제너레이터 객체의 세 개의 메서드 호출 시 동작 방식

1. `next`메서드

   - 제너레이터 함수의 `yield`표현식까지 코드 블록을 실행
   - yield된 값을 value 프로퍼티 값으로, false를 done 프로퍼티 값으로 갖는 이터레이터 리절트 객체 반환

2. `return`메서드

   - 인수로 전달받은 값을 value 프로퍼티 값으로, true를 done 프로퍼티 값으로 갖는 이터레이터 리절트 객체를 반환

3. `throw` 메서드
   - 인수로 전달받은 에러를 발
   - undefined를 value 프로퍼티 값으로, true를 done 프로펕티 값으로 갖는 이터레이터 리절트 객체를 반환

```js
function* getFunc() {
  try {
    yield 1;
    yield 2;
    yield 3;
  } catch (e) {
    console.error(e);
  }
}

const generator = getFunc();
console.log(generator.next()); // { value: 1, done: false }
console.log(generator.return("끝!")); // { value: "끝!", done: true }
console.log(generator.throw("에러발생!")); // 에러발생!
// { value: undefined, done: true }
```

### 46.4 제너레이터의 일시 중지와 재개<a name="46.4"></a>

---

#### ✅ 제너레이터는 yield 키워드와 next 메서드를 통해 `실행을 일시 중지했다가 필요한 시점에 다시 재개 가능`

#### ✅ `yield` 키워드는 제너레이터 함수의 실행을 `일시 중지` 시키거나, `yield` 키워드 뒤에 오는 `표현식의 평가 결과를 제너레이터 함수 호출자에게 반환`

#### ✅ 제너레이터 객체의 `next` 메서드를 호출하면 yield 표헌식까지 실행되고 일시 중지 -> 이 때 함수의 제어권이 `호출자로 양도(yield)`

```js
function* getFunc() {
  yield 1;
  yield 2;
  yield 3;
}
```

> 제너레이터 함수 생성

```js
const generator = getFunc();
```

> 제너레이터 함수를 호출하면, 이터러블이면서 이터레이터인 + next 메서드를 소유하는 제너레이터 객체를 반환

```js
console.log(generator.next()); // { value: 1, done: false }
```

> 처음 next 메서드를 호출하면 첫 번째 yield 표현식까지 실행되고 일시 중지!

> next 메서드는 `value 프로퍼티에 첫 번째 yield 표현식에서 yield된 값 1이 할당`되고, `done 프로퍼티에는 제너레이터 함수가 끝까지 실행되었는지를 나타내는 false가 할당`된 이터레이터 리절트 객체({value, done})을 반환

```js
console.log(generator.next()); //{ value: 2, done: false }
```

> 다시 next 메서드를 호출하면 두 번째 yield 표현식까지 실행되고 일시 중지!

> next 메서드는 `value 프로퍼티에 두 번째 yield 표현식에서 yield된 값 2가 할당`되고, `done 프로퍼티에는 제너레이터 함수가 끝까지 실행되었는지를 나타내는 false가 할당`된 이터레이터 리절트 객체({value, done})을 반환

```js
console.log(generator.next()); //{ value: 3, done: false }
```

> 다시 next 메서드를 호출하면 세 번째 yield 표현식까지 실행되고 일시 중지!

> next 메서드는 `value 프로퍼티에 세 번째 yield 표현식에서 yield된 값 3이 할당`되고, `done 프로퍼티에는 제너레이터 함수가 끝까지 실행되었는지를 나타내는 false가 할당`된 이터레이터 리절트 객체({value, done})을 반환

```js
console.log(generator.next()); //{ value: undefined, done: true }
```

> 다시 next 메서드를 호출하면 남은 yield 표현식이 없으므로 제너레이터 함수의 마지막까지 실행

> next 메서드는 `value 프로퍼티에 제너레이터 함수의 반환값 undefined가 할당`되고, `done 프로퍼티에는 제너레이터 함수가 끝까지 실행되었는지를 나타내는 true가 할당`된 이터레이터 리절트 객체({value, done})을 반환

#### ✅ yield 표현식을 할당받는 변수에 yield 표현식의 평가 결과가 할당되지 않는 것에 주의!

```js
function* genFuc() {
  const x = yield 1;
  const y = yield x + 10;
  // 제너레이터에서는 값을 반환할 필요가 없고 return은 종료의 의미로만 사용해야 함
  return x + y;
}

const generator = genFuc(0);

// 만약 처음 호출하는 next 메서드에 인수를 전달하면 무시됨
let res = generator.next();
console.log(res); //{ value: 1, done: false }

// next 메서드에 인수로 전달한 10은 getFunc 함수의 x 변수에 할당
res = generator.next(10);
console.log(res); //{ value: 20, done: false }

// next 메서드에 인수로 전달한 20은 getFunc 함수의 y 변수에 할당
res = generator.next(20);
console.log(res); //{ value: 30, done: true }
```

> 함수 호출자는 next 메서드를 통해 yield 표현식까지 함수를 실행시켜 제너레이터 객체가 관리하는 상태를 꺼내올 수 있고, next 메서드에 인수를 전달해서 제너레이터 객체에 상태를 밀어넣을 수 있음

> 이러한 특성을 활용해 `비동기 처리를 동기 처리처럼 구현 가능`

### 46.5 제너레이터의 활용<a name="46.5"></a>

---

#### 46.5.1 이터러블의 구현

- 제너레이터 함수를 사용하면 이터레이션 프로토콜을 준수해 이터러블을 생성하는 방식보다 간단하게 구현 가능

```js
// 무한 이터러블을 생성하는 제너레이터 함수
const infineteFibonacci = (function* () {
  let [pre, cur] = [0, 1];

  while (true) {
    [pre, cur] = [cur, pre + cur];
    yield cur;
  }
})();

// infiniteFibonacci는 무한 이터러블
for (const num of infiniteFibonacci) {
  if (num > 10000) break;
  console.log(num); // 1 2 3 5 8 ... 2584 4181 6765
}
```

#### 46.5.2 비동기 처리
- 제너레이터 함수의 next 메서드와 yield 표현식을 통해 `함수 호출자와 함수의 상태를 주고받을 수 있는 특성`을 활용하면 `프로미스를 사용한 비동기 처리처럼 구현 가능`
- 프로미스의 후속 처리 메서드 then/catch/finally 없이 비동기 처리 결과를 반환하도록 구현 가능
```js
// node-fetch는 Node.js 환경에서 window.fetch 함수를 사용하기 위한 패키지, 브라우저 환경에서는 필요 없음
const fetch = require('node-fetch');

const async = generatorFunc => {
    const generator = generatorFunc(); ✅ (2)

    const onResolved = arg => {
        const result = generator.next(arg); ✅ (5) 

        return result.done 
            ?result.value : 
            result.value.then(res => onResolved(res)); ✅ (7) 

    }
    return onResolved; ✅ (3) 
}

(async(function * fetchTodo(){ ✅ (1) 
    const url = 'https://jsonplaceholder.typicode.com/post/1';

    const res = yield fetch(url); ✅ (6)
    const todo = yield response.json(); ✅ (8)
    console.log(todo);
})()) ✅ (4)
```
> `async 함수가 호출`(1)되면 인수로 전달받은 제너레이터 함수 `fetchTodo를 호출`하여 제너레이터 객체를 생성(2)하고 `onResolved 함수 반환`, onResolved 함수는 `상위 스코프의 generator 변수를 기억하는 클로저` , async 함수가 반환한 onResolved 함수를 즉시 호출(4)하여 (2)에서 생성한 제너레이터 객체의 `next 메서드를 처음 호출`(5)

> next 메서드가 처음 호출(5)되면 제너레이터 함수 `fetchTodo의 첫 번째 yield문`(6)까지 실행, 이때 next 메서드가 반환한 이터레이터 리절트 객체의 done 프로퍼티 값이 false, 즉 아직 제너레이터 함수가 끝까지 실행되지 않았다면 이터레이터 리절트 객체의 value 프로퍼티 값인 `첫 번 째 yield된 fetch 함수가 반환한 프로미스가 resolve한 Response 객체를 onResolved 함수에 인수로 전달하면서 재귀호출`(7)

> onResolved 함수에 인수로 전달된 Response 객체를 next 메서드에 인수로 전달하면서 `next 메서드를 두 번째로 호출`(5), 이때 next 메서드에 인수로 전달한 Response 객체는 제너레이터 함수 fetchTodo의 response 변수(6)에 할당되고 `제너레이터 함수 fetchTodo의 두 번째 yield문`(8)까지 실행

> next 메서드가 반환한 이터레이터 리절트 객체의 done 프로퍼티 값이 false, 즉 아직 제너레이터 함수가 끝까지 실행되지 않았다면 이터레이터 리절트 객체의 value 프로퍼티 값, 즉 `두 번째 yield된 response.json 메서드가 반환한 프로미스가 resolve한 todo 객체를 onResolved 함수에 인수로 전달하면저 재귀 호출`(7)

> onResolved 함수에 인수로 전달된 todo 객체를 next 메서드에 인수로 전달하면서 `next 메서드를 세 번째로 호출`(5), 이때 next 메서드에 인수로 전달한 todo 객체는 제너레이터 함수 fetchTodo의 todo 변수(8)에 할당되고 `제너레이터 함수 fetchTodo가 끝까지 실행`

> next 메서드가 반환한 이터레이터 리절트 객체의 done 프로퍼티 값이 true, 즉 제너레이터 함수가 `끝까지 실행`되었다면 이터레이터 리절트 객체의 value 프로퍼티 값, 즉 `제너레이터 함수 fetchTodo의 반환값인 umdefined를 그대로 반환(9)하고 처리를 종료`

### 46.6 async/await<a name="46.6"></a>
---

- 제너레이터를 사용해서 비동기 처리를 동기 처리처럼 동작하도록 구현했지만 `코드가 무척이나 장황하고 가독성 나빠짐`

- ES8(ECMAScript 2017)에서는 제너레이터보다 간단하고 가독성 좋게 비동기 처리를 동기 처리처럼 동작하도록 구현할 수 있는 `async/await` 도입

#### ✅ async/await: 프로미스를 기반으로 동작하나 프로미스의 then/catch/finally 후속 처리 메서드에 콜백 함수를 전달해서 비동기 처리 결과를 후속 처리할 필요 없이 `마치 동기 처리처럼 프로미스 사용 가능`

#### 46.6.1 async 키워드
- `async 함수`는 `async` 키워드를 사용해 정의하며 언제나 `프로미스를 반환`
- 명시적으로 프로미스를 반환하지 않더라고 암묵적으로 반환값을 resolve하는 프로미스를 반환

```js
// 함수 선언문
async function foo(n) {return n;}
foo(1).then(v=> console.log(v)) // 1

// async 함수 표현식 
const bar = async function(n) {return n};
bar(2).then(v => console.log(v)); // 2

// async 화살표 함수 
const baz = async n => n;
baz(3).then(v=>console.log(v)) // 3

// async 메서드 
const obj = {
    async foo(n) {return n;}
};
obj.foo(4).then(v => console.log(v)); // 4

// async 클래스 메서드 
class MyClass {
    async bar(n) {return n;}
}
const myClass = new MyClass();
myClass.bar(5).then(v => console.log(v)) // 5
```
> 클래스의 constructor 메서드는 인스턴스를 반환해야 하지만 async 함수는 언제나 프로미스를 반환해야 하므로 constructor 메서드는 async 메서드가 될 수 없음!

#### 46.6.2 await 키워드
- `await` 키워드는 반드시 `async 함수 내부에서 사용`, 반드시 `프로미스 앞에서 사용`
- `await` 키워드는 프로미스가 settled 상태(비동기 처리가 수행된 상태)가 될 때까지 대기하다가 settled 상태가 되면 프로미스가 resolve한 처리 결과를 반환

```js
const getGitUserName = async id => {
    const res = await fetch(`https://api.github.com/users/${id}`); ✅ (1)
    const name = await res.json(); ✅ (2)
    console.log(name);
};

getGitUserName('ungmo2');
```

> (1)의 fetch 함수가 수행한 HTTP 요청에 대한 서버의 응답이 도착해서 fetch 함수가 반환한 프로미스가 settled 상태가 될 때까지 대기, 이후 프로미스가 settled 상태가 되면 프로미스가 resolve한 처리 결과가 res 변수에 할당

```js
async function foo() {
    const a = await new Promise(resolve => setTimeout(() => resolve(1), 3000));
    const b = await new Promise(resolve => setTimeout(() => resolve(2), 3000));
    const c = await new Promise(resolve => setTimeout(() => resolve(3), 3000));

    console.log([a, b, c]); // [1, 2, 3]
}

foo(); // 약 6초 소요
```

> 모든 프로미스에 await 키워드를 사용하는 것은 주의가 필요, 위 예제의 foo 함수는 종료될 때까지 약 6초가 소요되는데 첫 번째 프로미스는 settled 상태가 될 때까지 3초, 두 번째 프로미스는 settled 상태가 될 때까지 2초, 세 번째 프로미스는 settled 상태가 될 때까지 1초가 소요

> foo 함수가 수행하는 3개의 비동기 처리는 서로 연관이 없이 개별적으로 수행되는 비동기 처리이므로 앞선 비동기 처리가 완료될 때까지 대기해서 순차적으로 처리할 필요 없음

```js
async function foo() {
    const res = await Promise.all([
        new Promise(resolve => setTimeout(() => resolve(1), 3000)),
        new Promise(resolve => setTimeout(() => resolve(2), 3000)),
        new Promise(resolve => setTimeout(() => resolve(3), 3000)),
    ]);

    console.log(res); // [1, 2, 3]
}

foo(); // 약 3초 소요
```

> 다음과 같이 처리하는 것이 권장

```js
async function bar(n) {
    const a = await new Promise(resolve => setTimeout(() => resolve(n), 3000));
    //  두 번째 비동기 처리를 수행하려면 첫 번째 비동기 처리 결과가 필요
    const b = await new Promise(resolve => setTimeout(() => resolve(a + 1), 3000));
    // 세 번째 비동기 처리를 수행하려면두 번째 비동기 처리 결과가 필요
    const c = await new Promise(resolve => setTimeout(() => resolve(b + 1), 3000));
}
```
> bar 함수는 앞선 비동기 처리의 결과를 가지고 다음 비동기 처리를 수행해야 하고, 비동기 처리의 처리 순서가 보장되어야 하므로 모든 프로미스에 await 키워드를 써서 순차적으로 처리해야 함

#### 46.6.3 에러 처리

- 비동기 처리를 위한 콜백 패턴의 단점 중 가장 심각한 것은 에러 처리가 곤란하다는 것
- 에러는 `호출자 방향으로 전파` 즉, 콜 스택의 아래 방향(실행 중인 실행 컨텍스트가 푸시되기 직전에 푸시된 실행 컨텍스트 방향)으로 전파
- 비동기 함수의 콜백 함수를 호출한 것은 비동기 함수가 아니기 대문에 `try...catch문`을 사용해 에러 캐치 불가능!

#### ✅ async/await에서 에러 처리는 try...catch문을 사용 가능, 콜백 함수를 인수로 전달받는 비동기 함수와는 달리 프로미스를 반환하는 비동기 함수는 명시적으로 호출할 수 있기 때문에 호출자 명확

```js
const fetch = require('node-fetch');

const foo = async () => {
    try {
        const wrongUrl = 'https://wrong.url';

        const response = await fetch(wrongUrl);
        const data = await response.json();
        console.log(data);
    } catch (err) {
        console.error(err); // TypeError : Failed to fetch
    }
};

foo();
```
> 위 예제의 foo 함수의 catch 문은 HTTP 통신에서 발생한 네트워크 에러뿐 아니라 try 코드 블록 내의 모든문에서 발생한 일반적인 에러까지 모두 캐치 가능, async 함수 내에서 catch 문을 사용해서 에러 처리를 하지 않으면 async 함수는 발생한 에러를 reject하는 프로미스를 반환