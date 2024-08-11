# 46. 제너레이터와 async/await

## 46.1 제너레이터란?

**제너레이터**: 코드 블록의 실행을 일시 중지했다가 필요한 시점에 재개할 수 있는 특수한 함수

- 제너레이터와 일반 함수의 차이
  1.  제너레이터 함수는 함수 호출자에게 함수 실행의 제어권을 양도할 수 있다
      - 제너레이터 함수는 함수 호출자가 함수 실행을 일시 중지시키거나 재개시킬 수 있다. = 함수 제어권을 함수 호출자에게 양도할 수 있음
  2.  제너레이터 함수는 함수 호출자와 함수의 상태를 주고받을 수 있다
  3.  제너레이터 함수를 호출하면 제너레이터 객체를 반환한다

## 46.2 제너레이터 함수의 정의

- 제너레이터 함수는 `function*` 키워드로 선언한다
- 하나 이상의 `yield` 표현식을 포함한다

```js
function* genDecFunc() {
  yield 1;
}

const genExpFunc = function* () {
  yield 1;
};

const obj = {
  *genObjMethod() {
    yield 1;
  },
};

class MyClass {
  *genClsMethod() {
    yield 1;
  }
}
```

- 애스터리스크(`*`)의 위치는 function 키워드와 함수 이름 사이라면 어디든지 상관없
  - 지만 일관성 유지를 위해 `function*`을 권장

```js
function* getFunc() {
  yield 1;
}

function* genFunc() {
  yield 1;
}

function* genFunc() {
  yield 1;
}

function* genFunc() {
  yield 1;
}
```

- 제너레이터 함수는 화살표 함수로 정의할 수 없다

```js
const genArrowFunc = * () => {
  yield 1;
}; // SyntaxError: Unexpected token '*'
```

- 제너레이터 함수는 new 연산자와 함께 생성자 함수로 호출할 수 없다

```js
function* genFunc() {
  yield 1;
}

new genFunc(); // TypeError: genFunc is not a constructor
```

## 46.3 제너레이터 객체

- 제너레이터 함수를 호출하면 일반 함수처럼 함수 코드 블록을 실행하는 것이 아니라 제너레이터 객체를 생성해 반환함
- 제너레이터 함수가 반환한 제너레이터 객체는 이터러블이면서 동시에 이터레이터다
  - 제너레이터 객체는 Symbol.iterator 메서드를 상속받는 이터러블이면서 value, done 프로퍼티를 갖는 이터레이터 리절드 객체를 반환하는 next 메서드를 소유하는 이터레이터

```js
function* genFunc() {
  yield 1;
  yield 2;
  yield 3;
}

const generator = genFunc();

console.log(Symbol.iterator in generator); // true

console.log('next' in generator); // true
```

- 제너레이터 객체는 next 메서드를 갖는 이터레이터이지만 이터레이터에는 없는 return, throw 메서드를 가짐
  제너레이터 객체의 세 개 메서드를 호출하면 다음과 같이 동작함
- next 메서드를 호출하면 제너레이터 함수의 yield 표현식까지 코드 블록 실행, yield된 값을 value 프로퍼티 값으로, false를 done 프로퍼티 값으로 갖는 이터레이터 리절트 객체를 반환
- return 메서드를 호출하면 인수로 전달받은 값을 value 프로퍼티 값으로, true를 done 프로퍼티 값으로 갖는 이터레이터 리절트 객체를 반환함

```js
function* genFunc() {
  try {
    yield 1;
    yield 2;
    yield 3;
  } catch (e) {
    console.error(e);
  }
}

const generator = genFunc();

console.log(generator.next()); // {value: 1, done: false}
console.log(generator.return('End!')); // {value: "End!", done: true}
```

- throw 메서드를 호출하면 인수로 전달받은 에러를 발생시키고 undefined를 value 프로퍼티 값으로, true를 done 프로퍼티 값으로 갖는 이터레이터 리절트 객체를 반환

```js
function* genFunc() {
  try {
    yield 1;
    yield 2;
    yield 3;
  } catch (e) {
    console.error(e);
  }
}

const generator = genFunc();

console.log(generator.next()); // {value: 1, done: false}
console.log(generator.throw('Error!')); // {value: undefined, done: true}
```

## 46.4 제너레이터의 일시 중지와 재개

- yield 키워드와 next 메서드를 통해 실행을 일시 중지했다가 필요한 시점에 제너레이터를 다시 재개할 수 있음
- 한 번에 코드 블록의 모든 코드 일괄 실행 ❌
- yield 표현식까지만 실행 ⭕
- yield 키워드는 제너레이터 함수의 실행을 일시 중지시키거나 yield 키워드 뒤에 오는 표현식의 평가 결과를 제너레이터 함수 호출자에게 반환

```js
// 제너레이터 함수
function* genFunc() {
  yield 1;
  yield 2;
  yield 3;
}

// 제너레이터 함수를 호출하면 제너레이터 객체를 반환한다.
// 이터러블이면서 동시에 이터레이터인 제너레이터 객체는 next 메서드를 갖는다.
const generator = genFunc();

// 처음 next 메서드를 호출하면 첫 번째 yield 표현식까지 실행되고 일시 중지된다.
// next 메서드는 이터레이터 리절트 객체({value, done})를 반환한다.
// value 프로퍼티에는 첫 번째 yield 표현식에서 yield된 값 1이 할당된다.
// done 프로퍼티에는 제너레이터 함수가 끝까지 실행되었는지를 나타내는 false가 할당된다.
console.log(generator.next()); // {value: 1, done: false}

// 다시 next 메서드를 호출하면 두 번째 yield 표현식까지 실행되고 일시 중지된다.
// next 메서드는 이터레이터 리절트 객체({value, done})를 반환한다.
// value 프로퍼티에는 두 번째 yield 표현식에서 yield된 값 2가 할당된다.
// done 프로퍼티에는 제너레이터 함수가 끝까지 실행되었는지를 나타내는 false가 할당된다.
console.log(generator.next()); // {value: 2, done: false}

// 다시 next 메서드를 호출하면 세 번째 yield 표현식까지 실행되고 일시 중지된다.
// next 메서드는 이터레이터 리절트 객체({value, done})를 반환한다.
// value 프로퍼티에는 세 번째 yield 표현식에서 yield된 값 3이 할당된다.
// done 프로퍼티에는 제너레이터 함수가 끝까지 실행되었는지를 나타내는 false가 할당된다.
console.log(generator.next()); // {value: 3, done: false}

// 다시 next 메서드를 호출하면 남은 yield 표현식이 없으므로 제너레이터 함수의 마지막까지 실행한다.
// next 메서드는 이터레이터 리절트 객체({value, done})를 반환한다.
// value 프로퍼티에는 제너레이터 함수의 반환값 undefined가 할당된다.
// done 프로퍼티에는 제너레이터 함수가 끝까지 실행되었음을 나타내는 true가 할당된다.
console.log(generator.next()); // {value: undefined, done: true}
```

- 제너레이터 객체의 next 메서드를 호출하면 yield 표현식까지 실행되고 일시 중지됨, 이때 함수의 제어권이 호출자로 양도됨
- 제너레이터 객체의 next 메서드는 value, done 프로퍼티를 갖는 이터레이터 리절트 객체를 반환
- next 메서드가 반환한 이터레이터 리절트 객체의 value 프로퍼티에는 yield 표현식에서 yield된 값이 할당되고 done 프로퍼티에는 제너레이터 함수가 끝까지 실행되었는지를 나타내는 불리언 값 할당

- 제너레이터 객체의 next 메서드에는 인수를 전달할 수 있음
  - 전달한 인수는 yield 표현식을 할당받는 변수에 할당됨

```js
function* genFunc() {
  // 처음 next 메서드를 호출하면 첫 번째 yield 표현식까지 실행되고 일시 중지된다.
  // 이때 yield된 값 1은 next 메서드가 반환한 이터레이터 리절트 객체의 value 프로퍼티에 할당된다.
  // x 변수에는 아직 아무것도 할당되지 않았다. x 변수의 값은 next 메서드가 두 번째 호출될 때 결정된다.
  const x = yield 1;

  // 두 번째 next 메서드를 호출할 때 전달한 인수 10은 첫 번째 yield 표현식을 할당받는 x 변수에 할당된다.
  // 즉, const x = yield 1;은 두 번째 next 메서드를 호출했을 때 완료된다.
  // 두 번째 next 메서드를 호출하면 두 번째 yield 표현식까지 실행되고 일시 중지된다.
  // 이때 yield된 값 x + 10은 next 메서드가 반환한 이터레이터 리절트 객체의 value 프로퍼티에 할당된다.
  const y = yield x + 10;

  // 세 번째 next 메서드를 호출할 때 전달한 인수 20은 두 번째 yield 표현식을 할당받는 y 변수에 할당된다.
  // 즉, const y = yield (x + 10);는 세 번째 next 메서드를 호출했을 때 완료된다.
  // 세 번째 next 메서드를 호출하면 함수 끝까지 실행된다.
  // 이때 제너레이터 함수의 반환값 x + y는 next 메서드가 반환한 이터레이터 리절트 객체의 value 프로퍼티에 할당된다.
  // 일반적으로 제너레이터의 반환값은 의미가 없다.
  // 따라서 제너레이터에서는 값을 반환할 필요가 없고 return은 종료의 의미로만 사용해야 한다.
  return x + y;
}

// 제너레이터 함수를 호출하면 제너레이터 객체를 반환한다.
// 이터러블이며 동시에 이터레이터인 제너레이터 객체는 next 메서드를 갖는다.
const generator = genFunc(0);

// 처음 호출하는 next 메서드에는 인수를 전달하지 않는다.
// 만약 처음 호출하는 next 메서드에 인수를 전달하면 무시된다.
// next 메서드가 반환한 이터레이터 리절트 객체의 value 프로퍼티에는 첫 번째 yield된 값 1이 할당된다.
let res = generator.next();
console.log(res); // {value: 1, done: false}

// next 메서드에 인수로 전달한 10은 genFunc 함수의 x 변수에 할당된다.
// next 메서드가 반환한 이터레이터 리절트 객체의 value 프로퍼티에는 두 번째 yield된 값 20이 할당된다.
res = generator.next(10);
console.log(res); // {value: 20, done: false}

// next 메서드에 인수로 전달한 20은 genFunc 함수의 y 변수에 할당된다.
// next 메서드가 반환한 이터레이터 리절트 객체의 value 프로퍼티에는 제너레이터 함수의 반환값 30이 할당된다.
res = generator.next(20);
console.log(res); // {value: 30, done: true}
```

```js
function* genFunc() {
  const x = yield 1;
  const y = yield x + 10;
  return x + y;
}

const generator = genFunc(0);

let res = generator.next();
console.log(res); // {value: 1, done: false}

res = generator.next(10);
console.log(res); // {value: 20, done: false}

res = generator.next(20);
console.log(res); // {value: 30, done: true}
```

- 제너레이터 함수는 next 메서드와 yield 표현식을 통해 함수 호출자와 함수의 상태를 주고받을 수 있음
- 이러한 제너레이터의 특성을 활용하여 비동기 처리를 동기 처리처럼 구현할 수 있음

## 46.5 제너레이터의 활용

### 이터러블의 구현

⬇️ 이터레이션 프로토콜을 준수하여 무한 피보나치 수열 생성 함수 구현했을 때

```js
// 무한 이터러블을 생성하는 함수
const infiniteFibonacci = (function () {
  let [pre, cur] = [0, 1];

  return {
    [Symbol.iterator]() {
      return this;
    },
    next() {
      [pre, cur] = [cur, pre + cur];
      // 무한 이터러블이므로 done 프로퍼티를 생략한다.
      return { value: cur };
    },
  };
})();

// infiniteFibonacci는 무한 이터러블이다.
for (const num of infiniteFibonacci) {
  if (num > 10000) break;
  console.log(num); // 1 2 3 5 8...2584 4181 6765
}
```

⬇️ 제너레이터를 사용하여 무한 피보나치 수열 생성 함수 구현했을 때

```js
// 무한 이터러블을 생성하는 제너레이터 함수
const infiniteFibonacci = (function* () {
  let [pre, cur] = [0, 1];

  while (true) {
    [pre, cur] = [cur, pre + cur];
    yield cur;
  }
})();

// infiniteFibonacci는 무한 이터러블이다.
for (const num of infiniteFibonacci) {
  if (num > 10000) break;
  console.log(num); // 1 2 3 5 8...2584 4181 6765
}
```

- ? ~~개어려운디~~ => 해결

### 비동기 처리

- next 메서드와 yield 표현식을 통해 함수 호출자와 함수의 상태를 주고받는 특성을 활용하여 프로미스를 사용한 비동기 처리를 동기 처리처럼 구현할 수 있음
  - 프로미스의 후속 처리 메서드 then/catch/finally 없이 비동기 처리 결과 반환하도록 구현할 수 있다는 말

```js
// node-fetch는 node.js 환경에서 window.fetch 함수를 사용하기 위한 패키지다.
// 브라우저 환경에서 이 예제를 실행한다면 아래 코드는 필요 없다.
// https://github.com/node-fetch/node-fetch
const fetch = require('node-fetch');

// 제너레이터 실행기
const async = (generatorFunc) => {
  const generator = generatorFunc(); // ②

  const onResolved = (arg) => {
    const result = generator.next(arg); // ⑤

    return result.done
      ? result.value // ⑨
      : result.value.then((res) => onResolved(res)); // ⑦
  };

  return onResolved; // ③
};

async(function* fetchTodo() {
  // ①
  const url = 'https://jsonplaceholder.typicode.com/todos/1';

  const response = yield fetch(url); // ⑥
  const todo = yield response.json(); // ⑧
  console.log(todo);
  // {userId: 1, id: 1, title: 'delectus aut autem', completed: false}
})(); // ④
```

- async 함수가 호출①되면 인수로 전달받은 제너레이터 함수 fetchTodo를 호출하여 제너레이터 객체를 생성②하고 `onResolved` 함수를 반환③함. `onResolved` 함수는 상위 스코프의 generator 변수를 기억하는 클로저임. async 함수가 반환한 `onResolved` 함수를 즉시 호출④하여 ②에서 생성한 제너레이터 객체의 next 메서드를 처음 호출⑤함
- next 메서드가 처음 호출⑤되면 제너레이터 함수 fetchTodo의 첫 번째 yield 문까지 실행됨 ⑥ 이때 next 메서드가 반환한 `done !== false`이면 fetch 함수가 반환한 프로미스가 resolve한 Response 객체는 onResolved 함수에 인수로 전달하면서 재귀호출 ⑦
- 그러면 Response 객체는 next 메서드에 인수로 전달하면서 next 메서드를 두 번째로 호출함 ⑤ 이때 next 메서드에 인수로 전달한 Response 객체는 제너레이터 함수 fetchTodo의 response 변수에 할당 ⑥, fetchTodo의 두 번째 yield 문까지 실행됨⑧
- `done !== false`이면 두 번째 yield된 response.json 메서드가 반환한 프로미스가 resolve한 todo 객체를 onResolved 함수에 인수로 전달하면서 재귀호출⑦한다.
- onResolved 함수에 인수로 전달된 todo 객체를 next 메서드에 인수로 전달하면서 next 메서드를 세 번째로 호출 ⑤, 이때 next 메서드에 인수로 전달한 todo 객체는 제너레이터 함수 fetchTodo의 todo 변수에 할당 ⑧, 제너레이트 함수 fetchTodo가 끝까지 실행됨
- next 메서드가 반환한 `done === true` 이므로 fetchTodo 함수의 반환값 undefined를 그대로 반환하고 처리를 종료

## 46.6 async/await

- % 혼잣말: async/await가 프로미스랑 같이 안 묶이고 여기 있다는 게 좀 어색함?

- 프로미스를 기반으로 동작하는 `async/await`는 비동기 처리를 하는 프로미스를 동기 처리하는 것처럼 코드 작성할 수 있다.

```js
const fetch = require('node-fetch');

async function fetchTodo() {
  const url = 'https://jsonplaceholder.typicode.com/todos/1';

  const response = await fetch(url);
  const todo = await response.json();
  console.log(todo);
  // {userId: 1, id: 1, title: 'delectus aut autem', completed: false}
}

fetchTodo();
```

### async 함수

- `await` 키워드는 반드시 `async` 함수 내부에서 사용해야 함
- `async` 함수는 `async` 키워드를 사용해 정의하며 언제나 프로미스를 반환함
  - 명시적으로 프로미스 반환하지 않더라도 암묵적으로 반환값을 resolve하는 프로미스를 반환

```Js
// async 함수 선언문
async function foo(n) { return n; }
foo(1).then(v => console.log(v)); // 1

// async 함수 표현식
const bar = async function (n) { return n; };
bar(2).then(v => console.log(v)); // 2

// async 화살표 함수
const baz = async (n) => n;
baz(3).then(v => console.log(v)); // 3

// async 메서드
const obj = {
  async foo(n) { return n; }
};
obj.foo(4).then(v => console.log(v)); // 4

// async 클래스 메서드
class MyClass {
  async bar(n) { return n; }
}
const myClass = new MyClass();
myClass.bar(5).then(v => console.log(v)); // 5
```

- 예외는.. 클래스의 constructor 메서드는 인스턴스를 반환해야 하지만 async 함수는 언제나 프로미스를 반환해야 하기 때문에 async 메서드가 될 수 없음

### await 키워드

- `await` 키워드는 프로미스가 settled 상태가 될때까지 대기하다가 settled 상태가 되면 프로미스가 resolve한 처리 결과를 반환함
- `await` 키워드는 반드시 프로미스 앞에서 사용해야 함
- `await` 키워드는 다음 실행을 일시 중지시켰다가 프로미스가 settled 상태가 되면 재개함

- 모든 프로미스에 `await` 키워드를 사용하는 것은 주의해야 하고, 연관 없는 비동기 처리는 `Promise.all` 메서드를 사용하면 좋다

### 에러 처리

- `async/await`에서 에러처리는 `try...catch` 문을 사용할 수 있음

```js
const fetch = require('node-fetch');

const foo = async () => {
  try {
    const wrongUrl = 'https://wrong.url';

    const response = await fetch(wrongUrl);
    const data = await response.json();

    console.log(data);
  } catch (err) {
    console.error(err); // TypeError: Failed to fetch
  }
};

foo();
```

- 대신 이렇게 하면 일반 에러까지 모두 캐치할 수 있기 때문에 Promise.prototype.catch 후속 처리 메서드를 사용해 에러를 캐치할 수도 있음
  - async 함수내에서 catch 문으로 에러 처리를 하지 않으면 async 함수는 발생한 에러를 reject 하는 프로미스를 반환하기 때문

```js
const fetch = require('node-fetch');

const foo = async () => {
  const wrongUrl = 'https://wrong.url';

  const response = await fetch(wrongUrl);
  const data = await response.json();
  return data;
};

foo().then(console.log).catch(console.error); // TypeError: Failed to fetch
```
