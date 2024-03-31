# 15. let, const 키워드와 블록 레벨 스코프

## 15.1 `var` 키워드로 선언한 변수의 문제점

#### `var` 키워드로 선언한 변수의 특징

1. 변수 중복 선언 허용

```run-javascript
var x = 1
var y = 2

var x = 100
var y; // 초기화 문 없는 건 무시

console.log(x) // 100
console.log(y) // 2
```

2. 함수 레벨 스코프

```javascript
var x = 1;

if (true) {
  var x = 10;
}

console.log(x); // 10
```

```javascript
var i = 10;
for (var i = 0; i < 5; i++) {
  console.log(i); // 0 1 2 3 4
}

console.log(i); // 5
```

3. 변수 호이스팅

```javascript
console.log(x); // undefined

var x = 1;

console.log(x); // 1
```

## 15.2 `let` 키워드

#### `var` 키워드와 `let` 키워드의 차이점을 중심으로

1. 변수 중복 선언 금지

```run-javascript
let bar = 123;
let bar = 456; // SyntaxError: Identifier 'bar' has already been declared
```

2. 블록 레벨 스코프

```run-javascript
var x = 1
let y = 1

if(true){
  var x = 10
  let y = 10
  let z = 20
}

console.log(x) // 10
console.log(y) // 1
console.log(z) // ReferenceError: z is not defined
```

3. 변수 호이스팅

```run-javascript
console.log(x) // ReferenceError: Cannot access 'x' before initialization
let x = 10;
```

- 참조 에러 발생
- 선언 단계와 초기화 단계가 분리되어 진행된다.
  - 초기화 단계가 실행되기 이전에 변수를 참조하면 참조 에러가 발생한다
  - 스코프의 시작 지점부터 초기화 단계 시작 지점인 변수 선언문까지 변수를 참조할 수 없다
    → 일시적 사각지대
- 호이스팅이 발생하지 않는 것은 아님

4. 전역 객체와 `let`

```javascript
var x = 1;
y = 2; // 암묵적 전역
function foo() {}

console.log(window.x); // 1
console.log(x); // 1
console.log(window.y); // 2
console.log(y); // 2
console.log(window.foo); // f foo() {}
console.log(foo); // f foo() {}

let z = 3;
console.log(window.z); // undefined
console.log(z); // 3
```

- `let` 키워드로 선언한 전역 변수는 전역 객체 `window`의 프로퍼티가 아님
- 개념적인 블록(전역 렉시컬 환경의 선언적 환경 레코드) 내에 존재한다고 한다

## 15.3 `const` 키워드

- 주로 상수 선언을 위해 사용

#### `let` 키워드와의 차이점을 중심으로

1. 선언과 초기화
   - 반드시 선언과 동시에 초기화

```run-javascript
const foo; // SyntaxError: Missing initializer in const declaration
```

- `let` 키워드와 마찬가지로 블록 레벨 스코프, 호이스팅이 발생하지 않는 것처럼 동작함

2. 재할당 금지

```run-javascript
const foo = 1
foo = 2 // TypeError: Assignment to constant variable.
```

3. 상수
   - 상수: 재할당이 금지된 변수
   - `const` 키워드로 선언한 값에원시 값을 할당한 경우 원시 값 = 변경 불가능한 값, 재할당 금지
     → 할당된 값 변경할 수 있는 방법 없음

```javascript
// 매직 넘버의 용도가 무엇인지 명확하게 명시해 주자
const TAX_RATE = 0.1;
let preTaxPrice = 100;
let afterTaxPrice = preTaxPrice + preTaxPrice * TAX_RATE;

const PI = 3.14;
let radius = 5;
let diameter = 5 * PI;
```

4. `const` 키워드와 객체
   - `const` 키워드로 선언한 객체의 값은 변경할 수 있다

```run-javascript
const person = {
  name: 'Lee'
}

person.name = 'Kim'
console.log(person) // { name: 'Kim' }

// 객체 자체를 재할당하는 것은 불가능
person = { name: 'Park' } // TypeError: Assignment to constant variable.
```

## 15.4 `var` vs. `let` vs. `const`

- ES6에서 `var` 키워드 사용은 지양하자
- 변수 선언에는 기본적으로 `const`를 사용하자
- `let`은 재할당이 필요한 경우에 사용하자
