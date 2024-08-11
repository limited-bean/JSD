# 33. Symbol

## 33.1 심벌이란?

- ES6에서 도입된 7번째 데이터 타입으로 변경 불가능한 원시 타입의 값
- 다른 값과 중복되지 않는 유일무이한 값
- 이름의 충돌 위험이 없는 유일한 프로퍼티 키를 만들기 위해 사용한다
  - % 프로퍼티 키로 사용할 수 있는 값: 빈 문자열을 포함하는 모든 문자열 또는 심벌

## 33.2 심벌 값의 생성

### Symbol 함수

- 심벌 값은 Symbol 함수를 호출하여 생성해야 함
- 이때 생성된 심벌 값은 외부로 노출되지 않아 확인할 수 없으며
- 다른 값과 절대 중복되지 않는 유일무이한 값

```js
// Symbol 함수를 호출하여 유일무이한 심벌 값을 생성한다.
const mySymbol = Symbol();
console.log(typeof mySymbol); // symbol

// 심벌 값은 외부로 노출되지 않아 확인할 수 없다.
console.log(mySymbol); // Symbol()
```

- new 연산자와 함께 호출하지 않는다

```Js
new Symbol(); // TypeError: Symbol is not a constructor
```

- 선택적으로 문자열을 인수로 전달가능
- 생성된 심벌 값에 대한 설명으로 디버깅 용도로만 사용되고 값 생성에 영향을 주지 않음
- 인수로 전달한 문자열이 같더라도 생성된 심벌 값은 유일무이한 값

```js
// 심벌 값에 대한 설명이 같더라도 유일무이한 심벌 값을 생성한다.
const mySymbol1 = Symbol('mySymbol');
const mySymbol2 = Symbol('mySymbol');

console.log(mySymbol1 === mySymbol2); // false
```

- 객체처럼 접근하면 래퍼 객체를 암묵적 생성

```js
const mySymbol = Symbol('mySymbol');

// 심벌도 레퍼 객체를 생성한다
console.log(mySymbol.description); // mySymbol
console.log(mySymbol.toString()); // Symbol(mySymbol)
```

- 문자열이나 숫자 타입으로 암묵적 변환되지 않음

```js
const mySymbol = Symbol();

// 심벌 값은 암묵적으로 문자열이나 숫자 타입으로 변환되지 않는다.
console.log(mySymbol + ''); // TypeError: Cannot convert a Symbol value to a string
console.log(+mySymbol); // TypeError: Cannot convert a Symbol value to a number
```

- 불리언 타입으로는 암묵적 타입 변환됨

```js
const mySymbol = Symbol();

// 불리언 타입으로는 암묵적으로 타입 변환된다.
console.log(!!mySymbol); // true

// if 문 등에서 존재 확인을 위해 사용할 수 있다.
if (mySymbol) console.log('mySymbol is not empty.');
```

### Symbol.for / Symbol.keyFor 메서드

- Symbol.for 메서드는 인수로 전달받은 문자열을 키로 사용하여 키와 심벌 값의 쌍들이 저장되어 있는 심벌 레지스트리에서 해당 키와 일치하는 심벌 값을 검색
  - 검색에 성공하면 새로운 심벌 값을 생성하지 않고 검색된 심벌 값 반환
  - 검색에 실패하면 새로운 심벌 값을 생성하여 Symbol.for 메서드의 인수로 전달된 키로 전역 심벌 제리스트리에 저장, 생성된 심벌 값을 반환

```js
// 전역 심벌 레지스트리에 mySymbol이라는 키로 저장된 심벌 값이 없으면 새로운 심벌 값을 생성
const s1 = Symbol.for('mySymbol');
// 전역 심벌 레지스트리에 mySymbol이라는 키로 저장된 심벌 값이 있으면 해당 심벌 값을 반환
const s2 = Symbol.for('mySymbol');

console.log(s1 === s2); // true
```

- Symbol.for 메서드를 사용하면 애플리케이션 전역에서 중복되지 않는 유일무이한 상수인 심벌 값을 단 하나만 생성하여 전역 심벌 레지스트리를 통해 공유할 수 있음

  - ? 리액트에서도 애플리케이션 전역에서 사용할 수 있을가

- Symbol.keyFor 메서드를 사용하면 전역 심벌 레지스트리에 저장된 심벌 값의 키를 추출할 수 있다

```js
// 전역 심벌 레지스트리에 mySymbol이라는 키로 저장된 심벌 값이 없으면 새로운 심벌 값을 생성
const s1 = Symbol.for('mySymbol');
// 전역 심벌 레지스트리에 저장된 심벌 값의 키를 추출
Symbol.keyFor(s1); // -> mySymbol

// Symbol 함수를 호출하여 생성한 심벌 값은 전역 심벌 레지스트리에 등록되어 관리되지 않는다.
const s2 = Symbol('foo');
// 전역 심벌 레지스트리에 저장된 심벌 값의 키를 추출
Symbol.keyFor(s2); // -> undefined
```

## 33.3 심벌과 상수

```js
// 위, 아래, 왼쪽, 오른쪽을 나타내는 상수를 정의한다.
// 이때 값 1, 2, 3, 4에는 특별한 의미가 없고 상수 이름에 의미가 있다.
const Direction = {
  UP: 1,
  DOWN: 2,
  LEFT: 3,
  RIGHT: 4,
};

// 변수에 상수를 할당
const myDirection = Direction.UP;

if (myDirection === Direction.UP) {
  console.log('You are going UP.');
}
```

- 위 예제와 같이 값에는 특별한 의미가 없고 상수 이름 자체에 의미가 있는 경우가 있음
- 이때 상수 값이 변경될 수 있으며 다른 변수 값과 중복될 수도 있다는 문제가 있음
- 이러한 경우 변경/중복될 가능성이 있는 무의미한 상수 대신 중복될 가능성이 없는 심벌 값을 사용할 수 있다

```js
// 위, 아래, 왼쪽, 오른쪽을 나타내는 상수를 정의한다.
// 중복될 가능성이 없는 심벌 값으로 상수 값을 생성한다.
const Direction = {
  UP: Symbol('up'),
  DOWN: Symbol('down'),
  LEFT: Symbol('left'),
  RIGHT: Symbol('right'),
};

const myDirection = Direction.UP;

if (myDirection === Direction.UP) {
  console.log('You are going UP.');
}
```

- $ + 자바스크립트 enum 사용하기
  - enum은 명명된 숫자 상수의 집합으로 열거형이라고 부름
  - 자바스크립트는 enum을 지원하지 않지만 타입스크립트에서는 enum을 지원함
  - 자바스크립트에서 enum을 흉내내 사용하려면 객체의 변경을 방지하기 위해 객체를 동결하는 `Object.freeze` 메서드와 심벌 값을 사용한다

```js
// JavaScript enum
// Direction 객체는 불변 객체이며 프로퍼티는 유일무이한 값이다.
const Direction = Object.freeze({
  UP: Symbol('up'),
  DOWN: Symbol('down'),
  LEFT: Symbol('left'),
  RIGHT: Symbol('right'),
});

const myDirection = Direction.UP;

if (myDirection === Direction.UP) {
  console.log('You are going UP.');
}
```

## 33.4 심벌과 프로퍼티 키

- 심벌 값으로 프로퍼티 키를 동적 생성하여 프로퍼티 만들어 보기

```js
const obj = {
  // 심벌 값으로 프로퍼티 키를 생성
  [Symbol.for('mySymbol')]: 1,
};

obj[Symbol.for('mySymbol')]; // -> 1
```

- 심벌 값으로 프로퍼티 키를 만들면 다른 프로퍼티 키와 **절대 충돌하지 않는다**

## 33.5 심벌과 프로퍼티 은닉

- 심벌 값으로 프로퍼티 키를 생성하면 그 프로퍼티는 for...in 문이나 Object.keys, Object.getOwnPropertyNames 메서드로 찾을 수 없다
  **→ 외부에 노출할 필요가 없는 프로퍼티를 은닉할 수 있음!**

```js
const obj = {
  // 심벌 값으로 프로퍼티 키를 생성
  [Symbol('mySymbol')]: 1,
};

for (const key in obj) {
  console.log(key); // 아무것도 출력되지 않는다.
}

console.log(Object.keys(obj)); // []
console.log(Object.getOwnPropertyNames(obj)); // []
```

- 하지만 프로퍼티를 완전히 숨길 수 있는 것은 아님
- Object.getOwnPropertySymbols 메서드를 사용하면 심벌 값을 프로퍼티 키로 사용해 만든 프로퍼티를 찾을 수 있음

```Js
const obj = {
  // 심벌 값으로 프로퍼티 키를 생성
  [Symbol('mySymbol')]: 1
};

// getOwnPropertySymbols 메서드는 인수로 전달한 객체의 심벌 프로퍼티 키를 배열로 반환한다.
console.log(Object.getOwnPropertySymbols(obj)); // [Symbol(mySymbol)]

// getOwnPropertySymbols 메서드로 심벌 값도 찾을 수 있다.
const symbolKey1 = Object.getOwnPropertySymbols(obj)[0];
console.log(obj[symbolKey1]); // 1
```

## 33.6 심벌과 표준 빌트인 객체 확장

- 표준 빌트인 객체에 사용자 정의 메서드를 직접 추가하여 확장하는 것은 권장하지 않음
- 표준 빌트인 객체는 읽기 전용으로 사용하는 것이 좋다

→ 표준 빌트인 객체 확장 예시

```js
// 표준 빌트인 객체를 확장하는 것은 권장하지 않는다.
Array.prototype.sum = function () {
  return this.reduce((acc, cur) => acc + cur, 0);
};

[1, 2].sum(); // -> 3
```

- 이유는 바로바로

  - 개발자가 직접 추가한 메서드와 미래에 표준 사양으로 추가될 메서드의 이름이 중복될 수 있기 때문임

- 중복된 심벌 값으로 프로퍼티 키를 생성하여 표준 빌트인 객체를 확장한다면?
  - 그 어떤 프로퍼티 키와 충돌하지 않아서 안전하게 표준 빌트인 객체 확장 가능

![](https://i.imgur.com/CPKAN22.png)

```js
// 심벌 값으로 프로퍼티 키를 동적 생성하면 다른 프로퍼티 키와 절대 충돌하지 않아 안전하다.
Array.prototype[Symbol.for('sum')] = function () {
  return this.reduce((acc, cur) => acc + cur, 0);
};

[1, 2][Symbol.for('sum')](); // -> 3
```

## 33.7 Well-known Symbol

- 자바스크립트가 기본 제공하는 빌트인 심벌 값
  → Symbol 함수의 프로퍼티에 할당되어 있음!

![CPKAN22.png](https://i.imgur.com/CPKAN22.png)

- 자바스크립트가 기본 제공하는 빌트인 심벌 값을 **Well-known Symbol**이라고 한다

  - 자바스크립트 내부 알고리즘에 사용됨

- Array, String, Map, Set, TypedArray, arguments, NodeList, HTMLCollection과 같이 for...of 문으로 순회가능한 빌트인 이터러블은 Well-known Symbol인 Symbol.iterator를 키로 가지는 메서드를 가지고
- Symbol.iterator 메서드를 호출하면 이터레이터를 반환하도록 되어 있다
- 빌트인 이터러블은 이 규정 = 이터레이션 프로토콜을 준수함

- 이터러블을 만들 수 있는 규칙을 이터레이션 프로토콜이라고 함

  > [!이터러블의 정의]
  >
  > 1. Symbol.iterator라고 하는 메서드를 가지고 있는 객체
  > 2. Symbol.iterator가 next라는 함수가 포함된 객체를 반환해야 함
  > 3. next 함수는 { value }를 반환해야 함

- 빌트인 이터러블이 아닌 일반 객체를 이터러블처럼 동작하도록 구현하고 싶다면 이터레이션 프로토콜을 따르면 됨
- ECMAScript 사양에 규정되어 있는 대로 Well-known Symbol인 Symbol.iterator를 키로 가지는 메서드를 객체에 추가하고 이터레이터를 반환하도록 구현하면 그 객체는 이터러블이 된다

```js
// 1 ~ 5 범위의 정수로 이루어진 이터러블
const iterable = {
  // Symbol.iterator 메서드를 구현하여 이터러블 프로토콜을 준수
  [Symbol.iterator]() {
    let cur = 1;
    const max = 5;
    // Symbol.iterator 메서드는 next 메서드를 소유한 이터레이터를 반환
    return {
      next() {
        return { value: cur++, done: cur > max + 1 };
      },
    };
  },
};

for (const num of iterable) {
  console.log(num); // 1 2 3 4 5
}
```

- 이처럼 심벌은 중복되지 않는 상수 값을 생성하는 것은 물론 기존에 작성된 코드에 영향을 주지 않고 새로운 프로퍼티를 추가하기 위해, = 하위 호환성을 보장하기 위해 도입되었다

## Symbol을 사용하는 예시

- react query에는 useQuery에 enabled 옵션이 있음
  - 쿼리가 자동으로 실행되는 것을 막는 옵션임

```ts
function Todos() {
  const { isLoading, isError, data, error, refetch, isFetching } = useQuery({
    queryKey: ['todos'],
    queryFn: fetchTodoList,
    enabled: false,
  });

  return (
    <div>
      <button onClick={() => refetch()}>Fetch Todos</button>

      {data ? (
        <>
          <ul>
            {data.map((todo) => (
              <li key={todo.id}>{todo.title}</li>
            ))}
          </ul>
        </>
      ) : isError ? (
        <span>Error: {error.message}</span>
      ) : isLoading ? (
        <span>Loading...</span>
      ) : (
        <span>Not ready ...</span>
      )}

      <div>{isFetching ? 'Fetching...' : null}</div>
    </div>
  );
}
```

- 그런데 리액트 쿼리 v5부터 skipToken이라는 것도 사용할 수 있다고 함
  > Typescript users may prefer to use [skipToken](https://tanstack.com/query/latest/docs/framework/react/guides/disabling-queries/#typesafe-disabling-of-queries-using-skiptoken) as an alternative to enabled = false.

> ## Typesafe disabling of queries using skipToken

    If you are using TypeScript, you can use the skipToken to disable a query. This is useful when you want to disable a query based on a condition, but you still want to keep the query to be type safe.

> IMPORTANT: refetch from useQuery will not work with skipToken. Other than that, skipToken works the same as enabled: false.

```js
function Todos() {
  const [filter, setFilter] = React.useState<string | undefined>()

  const { data } = useQuery({
    queryKey: ['todos', filter],
    // ⬇️ disabled as long as the filter is undefined or empty
    queryFn: filter ? () => fetchTodos(filter) : skipToken,
  })

  return (
    <div>
      // 🚀 applying the filter will enable and execute the query
      <FiltersForm onApply={setFilter} />
      {data && <TodosTable data={data} />}
    </div>
  )
}
```

skipToken의 구현체를 살펴보니 Symbol로 구성되어 있음

https://github.com/TanStack/query/blob/a300d484750edb4b64bdefcc86abaac305d73b13/packages/query-core/src/utils.ts#L351

```ts
export const skipToken = Symbol();
export type SkipToken = typeof skipToken;
```

### React Query에서 Symbol을 사용하는 이유

`skipToken`의 경우에는 이와 같은 특성을 활용하여 특정 동작을 표현하는 데 사용됩니다. 즉, `skipToken`이 Symbol로 정의되어 있는 이유는 다음과 같습니다:

1. **고유성 확보**:

   - `skipToken`은 특정한 동작 (쿼리를 생략하는 동작)을 나타냅니다. Symbol을 사용하여 `skipToken`을 정의하면, 다른 어떤 값과도 절대 충돌하지 않는 유일한 값임을 보장할 수 있습니다. 이를 통해 `skipToken`이 매우 특별한 경우임을 명확히 지시할 수 있습니다.

2. **안전성**:

   - Symbol을 사용하여 `skipToken`을 정의하면, 실수로 `skipToken`과 같은 값이 다른 곳에서 사용될 가능성을 최소화할 수 있습니다. 예를 들어, 스트링이나 숫자를 사용했다면 같은 값이 다른 곳에서 우연히 사용될 수 있는 가능성이 있기 때문입니다.

3. **명시적 의미**:

   - 코드에서 `skipToken`을 볼 때, 이는 단순한 값이 아니라 특별한 의도를 가진 값임을 즉시 알 수 있습니다. 이는 코드의 가독성을 높이고, 동료 개발자들이 코드를 이해하는데 도움이 됩니다.

### 실제 예를 분석

```ts
export const skipToken = Symbol();
export type SkipToken = typeof skipToken;
```

여기서 `skipToken`이 Symbol로 정의되어 있는 것을 볼 수 있습니다. 이를 통해 `skipToken`이 유일하고, 변경될 수 없으며, 객체의 속성 등으로 사용되어도 열거되지 않는 특성을 가질 수 있습니다.

### useQuery 예시

```ts
function Todos() {
  const [filter, setFilter] = React.useState<string | undefined>();
  const { data } = useQuery({
    queryKey: ['todos', filter], // ⬇️ filter가 undefined 이거나 빈 값인 경우 쿼리를 비활성화함
    queryFn: filter ? () => fetchTodos(filter) : skipToken,
  });

  return (
    <div>
      {' '}
      // 🚀 필터가 적용되면 쿼리가 활성화되고 실행됨
      <FiltersForm onApply={setFilter} />
      {data && <TodosTable data={data} />}
    </div>
  );
}
```

이 예시에서 `queryFn`이 `skipToken`을 반환하는 것을 볼 수 있습니다.
이는 `filter` 값이 유효하지 않은 경우 쿼리를 생략하게끔 만들어 줍니다.
`filter`가 유효하지 않으면 `queryFn`에 `skipToken`이 할당되고, 이를 통해 React Query가 해당 쿼리를 건너뛸 수 있게 됩니다.
이러한 방식은 `queryFn`의 반환값이 유일한 값을 갖고 있는 Symbol을 통해 명확히 특수한 상황임을 나타내고 쿼리 생략 동작을 안전하게 처리할 수 있게 합니다. 이해에 도움이 되셨기를 바랍니다! 추가적인 질문이나 다른 사항이 있으시면 언제든지 물어봐 주세요.

- 찾다보니 (useQuery에서 사용하는 타입이 아닐 수도) 있는데 queryFn의 타입에 SkipToken 타입이 지정된 모습~

```ts
T extends {

queryFn?:

| QueryFunction<infer TQueryFnData, infer TQueryKey>

| SkipToken

select?: (data: any) => infer TData

throwOnError?: ThrowOnError<any, infer TError, any, any>

}
```
