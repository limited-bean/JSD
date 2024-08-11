# 32. String

- 문자열을 다룰 수 있는 String 빌트인 객체

## 32.1 String 생성자 함수

```js
const strObj = new String();
console.log(strObj); // String {length: 0, [[PrimitiveValue]]: ""}

const strObj = new String('Lee');
console.log(strObj); // String {0: "L", 1: "e", 2: "e", length: 3, [[PrimitiveValue]]: "Lee"}

const str = String('Lee');
console.log(str); // 'Lee'
```

- new 연산자와 함께 호출하여 String 인스턴스를 생성할 수 있다
- 인수를 전달하지 않고 new 연산자와 함께 호출하면 빈 문자열을 할당한 String 래퍼 객체를 생성한다.

- String 래퍼 객체는
  - length 프로퍼티와 인덱스를 나타내는 숫자 형식의 문자열을 프로퍼티 키로,
  - 각 문자를 프로퍼티 값으로 가지는
  - 유사 배열 객체이면서 이터러블

```js
const strObj = new String('Lee');
console.log(strObj);
console.log(strObj[0]); // L

// 문자열은 원시값이므로 변경할 수 없다. 이때 에러가 발생하지 않는다.
strObj[0] = 'S';
console.log(strObj); // 'Lee'
```

- String 생성자 함수의 인수로 문자열이 아닌 값을 전달하면 인수를 문자열로 강제 변환

```js
let strObj = new String(123);
console.log(strObj);
// String {0: "1", 1: "2", 2: "3", length: 3, [[PrimitiveValue]]: "123"}

strObj = new String(null);
console.log(strObj);
// String {0: "n", 1: "u", 2: "l", : "l", length: 4, [[PrimitiveValue]]: "null"}
```

- new 연산자를 사용하지 않고 String 생성자 함수를 호출하면 String 인스턴스가 아닌 문자열을 반환
  - 명시적 타입 변환에 쓸 수 있음

```js
// 숫자 타입 => 문자열 타입
String(1); // -> "1"
String(NaN); // -> "NaN"
String(Infinity); // -> "Infinity"

// 불리언 타입 => 문자열 타입
String(true); // -> "true"
String(false); // -> "false"
```

## 32.2 length 프로퍼티

- length 프로퍼티는 문자열의 문자 개수를 반환

```js
'Hello'.length; // -> 5
'안녕하세요!'.length; // -> 6
```

## 32.3 String 메서드

- String 객체는 원본 String 래퍼 객체를 직접 변경하는 메서드는 존재하지 않음
  → String 메서드는 언제나 새로운 문자열을 반환

### 1. String.prototype.indexOf

- 대상 문자열에서 인수로 전달받은 문자열을 검색하여 첫 번째 인덱스를 반환
- 검색에 실패하면 -1을 반환

```js
const str = 'Hello World';

// 문자열 str에서 'l'을 검색하여 첫 번째 인덱스를 반환한다.
str.indexOf('l'); // -> 2

// 문자열 str에서 'or'을 검색하여 첫 번째 인덱스를 반환한다.
str.indexOf('or'); // -> 7

// 문자열 str에서 'x'를 검색하여 첫 번째 인덱스를 반환한다. 검색에 실패하면 -1을 반환한다.
str.indexOf('x'); // -> -1
```

- 2번째 인수로 검색 시작할 인덱스 지정 가능

```js
// 문자열 str의 인덱스 3부터 'l'을 검색하여 첫 번째 인덱스를 반환한다.
str.indexOf('l', 3); // -> 3
```

- 대상 문자열에 특정 문자열 존재하는지 확인할 때 유용
  - String.prototype.includes 사용해도 됨 (가독성 더 좋음)(ES6에서 도임)

```js
if (str.indexOf('Hello') !== -1) {
  // 문자열 str에 'Hello'가 포함되어 있는 경우에 처리할 내용
}

if (str.includes('Hello')) {
  // 문자열 str에 'Hello'가 포함되어 있는 경우에 처리할 내용
}
```

### 2. String.prototype.search

- 대상 문자열에서 인수로 전달받은 정규 표현식과 매치하는 문자열을 검색해 일치하는 문자열의 인덱스를 반환
- 검색에 실패하면 -1을 반환

```js
const str = 'Hello world';

// 문자열 str에서 정규 표현식과 매치하는 문자열을 검색하여 일치하는 문자열의 인덱스를 반환한다.
str.search(/o/); // -> 4
str.search(/x/); // -> -1
```

### 3. String.prototype.includes

- 대상 문자열에 인수로 전달받은 문자열이 포함되어 있는지 확인해 그 결과를 boolean으로 반환
- 2번째 인수로 검색을 시작할 인덱스 지정 가능

```js
const str = 'Hello world';

str.includes('Hello'); // -> true
str.includes(''); // -> true
// 인수 ''는 모든 문자열에 대하여 true

str.includes('x'); // -> false
str.includes(); // -> false

// 문자열 str의 인덱스 3부터 'l'이 포함되어 있는지 확인
str.includes('l', 3); // -> true
str.includes('H', 3); // -> false
```

### 4. String.prototype.startsWith

- 대상 문자열이 인수로 전달받은 문자열로 시작하는지 확인하여 그 결과를 불리언으로 반환
- 2번째 인수로 검색을 시작할 인덱스 전달 가능

```js
const str = 'Hello world';

// 문자열 str이 'He'로 시작하는지 확인
str.startsWith('He'); // -> true
// 문자열 str이 'x'로 시작하는지 확인
str.startsWith('x'); // -> false

// 문자열 str의 인덱스 5부터 시작하는 문자열이 ' '로 시작하는지 확인
str.startsWith(' ', 5); // -> true
```

### 5. String.prototype.endsWith

- 대상 문자열이 인수로 전달받은 문자열로 끝나는지 확인하여 그 결과를 불리언으로 반환
- 2번째 인수로 **검색할 문자열의 길이** 전달 가능

```js
const str = 'Hello world';

// 문자열 str이 'ld'로 끝나는지 확인
str.endsWith('ld'); // -> true
// 문자열 str이 'x'로 끝나는지 확인
str.endsWith('x'); // -> false

// 문자열 str의 처음부터 5자리까지('Hello')가 'lo'로 끝나는지 확인
str.endsWith('lo', 5); // -> true
```

### 6. String.prototype.charAt

- 대상 문자열에서 인수로 전달받은 인덱스에 위치한 문자를 검색하여 반환

```js
const str = 'Hello';

for (let i = 0; i < str.length; i++) {
  console.log(str.charAt(i)); // H e l l o
}
```

- 인덱스는 `0` ~ `문자열.length - 1` 사이의 정수여야 함
  - 범위를 벗어난 정수인 경우 빈 문자열을 반환

```js
// 인덱스가 문자열의 범위(0 ~ str.length-1)를 벗어난 경우 빈문자열을 반환한다.
str.charAt(5); // -> ''
```

### 7. String.prototype.substring

- 대상 문자열에서 첫 번째 인수로 전달받은 인덱스에 위치하는 문자부터 두 번째 인수로 전달받은 인덱스에 위치하는 문자의 바로 이전 문자까지의 부분 문자열을 반환

```js
const str = 'Hello World';

// 인덱스 1부터 인덱스 4 이전까지의 부분 문자열을 반환한다.
str.substring(1, 4); // -> ell
```

- 두 번째 인수 생략 가능 → 첫 번째 인수 ~ 마지막 문자까지 부분 문자열 반환됨

```js
const str = 'Hello World';

// 인덱스 1부터 마지막 문자까지 부분 문자열을 반환한다.
str.substring(1); // -> 'ello World'
```

- 첫 번째 인수 > 두 번째 인수인 경우 두 인수는 교환됨
- 인수 < 0이거나 NaN인 경우 0으로 취급됨
- 인수 > 문자열의 길이 인 경우 인수는 문자열의 길이로 취급됨

```js
const str = 'Hello World'; // str.length == 11

// 첫 번째 인수 > 두 번째 인수인 경우 두 인수는 교환된다.
str.substring(4, 1); // -> 'ell'

// 인수 < 0 또는 NaN인 경우 0으로 취급된다.
str.substring(-2); // -> 'Hello World'

// 인수 > 문자열의 길이(str.length)인 경우 인수는 문자열의 길이(str.length)으로 취급된다.
str.substring(1, 100); // -> 'ello World'
str.substring(20); // -> ''
```

- RangeError가 발생하지 않는 이유 검색해 봤는데 초기버전부터 설계가 그렇게 되어 있네요

### 8. String.prototype.slice

- substring 메서드와 동일하게 동작
- slice 메서드에는 음수인 인수를 전달할 수 있음
  - 음수인 인수를 전달하면 대상 문자열의 뒤에서부터 시작하여 문자열을 잘라내어 반환함

```js
const str = 'hello world';

// substring과 slice 메서드는 동일하게 동작한다.
// 0번째부터 5번째 이전 문자까지 잘라내어 반환
str.substring(0, 5); // -> 'hello'
str.slice(0, 5); // -> 'hello'

// 인덱스가 2인 문자부터 마지막 문자까지 잘라내어 반환
str.substring(2); // -> 'llo world'
str.slice(2); // -> 'llo world'

// 인수 < 0 또는 NaN인 경우 0으로 취급된다.
str.substring(-5); // -> 'hello world'
// slice 메서드는 음수인 인수를 전달할 수 있다. 뒤에서 5자리를 잘라내어 반환한다.
str.slice(-5); // ⟶ 'world'
```

### 9. String.prototype.toUpperCase

- 대상 문자열을 모두 대문자로 변경한 문자열을 반환

```js
const str = 'Hello World!';

str.toUpperCase(); // -> 'HELLO WORLD!'
```

### 10. String.prototype.toLowerCase

- 대상 문자열을 모두 소문자로 변경한 문자열을 반환

```js
const str = 'Hello World!';

str.toLowerCase(); // -> 'hello world!'
```

### 11. String.prototype.trim

- 대상 문자열 앞뒤에 공백 문자 있을 경우 이를 제거한 문자열을 반환

```js
const str = '   foo  ';

str.trim(); // -> 'foo'
```

- `String.prototype.trimStart`, `String.prototype.trimEnd`를 사용하면 대상 문자열 앞 또는 뒤에 공백 문자 있을 경우 이를 제거한 문자열을 반환함

```js
const str = '   foo  ';

// String.prototype.{trimStart,trimEnd} : Proposal stage 4
str.trimStart(); // -> 'foo  '
str.trimEnd(); // -> '   foo'
```

- 공백은 `String.prototype.replace` 메서드에 정규 표현식을 인수로 전달하여 공백 문자를 제거할 수도 있음

```js
const str = '   foo  ';

// 첫 번째 인수로 전달한 정규 표현식에 매치하는 문자열을 두 번째 인수로 전달한 문자열로 치환한다.
str.replace(/\s/g, ''); // -> 'foo'
str.replace(/^\s+/g, ''); // -> 'foo  '
str.replace(/\s+$/g, ''); // -> '   foo'
```

### 12. String.prototype.repeat

- 대상 문자열을 인수로 전달받은 정수만큼 반복해 연결한 새로운 문자열을 반환
- 인수로 전달받은 수가
  - 0이면 빈 문자열 반환
  - 음수면 RangeError를 발생
  - 인수 생략하면 기본값 0

```js
const str = 'abc';

str.repeat(); // -> ''
str.repeat(0); // -> ''
str.repeat(1); // -> 'abc'
str.repeat(2); // -> 'abcabc'
str.repeat(2.5); // -> 'abcabc' (2.5 → 2)
str.repeat(-1); // -> RangeError: Invalid count value
```

### 13. String.prototype.replace

- 대상 문자열에서 첫 번째 인수로 전달받은 문자열 또는 정규 표현식을 검색하여 두 번째 인수로 전달한 문자열로 치환한 문자열을 반환함
- 검색된 문자열이 여러 개일 경우 첫 번째로 검색된 문자열만 치환
-

```js
const str = 'Hello world';

// str에서 첫 번째 인수 'world'를 검색하여 두 번째 인수 'Lee'로 치환한다.
str.replace('world', 'Lee'); // -> 'Hello Lee'

const str = 'Hello world world';

str.replace('world', 'Lee'); // -> 'Hello Lee world'
```

- 특수한 교체 패턴 사용 가능
  - $&는 검색된 문자열을 의미

```js
const str = 'Hello world';

// 특수한 교체 패턴을 사용할 수 있다. ($& => 검색된 문자열)
str.replace('world', '<strong>$&</strong>');
```

- 정규 표현식을 전달

```js
const str = 'Hello Hello';

// 'hello'를 대소문자를 구별하지 않고 전역 검색한다.
str.replace(/hello/gi, 'Lee'); // -> 'Lee Lee'
```

- 두 번째 인수로 치환 함수 전달 가능
  - 첫 번째 인수로 전달한 문자열/정규 표현식에 매치한 결과를 두 번째 인수로 전달한 치환함수의 인수로 전달하면서 호출
  - 치환함수가 반환한 결과와 매치 결과를 치환
- 카멜 케이스 → 스네이크 케이스 / 스네이크 케이스 → 카멜 케이스로 변경하는 함수

```js
// 카멜 케이스를 스네이크 케이스로 변환하는 함수
function camelToSnake(camelCase) {
  // /.[A-Z]/g는 임의의 한 문자와 대문자로 이루어진 문자열에 매치한다.
  // 치환 함수의 인수로 매치 결과가 전달되고, 치환 함수가 반환한 결과와 매치 결과를 치환한다.
  return camelCase.replace(/.[A-Z]/g, (match) => {
    console.log(match); // 'oW'
    return match[0] + '_' + match[1].toLowerCase();
  });
}

const camelCase = 'helloWorld';
camelToSnake(camelCase); // -> 'hello_world'

// 스네이크 케이스를 카멜 케이스로 변환하는 함수
function snakeToCamel(snakeCase) {
  // /_[a-z]/g는 _와 소문자로 이루어진 문자열에 매치한다.
  // 치환 함수의 인수로 매치 결과가 전달되고, 치환 함수가 반환한 결과와 매치 결과를 치환한다.
  return snakeCase.replace(/_[a-z]/g, (match) => {
    console.log(match); // '_w'
    return match[1].toUpperCase();
  });
}

const snakeCase = 'hello_world';
snakeToCamel(snakeCase); // -> 'helloWorld'
```

- & 최근에 ChatGPT로 만든 함수!

```js
const maskEmail = (email) =>
  email.replace(/^(.{2})([^@]*)/, (match, p1, p2) => {
    console.log(match, p1, p2);
    return p1 + '*'.repeat(p2.length);
  });

maskEmail('ltr0121@naver.com');
// ltr0121 lt r0121
// lt*****@naver.com
```

### 14. String.prototype.split

- 대상 문자열에서 첫 번째 인수로 전달한 문자열 또는 정규 표현식을 검색하여 문자열을 구분한 후 분리된 각 문자열로 이루어진 배열을 반환
- 빈 문자열을 전달하면 각 문자를 모두 분리
- 인수를 생략하면 대상 문자열 전체를 단일요소로 하는 배열을 반환 (`'hello'` → `['hello']`)

```js
const str = 'How are you doing?';

// 공백으로 구분(단어로 구분)하여 배열로 반환한다.
str.split(' '); // -> ["How", "are", "you", "doing?"]

// \s는 여러 가지 공백 문자(스페이스, 탭 등)를 의미한다. 즉, [\t\r\n\v\f]와 같은 의미다.
str.split(/\s/); // -> ["How", "are", "you", "doing?"]

// 인수로 빈 문자열을 전달하면 각 문자를 모두 분리한다.
str.split(''); // -> ["H", "o", "w", " ", "a", "r", "e", " ", "y", "o", "u", " ", "d", "o", "i", "n", "g", "?"]

// 인수를 생략하면 대상 문자열 전체를 단일 요소로 하는 배열을 반환한다.
str.split(); // -> ["How are you doing?"]
```

- 두 번째 인수로 배열의 길이 지정
