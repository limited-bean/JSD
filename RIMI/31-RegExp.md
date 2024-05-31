# 31. RegExp

## 31.1 정규 표현식이란?

- 정규 표현식: 일정한 패턴을 가진 문자열의 집합을 표현하기 위해 사용하는 형식 언어
- 문자열을 대상으로 패턴 매칭 기능을 제공
- 정규 표현식을 사용하면 반복문과 조건문 없이 패턴을 정의하여 간단히 테스트 가능
- 주석이나 공백을 허용하지 않고 기호를 혼합하여 사용하기 때문에 가독성이 좋지 않음

- (예제) 휴대전화 패턴

```js
const tel = '010-1234-567팔';

// 정규 표현식 리터럴로 휴대폰 전화번호 패턴을 정의한다.
const regExp = /^\d{3}-\d{4}-\d{4}$/;

// tel이 휴대폰 전화번호 패턴에 매칭하는지 테스트(확인)한다.
regExp.test(tel); // -> false
```

## 31.2 정규 표현식의 생성

- 정규 표현식 리터럴 사용하기
  - 패턴과 플래그로 구성됨
  - `/regexp/i`
    - `/`는 시작/종료 기호
    - `regexp`는 패턴
    - `i`는 플래그

```js
const target = 'Is this all there is?';

// 패턴: is
// 플래그: i => 대소문자를 구별하지 않고 검색한다.
const regexp = /is/i;

// test 메서드는 target 문자열에 대해 정규표현식 regexp의 패턴을 검색하여 매칭 결과를 불리언 값으로 반환한다.
regexp.test(target); // -> true
```

- RegExp 생성자 함수 사용하기

```js
const target = 'Is this all there is?';

const regexp = new RegExp(/is/i); // ES6
// const regexp = new RegExp(/is/, 'i');
// const regexp = new RegExp('is', 'i');

regexp.test(target); // -> true
```

- 변수를 사용해 동적으로 RegExp 객체 생성

```js
const count = (str, char) => (str.match(new RegExp(char, 'gi')) ?? []).length;

count('Is this all there is?', 'is'); // -> 3
count('Is this all there is?', 'xx'); // -> 0
```

## 31.3 RegExp 메서드

### 1. RegExp.prototype.exec

- exec 메서드는 인수로 전달받은 문자열에 대해 정규 표현식의 패턴을 검색하여 매칭 결과를 배열로 반환한다.
- 매칭 결과가 없는 경우 null을 반환한다.
- 문자열 내 모든 패턴을 검색하는 g 플래그를 설정해도 첫 번째 매칭 결과만 반환한다.

```Js
const target = 'Is this all there is?';
const regExp = /is/;

regExp.exec(target); // -> ["is", index: 5, input: "Is this all there is?", groups: undefined]
```

### 2. RegExp.prototype.test

- test 메서드는 인수로 전달받은 문자열에 대해 정규 표현식의 패턴을 검색하여 매칭 결과를 불리언 값으로 반환한다.

```js
const target = 'Is this all there is?';
const target2 = 'Hello';
const regExp = /is/;

regExp.test(target); // -> true
regExp.test(target2); // -> false
```

### 3. String.prototype.match

- String 객체가 제공하는 match 메서드는 대상 문자열과 인수로 전달받은 정규 표현식과의 매칭 결과를 배열로 반환한다.
- 모든 패턴을 검색하는 g 플래그를 지정하면 모든 매칭 결과를 반환한다.

```js
const target = 'Is this all there is?';
const target2 = 'Hello';

const regExp = /is/g;

target.match(regExp); // -> ["is", "is"]
target2.match(regExp); // -> null
```

## 31.4 플래그

- 플래그는 정규 표현식의 검색 방식을 설정하기 위해 사용
- 옵션이므로 선택적으로 사용할 수 있음
- 순서와 상관없이 하나 이상의 플래그를 동시에 설정 가능
- 0개의 플래그 사용할 경우 대소문자 구별해서 패턴 검색, 첫 번째 매칭한 대상만 검색하고 종료됨

| 플래그 | 의미        | 설명                                                       |
| ------ | ----------- | ---------------------------------------------------------- |
| i      | ignore case | 대소문자를 구별하지 않고 패턴을 검색                       |
| g      | Global      | 대상 문자열 내에서 패턴과 일치하는 모든 문자열을 전역 검색 |
| m      | multi line  | 문자열의 행이 바뀌더라도 패턴 검색 계속함                  |

```js
const target = 'Is this all there is?';

// target 문자열에서 is 문자열을 대소문자를 구별하여 한 번만 검색한다.
target.match(/is/);
// -> ["is", index: 5, input: "Is this all there is?", groups: undefined]

// target 문자열에서 is 문자열을 대소문자를 구별하지 않고 한 번만 검색한다.
target.match(/is/i);
// -> ["Is", index: 0, input: "Is this all there is?", groups: undefined]

// target 문자열에서 is 문자열을 대소문자를 구별하여 전역 검색한다.
target.match(/is/g);
// -> ["is", "is"]

// target 문자열에서 is 문자열을 대소문자를 구별하지 않고 전역 검색한다.
target.match(/is/gi);
// -> ["Is", "is", "is"]
```

## 31.5 패턴

- 정규 표현식의 패턴은 문자열의 일정한 규칙을 표현하기 위해 사용
- 패턴은 `/`로 열고 닫으며 문자열의 따옴표는 생략함

### 1. 문자열 검색

- 플래그 생략 케이스

```js
const target = 'Is this all there is?';

// 'is' 문자열과 매치하는 패턴. 플래그가 생략되었으므로 대소문자를 구별한다.
const regExp = /is/;

// target과 정규 표현식이 매치하는지 테스트한다.
regExp.test(target); // -> true

// target과 정규 표현식의 매칭 결과를 구한다.
target.match(regExp);
// -> ["is", index: 5, input: "Is this all there is?", groups: undefined]
```

- i 플래그 사용 케이스

```js
const target = 'Is this all there is?';

// 'is' 문자열과 매치하는 패턴. 플래그 i를 추가하면 대소문자를 구별하지 않는다.
const regExp = /is/i;

target.match(regExp);
// -> ["Is", index: 0, input: "Is this all there is?", groups: undefined]
```

- i, g 플래그 사용 케이스

```js
const target = 'Is this all there is?';

// 'is' 문자열과 매치하는 패턴.
// 플래그 g를 추가하면 대상 문자열 내에서 패턴과 일치하는 모든 문자열을 전역 검색한다.
const regExp = /is/gi;

target.match(regExp); // -> ["Is", "is", "is"]
```

### 2. 임의의 문자열 검색

- `.`은 임의의 문자 한개를 의미

```js
const target = 'Is this all there is?';

// 임의의 3자리 문자열을 대소문자를 구별하여 전역 검색한다.
const regExp = /.../g;

target.match(regExp); // -> ["Is ", "thi", "s a", "ll ", "the", "re ", "is?"]
```

### 3. 반복 검색

- `A{m,n}`은 `A`이 최소 m번, 최대 n번 반복되는 문자열을 의미

```js
const target = 'A AA B BB Aa Bb AAA';

// 'A'가 최소 1번, 최대 2번 반복되는 문자열을 전역 검색한다.
const regExp = /A{1,2}/g;

target.match(regExp); // -> ["A", "AA", "A", "AA", "A"]
```

- `A{n}`은 `A`이 n번 반복되는 문자열을 의미

```js
const target = 'A AA B BB Aa Bb AAA';

// 'A'가 2번 반복되는 문자열을 전역 검색한다.
const regExp = /A{2}/g;

target.match(regExp); // -> ["AA", "AA"]
```

- `A{n,}`은 `A`이 n번 이상 반복되는 문자열을 의미

```js
const target = 'A AA B BB Aa Bb AAA';

// 'A'가 최소 2번 이상 반복되는 문자열을 전역 검색한다.
const regExp = /A{2,}/g;

target.match(regExp); // -> ["AA", "AAA"]
```

- `A+`은 `A`이 최소 한번 이상 반복되는 문자열을 의미 === `?{1,}`과 같음

```js
const target = 'A AA B BB Aa Bb AAA';

// 'A'가 최소 한 번 이상 반복되는 문자열('A, 'AA', 'AAA', ...)을 전역 검색한다.
const regExp = /A+/g;

target.match(regExp); // -> ["A", "AA", "A", "AAA"]
```

- `?`는 앞선 패턴이 최대 한 번(0번 포함) 이상 반복되는 문자열을 의미 === `?{0,1}`과 같음

```js
const target = 'color colour';

// 'colo' 다음 'u'가 최대 한 번(0번 포함) 이상 반복되고 'r'이 이어지는 문자열 'color', 'colour'를 전역 검색한다.
const regExp = /colou?r/g;

target.match(regExp); // -> ["color", "colour"]
```

### 4. OR 검색

- `|`

```js
const target = 'A AA B BB Aa Bb';

// 'A' 또는 'B'를 전역 검색한다.
const regExp = /A|B/g;

target.match(regExp); // -> ["A", "A", "A", "B", "B", "B", "A", "B"]
```

- 분해되지 않은 단어 레벨로 검색하려면 `+`를 함께 사용

```js
const target = 'A AA B BB Aa Bb';

// 'A' 또는 'B'가 한 번 이상 반복되는 문자열을 전역 검색한다.
// 'A', 'AA', 'AAA', ... 또는 'B', 'BB', 'BBB', ...
const regExp = /A+|B+/g;

target.match(regExp); // -> ["A", "AA", "B", "BB", "A", "B"]
```

- `[]`를 사용

```js
const target = 'A AA B BB Aa Bb';
// 'A' 또는 'B'가 한 번 이상 반복되는 문자열을 전역 검색한다.
// 'A', 'AA', 'AAA', ... 또는 'B', 'BB', 'BBB', ...
const regExp = /[AB]+/g;

target.match(regExp); // -> ["A", "AA", "B", "BB", "A", "B"]
```

- `[]` 내에 `-`를 사용해 범위 지정

```js
const target = 'A AA BB ZZ Aa Bb';

// 'A' ~ 'Z'가 한 번 이상 반복되는 문자열을 전역 검색한다.
// 'A', 'AA', 'AAA', ... 또는 'B', 'BB', 'BBB', ... ~ 또는 'Z', 'ZZ', 'ZZZ', ...
const regExp = /[A-Z]+/g;

target.match(regExp); // -> ["A", "AA", "BB", "ZZ", "A", "B"]
```

- 대소문자 구분하지 않고 알파벳 검색하기: `[A-Za-z]`
- 숫자 검색: `[0-9]`, `[\d]`
- 숫자 제외 검색: `[\D]`
- 알파벳, 숫자, 언더 스코어: `[\w]` === `[A-Za-z0-9_]` <-> `[\W]`

```js
const target = 'A AA BB ZZ Aa Bb';

// 'A' ~ 'Z'가 한 번 이상 반복되는 문자열을 전역 검색한다.
// 'A', 'AA', 'AAA', ... 또는 'B', 'BB', 'BBB', ... ~ 또는 'Z', 'ZZ', 'ZZZ', ...
const regExp = /[A-Z]+/g;

target.match(regExp); // -> ["A", "AA", "BB", "ZZ", "A", "B"]

const target = 'AA BB Aa Bb 12';

// 'A' ~ 'Z' 또는 'a' ~ 'z'가 한 번 이상 반복되는 문자열을 전역 검색한다.
const regExp = /[A-Za-z]+/g;

target.match(regExp); // -> ["AA", "BB", "Aa", "Bb"]

const target = 'AA BB 12,345';

// '0' ~ '9'가 한 번 이상 반복되는 문자열을 전역 검색한다.
const regExp = /[0-9]+/g;

target.match(regExp); // -> ["12", "345"]

const target = 'AA BB 12,345';

// '0' ~ '9' 또는 ','가 한 번 이상 반복되는 문자열을 전역 검색한다.
const regExp = /[0-9,]+/g;

target.match(regExp); // -> ["12,345"]
```

## 31.6 자주 사용하는 정규표현식
