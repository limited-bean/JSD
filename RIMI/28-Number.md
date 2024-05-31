# 28. Number

- 원시 타입 숫자를 다룰 수 있는 표준 빌트인 객체 Number

## 28.1 생성자 함수

Number 객체는 생성자 함수 객체

- `new` 연산자와 함께 호출하여 `Number` 인스턴스를 생성할 수 있음

```js
const numObj = new Number();
console.log(numObj); // Number {[[PrimitiveValue]]: 0}
```

- 인수를 전달하지 않고 Number 생성자 함수를 호출하면 `[[NumberData]]` 내부 슬롯에 0을 할당한 Number 래퍼 객체 생성
- 크롬 개발자 도구에서는 `[[PrimitiveValue]`라는 접근할 수 없는 프로퍼티가 보임 → `[[NumberData]]` 내부 슬롯

```js
const numObj = new Number(10);
console.log(numObj); // Number {[[PrimitiveValue]]: 10}

let numObj = new Number('10');
console.log(numObj); // Number {[[PrimitiveValue]]: 10}

numObj = new Number('Hello');
console.log(numObj); // Number {[[PrimitiveValue]]: NaN}
```

- Number 생성자 함수의 인수로 숫자가 아닌 값을 전달하면 인수를 숫자로 강제 변환
  - 변환 결과가 NaN일 수도 있음

```js
// 문자열 타입 => 숫자 타입
Number('0'); // -> 0
Number('-1'); // -> -1
Number('10.53'); // -> 10.53

// 불리언 타입 => 숫자 타입
Number(true); // -> 1
Number(false); // -> 0
```

- new 연산자를 사용하지 않으면 Number 인스턴스가 아닌 숫자를 반환
- 명시적 타입 변환

## 28.2 Number 프로퍼티

### 1. Number.EPSILON

- 1과 1보다 큰 숫자 중 가장 작은 숫자와의 차이
  - 이 `EPSILON` 속성은 대략 `2.2204460492503130808472633361816E-16` 또는 `2^-52`의 값을 가진다
- 부동소수점 표준 IEEE 754는 2진법으로 변환했을 때 무한 소수가 되어 미세한 오차가 발생할 수밖에 없는 구조적 한계가 있음

```js
0.1 + 0.2; // 0.30000000000000004
0.1 + 0.2 === 0.3; // false
```

> 0.625 같이 이진수 소수점으로 딱 떨어지는 수는 문제없지만 0.1 와 같이 0.0001100110011...(2) 로 무한 반복되는 이진수 실수는 아무리 큰 수를 저장하는 부동 소수점 방식이라 해도 무한대를 저장할수 없으니 결국 메모리 한계까지 소수점을 집어넣고 어느 부분에서 끊어 반올림을 해주어야 한다.
> 즉, 컴퓨터의 메모리는 한정적이기 때문에 실수의 소숫점을 표현할 수 있는 수의 제한이 존재하게 될 수 밖에 없다.
> 그리고 실수를 표현하는 숫자 제한이 있다는 것은 곧 부정확한 실수의 계산값을 초래한다는 뜻이기도 하다.
>
> 출처: [https://inpa.tistory.com/entry/JAVA-☕-실수-표현부동-소수점-원리-한눈에-이해하기](https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EC%8B%A4%EC%88%98-%ED%91%9C%ED%98%84%EB%B6%80%EB%8F%99-%EC%86%8C%EC%88%98%EC%A0%90-%EC%9B%90%EB%A6%AC-%ED%95%9C%EB%88%88%EC%97%90-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0) [Inpa Dev 👨‍💻:티스토리]

- `Number.EPSILON`는 부동소수점으로 인해 발생하는 오차를 해결하기 위해 사용

```js
function isEqual(a, b) {
  // a와 b를 뺀 값의 절대값이 Number.EPSILON보다 작으면 같은 수로 인정한다.
  return Math.abs(a - b) < Number.EPSILON;
}

isEqual(0.1 + 0.2, 0.3); // -> true
```

- % 참고: [(ES6) Number.EPSILON은 왜 2.220446049250313e-16인가? | 오늘도 끄적끄적](https://perfectacle.github.io/2017/08/04/ES6-EPSILON/)

### 2. Number.MAX_VALUE

- 자바스크립트에서 표현할 수 있는 가장 큰 양수 값
- Number.MAX_VALUE보다 큰 숫자는 Infinity

### 3. Number.MIN_VALUE

- 자바스크립트에서 표현할 수 있는 가장 작은 양수 값 `(5 * 10^-324)`
- Number.MIN_VALUE보다 작은 숫자는 0

### 4. Number.MAX_SAFE_INTEGER

- 자바스크립트에서 안전하게 표현할 수 있는 가장 큰 정수값 (`2^53 - 1`)

### 5. Number.MIN_SAFE_INTEGER

- 자바스크립트에서 안전하게 표현할 수 있는 가장 작은 정수값 `(-(2^53 - 1))`

### 6. Number.POSITIVE_INFINITY

- 양의 무한대 === Infinity

### 7. Number.NEGATIVE_INFINITY

- 음의 무한대 === -Infinity

### 8. Number.NaN

- 숫자가 아님(Not a Number)을 나타내는 숫자값
- Number.NaN === window.NaN

## 28.3 Number 메서드

### 1. Number.isFinite

- 인수로 전달된 값이 Infinity / -Infinity인지 검사하여 결과를 불리언으로 반환

```js
// 인수가 정상적인 유한수이면 true를 반환한다.
Number.isFinite(0); // -> true
Number.isFinite(Number.MAX_VALUE); // -> true
Number.isFinite(Number.MIN_VALUE); // -> true
Number.isFinite(Number.MAX_SAFE_INTEGER); // -> true

// 인수가 무한수이면 false를 반환한다.
Number.isFinite(Infinity); // -> false
Number.isFinite(-Infinity); // -> false
Number.isFinite(Number.POSITIVE_INFINITY); // -> false
```

- 인수가 NaN이면 언제나 false를 반환

```js
Number.isFinite(NaN); // -> false
```

- 인수가 숫자가 아니면 반환값은 언제나 false
  - `Number.isFinite`는 빌트인 전역함수 `isFinite`와 차이가 있음
    - `isFinite`는 인수를 암묵적 숫자 타입 변환
    - `Number.isFinite`는 인수를 암묵적 타입 변환하지 않음

```js
+null; // 0

Number.isFinite(null); // -> false
isFinite(null); // -> true
```

### 2. Number.isInteger

- 인수로 전달된 숫자값이 정수인지 검사하여 결과를 불리언 값으로 반환
- 인수를 숫자로 암묵적 타입 변환하지 않음

```js
// 인수가 정수이면 true를 반환한다.
Number.isInteger(0); // -> true
Number.isInteger(123); // -> true
Number.isInteger(-123); // -> true

// 0.5는 정수가 아니다.
Number.isInteger(0.5); // -> false
// '123'을 숫자로 암묵적 타입 변환하지 않는다.
Number.isInteger('123'); // -> false
// false를 숫자로 암묵적 타입 변환하지 않는다.
Number.isInteger(false); // -> false
// Infinity/-Infinity는 정수가 아니다.
Number.isInteger(Infinity); // -> false
Number.isInteger(-Infinity); // -> false
```

### 3. Number.isNaN

- 인수로 전달된 숫자값이 NaN인지 검사하여 결과를 불리언 값으로 반환

```js
Number.isNaN(NaN); // -> true
```

- 숫자가 아닌 인수가 주어졌을 때 반환값은 언제나 false
  - `Number.isNaN`는 빌트인 전역함수 `isNaN`와 차이가 있음
    - `isNaN`는 인수를 암묵적 숫자 타입 변환
    - `Number.isNaN`는 인수를 암묵적 타입 변환하지 않음

```js
+undefined; // NaN

Number.isNaN(undefined); // -> false
isNaN(undefined); // -> true
```

### 4. Number.isSafeInteger

- 인수로 전달된 숫자값이 안전한 정수인지 검사하여 결과를 불리언 값으로 반환
  - 안전한 정수값은 `-(2^53 - 1)`과 `(2^53 - 1)` 사이의 정수값
- 인수를 숫자로 암묵적 타입 변환하지 않음

```js
// 0은 안전한 정수이다.
Number.isSafeInteger(0); // -> true
// 1000000000000000은 안전한 정수이다.
Number.isSafeInteger(1000000000000000); // -> true

// 10000000000000001은 안전하지 않다.
Number.isSafeInteger(10000000000000001); // -> false
// 0.5은 정수가 아니다.
Number.isSafeInteger(0.5); // -> false
// '123'을 숫자로 암묵적 타입 변환하지 않는다.
Number.isSafeInteger('123'); // -> false
// false를 숫자로 암묵적 타입 변환하지 않는다.
Number.isSafeInteger(false); // -> false
// Infinity/-Infinity는 정수가 아니다.
Number.isSafeInteger(Infinity); // -> false
```

### 5. Number.prototype.toExponential

- 숫자를 지수 표기법으로 변환하여 문자열로 반환함
  - **`지수 표기법이란?`**: e 앞에 있는 숫자에 10의 n승을 곱하는 형식으로 수를 나타내는 방식, 매우 크거나 작은 숫자를 표기할 때 주로 사용

```js
(77.1234).toExponential(); // -> "7.71234e+1"
(77.1234).toExponential(4); // -> "7.7123e+1"
(77.1234).toExponential(2); // -> "7.71e+1"
```

- 숫자 리터럴과 함께 사용할 경우 에러 발생
  - 숫자 뒤의 `.`은 의미가 모호하기 때문
    - 부동 소수점 숫자의 소수 구분 기호일 수도 있고
    - 객체 프로퍼티에 접근하는 연산자일 수도 있음
  - 숫자 리터럴 뒤의 `.`을 소수 구분 기호로 해석하기 때문에 에러 발생

```js
77.toExponential(); // -> SyntaxError: Invalid or unexpected token
(77).toExponential(); // -> "7.7e+1"
77 .toExponential(); // -> "7.7e+1"
```

- 정수와 소수 사이에 공백을 포함해서도 사용할 수 있음
  - 숫자와 `.` 사이 공백이 있으면 `.`을 프로퍼티 접근 연산자로 해석

### 6. Number.prototype.toFixed

- 숫자를 반올림하여 문자열로 반환
- 소수점 이하 자릿수 나타내는 0~20 사이의 정수값을 인수로 전달
  - 생략하면 기본값은 0

```js
(12345.6789).toFixed(); // -> "12346"
(12345.6789).toFixed(1); // -> "12345.7"
(12345.6789).toFixed(2); // -> "12345.68"
(12345.6789).toFixed(3); // -> "12345.679"
```

### 7. Number.prototype.toPrecision

- 인수로 전달받은 전체 자릿수까지 유효하도록 나머지 자릿수를 반올림하여 문자열로 반환
- 전체 자릿수로 표현할 수 없는 경우 지수 표기법으로 결과 반환
- 전체 자릿수 나타내는 0~21 사이의 정수값을 인수로 전달

  - 생략하면 기본값은 0

- 전체 자릿수 중 n자리만 유효하다?
- 0은 전체 자릿수가 모두 유효하다

```js
// 전체 자리수 유효. 인수를 전달하지 않으면 기본값 0이 전달된다.
(12345.6789).toPrecision(); // -> "12345.6789"
// 전체 1자리수 유효, 나머지 반올림
(12345.6789).toPrecision(1); // -> "1e+4"
// 전체 2자리수 유효, 나머지 반올림
(12345.6789).toPrecision(2); // -> "1.2e+4"
// 전체 6자리수 유효, 나머지 반올림
(12345.6789).toPrecision(6); // -> "12345.7"
```

### 8. Number.prototype.toString

- 숫자를 문자열로 변환하여 반환
- 진법을 나타내는 2~36 사이 정수값을 인수로 전달할 수 있음
  - 생략하면 기본값 10

```js
// 인수를 생략하면 10진수 문자열을 반환한다.
(10).toString(); // -> "10"
// 2진수 문자열을 반환한다.
(16).toString(2); // -> "10000"
// 8진수 문자열을 반환한다.
(16).toString(8); // -> "20"
// 16진수 문자열을 반환한다.
(16).toString(16); // -> "10"
```
