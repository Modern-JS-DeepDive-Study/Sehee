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
    yield 1
}

// 제너레이터 함수 표현식
const genExpFunc = function* () {
    yield 1
}

// 제너레이터 매서드
const obj = {
    * getObjMethod(){
        yield 1
    }
};

// 제너레이터 클래스 메서드 
class MyClass { 
    *getClsMethod(){
        yield 1 
    }
}
```

### 46.3 제너레이터 객체<a name="46.3"></a>
---

#### ✅ 제너레이터 함수를 호출하면 `이터러블`이면서 `이터레이터`인 `제너레이터 객체`를 반환
- `제너레이터 객체`는 Symbol.iterator 메서드를 상속 받고(이터러블), value와 done 프로퍼티를 갖는 이터레이터 리절트 객체를 반환하는 next 메서드를 소유(이터레이터)
```js
// 제너레이터 함수
function* getFunc(){
    yield 1
    yield 2
    yield 3
}

// 제너레이터 함수 호출 -> 제너레이터 객체 반환
const generator = getFunc();

console.log(Symbol.iterator in generator); // true (이터러블이면서 동시에 이터레이터) 
console.log('next' in generator); //true (next 메서드 소유)
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
function* getFunc(){
    try{
        yield 1
        yield 2
        yield 3
    }catch(e){
        console.error(e);
    }
}

const generator = getFunc();
console.log(generator.next());              // { value: 1, done: false }
console.log(generator.return('끝!'))        // { value: "끝!", done: true }
console.log(generator.throw('에러발생!'))   // 에러발생!
                                            // { value: undefined, done: true }
```

### 46.4 제너레이터의 일시 중지와 재개<a name="46.4"></a>
---
#### ✅ 제너레이터는 yield 키워드와 next 메서드를 통해 `실행을 일시 중지했다가 필요한 시점에 다시 재개 가능`
#### ✅ `yield` 키워드는 제너레이터 함수의 실행을 `일시 중지` 시키거나, `yield` 키워드 뒤에 오는 `표현식의 평가 결과를 제너레이터 함수 호출자에게 반환`
#### ✅ 제너레이터 객체의 `next` 메서드를 호출하면 yield 표헌식까지 실행되고 일시 중지 -> 이 때 함수의 제어권이 `호출자로 양도(yield)`
```js
function* getFunc(){
    yield 1
    yield 2
    yield 3
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
function* genFuc(){
    const x = yield 1;
    const y = yield(x+10);
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

#### 46.5.2 비동기 처리

### 46.6 async/await<a name="46.6"></a>
---

#### 46.6.1 async 키워드

#### 46.6.2 await 키워드

#### 46.6.3 에러 처리
