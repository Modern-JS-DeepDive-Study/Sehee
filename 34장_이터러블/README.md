## 🗂️ 34장. 이터러블

### 목차
- [34.1 이터레이션 프로토콜](#34.1)
- [34.2 빌트인 이터러블](#34.2)
- [34.3 for...of 문](#34.3)
- [34.4 이터러블과 유사 배열 객체](#34.4)
- [34.5 이터레이션 프로토콜의 필요성](#34.5)
- [34.6 사용자 정의 이터러블](#34.6)

<br />

### 34.1 이터레이션 프로토콜<a name="34.1"></a>
---

**💡 이터레이션 프로토콜(iteration protocol)**: 순회 가능한 데이터 컬렉션을 만들기 위해 미리 약속한 규칙

- ES6 이전에는 통일된 규약 없이 각자 나름의 구조를 가지고 `for문`, `for...in문`, `forEach 메서드` 등 다양한 방법으로 순회
- ES6에서는 `순회 가능한 데이터 컬렉션을 이터레이션 프로토콜을 준수하는 이터러블로 통일`하여 `for...of문`, `스프레드 문법`, `배열 디스트럭처링 할당`의 대상으로 사용할 수 있도록 일원화

**💡 이터러블 프로토콜(iterable protocol)**:  Well-known Symbol인 Symbol.iterator 프로퍼티 키로 사용한 메서드를 직접 구현하거나 프로토타입 체인을 통해 상속받은 Symbol.iterator 메서드를 호출하면 이터레이터 프로토콜을 준수한 `이터레이터`를 반환

**💡 이터레이터 프로토콜(iterator protocol)**: 이터러블의 Symbol.iterator 메서드를 호출하면 이터레이터 프로토콜을 준수한 이터레이터를 반환, 이터레이터는 next 메서드를 소유하며 next 메서드를 호출하면 이터러블을 순회하며 value와 done 프로퍼티를 갖는 `이터레이터 리절트 객체`를 반환

#### 34.1.1 이터러블

#### ✅ Symbol.iterator를 프로퍼티 키로 사용한 메서드를 직접 구현하거나 프로토타입 체인을 통해 상속받은 객체
#### ✅ `for...of문`으로 순회 가능, `스프레드 문법`과 `배열 디스트럭처링 할당의 대상`으로 사용 가능

```js
// 이터러블인지 확인하는 함수
const isIterable = v => v !== null && typeof v[Symbol.iterator] === 'function';

// 배열, 문자열, Map, Set 등은 이터러블
isIterable([]);        // -> true
isIterable('');        // -> true
isIterable(new Map()); // -> true
isIterable(new Set()); // -> true
isIterable({});        // -> false


const array = [1, 2, 3];

// 배열은 Array.prototype의 Symbol.iterator 메서드를 상속받는 이터러블
console.log(Symbol.iterator in array); // true

// 이터러블인 배열은 for...of 문으로 순회 가능
for (const item of array) {
  console.log(item);
}

// 이터러블인 배열은 스프레드 문법의 대상으로 사용 가능
console.log([...array]); // [1, 2, 3]

// 이터러블인 배열은 배열 디스트럭처링 할당의 대상으로 사용 가능
const [a, ...rest] = array;
console.log(a, rest); // 1, [2, 3]
```
> 일반 객체는 이터러블이 아니지만, TC39 프로세스의 stage 4단계에 제안되어 있는 스프레프 프로퍼티 제안은 일반 객체에 스프레드 분법의 사용을 허용

#### 34.1.2 이터레이터

#### ✅ 이터러블의 Symbol.iterator 메서드를 호출하면 이터레이터 프로토콜을 준수한 이터레이터를 반환
#### ✅ 이터레이터의 next 메서드는 `이터러블의 각 요소를 순회하기 위한 포인터의 역할`
-> next메서드를 호출하면 `이터러블을 순차적으로 한 단계씩 순회하며 순회 결과를 나타내는 iterator result object를 반환`

```js
// 배열은 이터러블 프로토콜을 준수한 이터러블
const array = [1, 2, 3];

// Symbol.iterator 메서드는 이터레이터를 반환
const iterator = array[Symbol.iterator]();

// next 메서드를 호출하면 이터러블을 순회하며 순회 결과를 나타내는 이터레이터 리절트 객체를 반환
// 이터레이터 리절트 객체는 value와 done 프로퍼티를 갖는 객체
console.log(iterator.next()); // { value: 1, done: false }
console.log(iterator.next()); // { value: 2, done: false }
console.log(iterator.next()); // { value: 3, done: false }
console.log(iterator.next()); // { value: undefined, done: true }
```

### 34.2 빌트인 이터러블<a name="34.2"></a>
---
- 이터레이션 프로토콜을 준수한 객체인 빌트인 이터러블을 제공

|빌트인 이터러블| Symbol.iterator 메서드|
|--------|:----------:|
| Array | Array.prototype[Symbol.iterator] |
| String | String.prototype[Symbol.iterator] |
| Map | Map.prototype[Symbol.iterator] |
| Set | Set.prototype[Symbol.iterator] |
| TypedArray | TypedArray.prototype[Symbol.iterator] |
| arguments | arguments[Symbol.iterator] |
| DOM 컬렉션 | NodeList.prototype[Symbol.iterator], HTMLCollection.prototype[Symbol.iterator] |


### 34.3 for...of 문<a name="34.3"></a>
---
#### ✅ `for...of문`은 이터러블을 순회하면서 이터러블의 요소를 변수에 할당, 내부적으로 `이터레이터의 next 메서드를 호출`하여 이터러블을 순회하며 next메서드가 반환한 이터레이터 리절트 객체의 `value 프로퍼티` 값을 for...of문의 변수에 할당
-> 이터레이터 리절트 객체의 `done 프로퍼티`값이 false이면 순회를 계속하고 true이면 순회를 중단

#### ✅ `for...in문`은 객체의 프로토타입 체인 상에 존재하는 모든 프로토타입의 프로퍼티 중에서 [[Enumerable]]의 값이 true인 프로퍼티를 순회하면서 열거

```js
for (변수선언문 of 이터러블) {...}

// 이터러블
const iterable = [1, 2, 3];

// 이터러블의 Symbol.iterator 메서드를 호출하여 이터레이터를 생성
const iterator = iterable[Symbol.iterator]();

for (;;) {
  // 이터레이터의 next 메서드를 호출하여 이터러블을 순회
  // 이때 next 메서드는 이터레이터 리절트 객체를 반환
  const res = iterator.next();
  console.log(res) // {value: 1, done: false}, {value: 2, done: false}, {value: 3, done: false}, {value: undefined, done: true}

  // next 메서드가 반환한 이터레이터 리절트 객체의 done 프로퍼티 값이 true이면 이터러블의 순회를 중단
  if (res.done) break;

  // 이터레이터 리절트 객체의 value 프로퍼티 값을 item 변수에 할당
  const item = res.value;
  console.log(item); // 1 2 3
}
```

### 34.4 이터러블과 유사 배열 객체<a name="34.4"></a>
---
#### ✅ 유사 배열 객체: 배열 처럼 인덱스로 프로퍼티 값에 접근할 수 있고 length 프로퍼티를 갖는 객체, `for문`으로 순회 가능
-> 그러나 일반 객체이므로 `for...of문`으로 순회 불가능

#### ES6에서 이터러블이 도입되면서 `arguments`, `NodeList`, `HTMLCollection`은 유사 배열 객체이면서 이터러블

### 34.5 이터레이션 프로토콜의 필요성<a name="34.5"></a>
---

- 이터러블은 `for...of문`, `스프레드 문법`, `배열 디스트럭처링 할당`과 같은 데이터 소비자에 의해 사용되므로 `데이터 공급자`의 역할을 한다!

- 다양한 데이터 공급자가 각자의 순회 방식을 갖는다면 데이터 소비자는 다양한 데이터 공급자의 순회 방식을 모두 지원해야 하는데 효율적이지 않으므로 `데이터 공급자가 이터레이션 프로토콜을 준수하도록 규정`하면 소비자는 이터레이션 프로토콜만 지원하도록 구현

#### ✅ 데이터 소비자와 데이터 공급자를 연결하는 인터페이스의 역할

### 34.6 사용자 정의 이터러블<a name="34.5"></a>
---
#### 34.6.1 사용자 정의 이터러블 구현
- 이터레이션 프로토콜을 준수하지 않는 일반 객체도 이터레이션 프로토콜을 준수하도록 구현하면 `사용자 정의 이터러블`

```js
// 피보나치 수열을 구현한 사용자 정의 이터러블
const fibonacci = {
  // Symbol.iterator 메서드를 구현하여 이터러블 프로토콜을 준수
  [Symbol.iterator]() {
    let [pre, cur] = [0, 1]; 
    const max = 10; // 수열의 최대값

    // Symbol.iterator 메서드는 next 메서드를 소유한 이터레이터를 반환
    // next 메서드는 이터레이터 리절트 객체를 반환
    return {
      next() {
        [pre, cur] = [cur, pre + cur]; /
        // 이터레이터 리절트 객체를 반환
        return { value: cur, done: cur >= max };
      }
    };
  }
};

// 이터러블인 fibonacci 객체를 순회할 때마다 next 메서드가 호출
for (const num of fibonacci) {
  console.log(num); // 1 2 3 5 8
}

// 직접 순회 방식
// 이터러블의 Symbol.iterator 메서드는 이터레이터를 반환
const iterator = iterable[Symbol.iterator]();

console.log(iterator.next()); // { value: 1, done: false }
console.log(iterator.next()); // { value: 2, done: false }
console.log(iterator.next()); // { value: 3, done: false }
console.log(iterator.next()); // { value: 5, done: true }
```

#### 34.6.2 이터러블을 생성하는 함수

#### 34.6.3 이터러블이면서 이터레이터인 객체를 생성하는 함수

#### 34.6.4 무한 이터러블과 지연 평가

#### ✅ 배열이나 문자열 등은 모든 데이터를 메모리에 미리 확보한 다음 데이터를 공급하지만 `이터러블은 지연 평가(Lazy evaluation)를 통해 데이터를 생성`

#### ✅ 지연 평가: 데이터가 필요한 시점 이전까지는 미리 데이터를 생성하지 않다가 데이터가 필요한 시점이 되면 그때야 비로소 데이터를 생성하는 기법(평가 결과가 필요할 때까지 평가를 늦추는 기법)

#### ✅ 지연 평가를 사용하면 불필요한 데이터를 미리 생성하지 않고 `필요한 데이터를 필요한 순간에 생성하므로 빠른 실행 속도`를 기대할 수 있고 `불필요한 메모리를 소비하지 않으며` `무한도 표현할 수 있다`는 장점


