# 20. strict mode

```run-javascript
function foo() {
  x = 10
}
foo()

console.log(x) // 10
```

- 다음 코드는 **암묵적 전역**이 발생함
  1.  foo 함수의 스코프에서 x 변수 검색
  2.  foo 함수 컨텍스트의 상위 스코프(전역 스코프)에서 x 변수 선언 검색
  3.  전역 스코프에도 x 존재하지 않음 → _암묵적으로 전역 객체에 x 프로퍼티를 생성_
- ==`var`, `let`, `const` 키워드를 사용하여 변수를 사용하자==

→ 잠재적인 오류를 발생시키기 어려운 개발 환경을 지원하기 위해 ES5부터 **strict mode**가 추가되었다

- ES6에서 도입된 클래스와 모듈은 기본적으로 strict mode가 적용된다
- ESLint 같은 린트 도구를 사용해도 strict mode와 유사한 효과를 얻을 수 있다

## 20.2 strict mode의 적용

- 전역의 맨 위나 함수 몸체의 맨 위에 `'use strict'`를 추가한다

```javascript
'use strict';

function foo() {
  x = 10; // ReferenceError: x is not defined
}
foo();
```

```javascript
function foo() {
  'use strict';
  x = 10; // ReferenceError: x is not defined
}
foo();
```

```javascript
function foo() {
  x = 10; // 에러 발생시키지 않는다
  ('use strict');
}
foo();
```

## 20.3 전역에 strict mode를 적용하는 것은 피하자

- 전역에 적용한 strict mode는 스크립트 단위로 적용된다.
- 다른 스크립트에 영향을 주지 않는다.

```html
<!DOCTYPE html>
<html>
  <body>
    <script>
      'use strict';
    </script>
    <script>
      x = 1;
      console.log(x;)
    </script>
    <script>
      'use strict';

      y = 1; // ReferenceError: y is not defined
      console.log(y);
    </script>
  </body>
</html>
```

- strict mode/non-strict mode를 혼용하는 것은 오류를 발생시킬 수도 있으므로 즉시 실행 함수로 스크립트 전체를 감싸 스코프를 구분하고 즉시 실행 함수의 선두에 strict mode를 적용한다.

```javascript
(function () {
  'use strict';

  // ...
})();
```

## 20.4 함수 단위로 strict mode를 적용하는 것도 피하자

- 함수마다 strict mode / non-strict mode가 적용된 것은 바람직하지 않으며
- 모든 함수에 strict mode 적용하는 것 번거롭다
- strict mode 적용된 함수가 참조하는 외부 컨텍스트에 strict mode가 적용되어 있지 않다면 문제가 발생할 수 있다

```javascript
(function () {
  var let = 10;
  function foo() {
    'use strict';
    let = 20; // SyntaxError: Unexpected strict mode reserved word
  }
  foo();
})();
```

- ==strict mode는 즉시 실행 함수로 감싼 스크립트 단위로 적용하자==

## 20.5 strict mode가 발생시키는 에러

### 암묵적 전역

- 선언하지 않은 변수를 참조했을 때

```javascript
(function () {
  'use strict';

  x = 1;
  console.log(x); // ReferenceError: x is not defined
})();
```

### 변수, 함수, 매개변수의 삭제

- `delete` 연산자로 변수, 함수, 매개변수를 삭제했을 때 SyntaxError 발생

```javascript
(function () {
  'use strict';

  var x = 1;
  delete x;
  // SyntaxError: Delete of an unqualified identifier in strict mode.

  function foo(a) {
    delete a;
    // SyntaxError: Delete of an unqualified identifier in strict mode.
  }
  delete foo;
  // SyntaxError: Delete of an unqualified identifier in strict mode.
})();
```

### 매개변수 이름 중복

- 중복된 매개변수 이름을 사용할 때 SyntaxError 발생
  - 원래는 이런 코드가 정상 작동한다는 것이 더 신기..

```javascript
(function () {
  'use strict';

  //SyntaxError: Duplicate parameter name not allowed in this context
  function foo(x, x) {
    return x + x;
  }
  console.log(foo(1, 2));
})();
```

### with 문의 사용

- `with` 문을 사용하면 SyntaxError 발생

```javascript
(function () {
  'use strict';

  // SyntaxError: Strict mode code may not include a with statement
  with ({ x: 1 }) {
    console.log(x);
  }
})();
```

- ? `with` 문이란

  - 전달된 객체를 스코프 체인에 추가

  ```javascript
  with(반복될 객체){
      뒤에 오게될 메서드 또는 객체요소
  }


  // BEFORE
  x = Math.cos(3 * Math.PI) + Math.sin(Math.LN10)
  y = Math.tan(14 * Math.E)

  // AFTER
  with(Math){
     x = cos(3 * PI) + sin (LN10)
     y = tan(14 * E)
  }
  ```

  - 동일한 객체의 프로퍼티를 반복해서 사용할 때 객체 이름을 생략해서 작성할 수 있지만
  - 성능과 가독성이 나빠지는 문제가 있음

## 20.6 strict mode 적용에 의한 변화

### 일반 함수의 this

- strict mode에서 함수를 일반함수로서 호출하면 `this`에 `undefined`가 자동 바인딩
  - 생성자 함수가 아닌 일반 함수 내에서는 `this`를 사용할 필요가 없기 때문

```javascript
(function () {
  'use strict';

  function foo() {
    console.log(this);
  }
  foo(); // undefined
  new foo(); // foo

  function Foo() {
    console.log(this); // Foo
  }
  new Foo();
})();
```

### arguments 객체

- strict mod에서는 매개변수에 전달된 인수를 재할당하여 변경해도 arguments 객체에 반영되지 않음

```javascript
(function (a) {
  'use strict';
  // 매개변수에 전달된 인수를 재할당하여 변경
  a = 2;

  // 변경된 인수가 arguments 객체에 반영되지 않는다.
  console.log(arguments); // { 0: 1, length: 1 }
})(1);

// strict mode를 쓰지 않으면 { 0: 2, length: 1 } 로 변경됨
```
