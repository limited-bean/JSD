# 39. DOM

브라우저의 렌더링 엔진은 HTML 문서를 파싱하여 브라우저가 이해할 수 있는 자료구조인 DOM을 생성

- **DOM**: HTML 문서의 계층적 구조와 정보를 표현하며 이를 제어할 수 있는 API(= 프로퍼티와 메서드)를 제공하는 트리 자료구조

## 39.1 노드

### HTML 요소와 노드 객체

**HTML 요소**: HTML 문서를 구성하는 개별적인 요소

- `<div class="gretting">Hello</div>`
  - `<div>`: 시작 태그
  - `class`: 어트리뷰트(속성) 이름
  - `greeting`: 어트리뷰트(속성) 값
  - `Hello`: 콘텐츠
  - `</div>`: 종료 태그
- HTML 요소는 렌더링 엔진에 의해 **파싱**되어 **DOM**을 구성하는 **요소 노드 객체로 변환**
  - 어트리뷰트 → 어트리뷰트 노드로, HTML 요소의 텍스트 콘텐츠 → 텍스트 노드로 변환됨
- HTML 문서는 HTML 요소들의 집합으로 이루어짐
  - HTML 요소는 중첩 관계를 가짐
  - 즉, 시작 태그와 종료 태그 사이인 콘텐츠 영역은 텍스트뿐 아닌 다른 HTML 요소가 포함될 수도 있음
- HTML 요소 간 중첩 관계에 의해 계층적인 부자 관계가 형성됨
  → 요소 간 부자 관계 반영하여 HTML 요소를 객체화한 모든 노드 객체들을 트리 자료 구조로 구성함

#### 트리 자료구조

트리 자료구조는 **노드들의 계층 구조**로 이루어짐
= 부모 노드와 자식 노드로 구성되어 노드 간 계층적 구조를 표현하는 비선형 자료구조

트리 자료구조는 하나의 최상위 노드에서 시작함
**최상위 노드=루트 노드**는 부모 노드가 없음
루트 노드는 0개 이상의 자식 노드를 가짐
리프 노드(leaf node): 자식 노드가 없는 노드

![](https://i.imgur.com/bZYo7kv.png)

**DOM**: 노드 객체들로 구성된 트리 자료구조
노드 객체의 트리로 구조화되어 있기 때문에 DOM 트리라고 부르기도 함

### 노드 객체의 타입

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <link rel="stylesheet" href="style.css" />
  </head>
  <body>
    <ul>
      <li id="apple">Apple</li>
      <li id="banana">Banana</li>
      <li id="orange">Orange</li>
    </ul>
    <script src="app.js"></script>
  </body>
</html>
```

다음 HTML 문서는 렌더링 엔진에 의해 다음과 같이 DOM으로 생성된다.

![](https://i.imgur.com/RYZ2W9E.png)

> [!공백 텍스트 노드]
> 위 그림에서는 공백 텍스트 노드가 생략되어 있다.
> HTML 요소 사이의 개행이나 공백이 텍스트 노드가 된다.

노드 객체에는 종류가 있고, 상속 구조를 가짐
노드 객체는 12개의 종류(노드 타입)가 있다.

노드 타입 참고: [Node: nodeType property - Web APIs | MDN](https://developer.mozilla.org/en-US/docs/Web/API/Node/nodeType)

#### 문서 노드

- DOM 트리 최상위 루트 노드 = document 객체
- 브라우저가 렌더링한 HTML 문서 전체를 가리키는 객체
- window.document / document로 참조 가능
  - document 객체는 HTML 문서당 하나
- 요소, 어트리뷰트, 텍스트 노드 접근하기 위해서는 문서 노드를 통해야 함

#### 요소 노드

- HTML 요소를 가리키는 객체
- HTML 요소 간 중첩에 의해 부자 관계 형성, 정보를 구조화
- 문서의 구조를 표현

#### 어트리뷰트 노드

- HTML 요소의 어트리뷰트를 가리키는 객체
- 어트리뷰트가 지정된 HTML 요소의 요소 노드와 연결되어 있음
- 어트리뷰트 노드는 부모 노드와 연결되어 있지 않고 요소 노드에만 연결
  - 요소 노드에 먼저 접근해서 접근해야 함

#### 텍스트 노드

- HTML 요소의 텍스트를 가리키는 객체
- 요소 노드의 자식 노드, 자식을 가질 수 없는 리프 노드
- DOM 트리의 최종단

### 노드 객체의 상속 구조

- DOM을 구성하는 노드 객체는 자신의 구조와 정보를 제어할 수 있는 **DOM API**를 사용할 수 있다.
- 이를 통해 노드 객체는 자신의 부모, 형제, 자식을 탐색할 수 있으며, 자신의 어트리뷰트, 텍스트를 조작할 수 있다.

- DOM을 구성하는 노드 객체는 표준 빌트인 객체가 아닌 **브라우저 환경에서 추가적으로 제공하는 호스트 객체**
- 그렇지만 노드 객체도 프로토타입에 의한 상속 구조를 가짐
  ![](https://i.imgur.com/cmyn0GK.png)

- 모든 노드 객체는 Object, EventTarget, Node 인터페이스를 상속받음
- 요소 노드는 Element 인터페이스를 상속받음
  - HTMLElement와 태그의 종류별로 세분화된 HTMLHtmlElement, HTMLHeadElement, HTMLBodyElement, HTMLUListElement 등의 인터페이스를 상속받음
- 프로토타입 체인 관점에서 살펴보면 **프로토타입 체인에 있는 모든 프로토타입의 프로퍼티/메서드를 상속받아 사용 가능**
  - input 요소 노드 객체는 HTMLInputElement, HTMLElement, Element, Node, EventTarget, Object의 프로토타입에 바인딩되어 있는 프로토타입 객체 상속받을 것이므로!

![](https://i.imgur.com/AcAyJAJ.png)

```html
<!DOCTYPE html>
<html>
  <body>
    <input type="text" />
    <script>
      // input 요소 노드 객체를 선택
      const $input = document.querySelector('input');

      // input 요소 노드 객체의 프로토타입 체인
      console.log(
        Object.getPrototypeOf($input) === HTMLInputElement.prototype,
        Object.getPrototypeOf(HTMLInputElement.prototype) ===
          HTMLElement.prototype,
        Object.getPrototypeOf(HTMLElement.prototype) === Element.prototype,
        Object.getPrototypeOf(Element.prototype) === Node.prototype,
        Object.getPrototypeOf(Node.prototype) === EventTarget.prototype,
        Object.getPrototypeOf(EventTarget.prototype) === Object.prototype
      ); // 모두 true
    </script>
  </body>
</html>
```

- 노드 객체의 상속 구조는 개발자 도구 Elements > Properties 에서도 확인 가능

  - ? 어디서 볼 수 있다는 거지... 못 찾겠음

- 모든 노드 객체가 공통으로 가지는 기능도 있고 노드 타입에 따라 고유한 기능도 있음
  - 모든 노드 객체는 공통적으로 이벤트를 발생시킬 수 있음 (EventTarget.addEventListener, EventTarget.removeEventListener)
  - 모든 노드 객체는 공통적으로 트리 탐색 기능(Node.parentNode, Node.childNodes, Node.previousSibling, Node.nextSibling)
  - 모든 노드 객체는 노드 정보 제공 기능(Node.nodeType, Node.nodeName)
- HTML 요소가 객체화된 요소 노드 객체는 HTML 요소가 가지는 공통적인 기능이 있음
  - input, div 노드 객체는 HTML 요소의 스타일을 나타내는 style 프로퍼티 (HTMLElement 인터페이스가 제공)
- value 프로퍼티: input은 필요, div는 필요하지 않음 - HTMLInputElement, HTMLDivElement 등 요소의 종류에 따라 필요 기능 제공하는 인터페이스가 각각 다름
  → **공통된 기능일수록 프로토타입 체인 상위에, 개별적인 고유 기능일 수록 프로토타입 체인 하위에 프로토타입 체인을 구축해 프로퍼티/메서드 제공하는 상속 구조 가짐**

- DOM은
  - HTML 문서의 계층적 구조와 정보를 표현,
  - 노드 타입에 따라 필요한 기능을 프로퍼티와 메서드의 집합인 DOM API로 제공
- DOM API를 통해 HTML의 구조나 내용 또는 스타일 등을 동적 조작 가능

## 39.2 요소 노드 취득

- HTML의 구조/내용/스타일을 동적 조작하려면 요소 노드를 먼저 취득해야 함

### id를 통한 요소 노드 취득

- `Document.prototype.getElementById`
- id 어트리뷰트 값을 갖는 하나의 요소 노드를 탐색하여 반환
- 반드시 document를 통해 호출해야 함

- id 값은 HTML 문서 내에서 유일한 값
- 동일한 id가 여러 개 존재하더라도 에러가 발생하지는 않는다.
- HTML 문서 내에 중복된 Id 값이 있더라도 getElementById는 해당 id 값을 가지는 첫 번째 요소 노드만 반환
- getElementById는 언제나 단 하나의 요소 노드를 반환
- id 값을 가지는 HTML 요소가 없을 경우 null을 반환

```html
<!DOCTYPE html>
<html>
  <body>
    <ul>
      <li id="banana">Apple</li>
      <li id="banana">Banana</li>
      <li id="banana">Orange</li>
    </ul>
    <script>
      // getElementById 메서드는 언제나 단 하나의 요소 노드를 반환한다.
      // 첫 번째 li 요소가 파싱되어 생성된 요소 노드가 반환된다.
      const $elem = document.getElementById('banana');

      // 취득한 요소 노드의 style.color 프로퍼티 값을 변경한다.
      $elem.style.color = 'red';
    </script>
  </body>
</html>
```

```html
<!DOCTYPE html>
<html>
  <body>
    <ul>
      <li id="apple">Apple</li>
      <li id="banana">Banana</li>
      <li id="orange">Orange</li>
    </ul>
    <script>
      // id 값이 'grape'인 요소 노드를 탐색하여 반환한다. null이 반환된다.
      const $elem = document.getElementById('grape');

      // 취득한 요소 노드의 style.color 프로퍼티 값을 변경한다.
      $elem.style.color = 'red';
      // -> TypeError: Cannot read property 'style' of null
    </script>
  </body>
</html>
```

- HTML 요소에 id 어트리뷰트를 부여하면 id 값과 동일한 이름의 전역 변수가 암묵적으로 선언되고 해당 노드 객체가 할당됨

```html
<!DOCTYPE html>
<html>
  <body>
    <div id="foo"></div>
    <script>
      // id 값과 동일한 이름의 전역 변수가 암묵적으로 선언되고 해당 노드 객체가 할당된다.
      console.log(foo === document.getElementById('foo')); // true

      // 암묵적 전역으로 생성된 전역 프로퍼티는 삭제되지만 전역 변수는 삭제되지 않는다.
      delete foo;
      console.log(foo); // <div id="foo"></div>
    </script>
  </body>
</html>
```

- id 값과 동일한 이름의 전역 변수가 이미 선언되어 있을 경우에는 노드 객체가 재할당되지 않음

```html
<!DOCTYPE html>
<html>
  <body>
    <div id="foo"></div>
    <script>
      let foo = 1;

      // id 값과 동일한 이름의 전역 변수가 이미 선언되어 있으면 노드 객체가 재할당되지 않는다.
      console.log(foo); // 1
    </script>
  </body>
</html>
```

### 태그 이름을 통한 요소 노드 취득

- `getElementByTagName` 메서드는 인수로 전달한 태그 이름을 가지는 모든 요소 노드들을 탐색하여 반환
- 반환 값은 여러 개의 요소 노드 객체를 가지는 유사 배열 객체이며 이터러블인 HTMLCollection 객체

- `getElementsByTagName('*')` 으로 HTML 문서의 모든 요소 노드를 취득할 수 있음

- Document.prototype, Element.prototype에서 정의된 메서드가 각각 있어서 특정 요소 내의 요소 노드를 탐색하여 반환할 수 있음

- 인수로 전달된 태그 이름을 가지는 요소가 존재하지 않을 경우 빈 HTMLCollection 객체를 반환함

### class를 이용한 요소 노드 취득

- `getElementsByClassName`
- 인수로 전달한 class 어트리뷰트 값을 갖는 모든 요소 노드들을 탐색하여 반환
- 인수로 전달하는 class는 공백으로 구분해 여러 개의 클래스를 지정할 수 있음
- 반환 값은 HTMLCollection 객체
- Document.prototype, Element.prototype에서 정의된 메서드가 각각 있어서 특정 요소 내의 요소 노드를 탐색하여 반환할 수 있음

```html
<!DOCTYPE html>
<html>
  <body>
    <ul>
      <li class="fruit apple">Apple</li>
      <li class="fruit banana">Banana</li>
      <li class="fruit orange">Orange</li>
    </ul>
    <script>
      // class 값이 'fruit'인 요소 노드를 모두 탐색하여 HTMLCollection 객체에 담아 반환한다.
      const $elems = document.getElementsByClassName('fruit');

      // 취득한 모든 요소의 CSS color 프로퍼티 값을 변경한다.
      [...$elems].forEach((elem) => {
        elem.style.color = 'red';
      });

      // class 값이 'fruit apple'인 요소 노드를 모두 탐색하여 HTMLCollection 객체에 담아 반환한다.
      const $apples = document.getElementsByClassName('fruit apple');

      // 취득한 모든 요소 노드의 style.color 프로퍼티 값을 변경한다.
      [...$apples].forEach((elem) => {
        elem.style.color = 'blue';
      });
    </script>
  </body>
</html>
```

- 인수로 전달된 클래스 값을 가지는 요소가 존재하지 않을 경우 빈 HTMLCollection 객체를 반환

### CSS 선택자를 이용한 요소 노드 취득

- `querySelector`
- 인수로 전달한 CSS 선택자를 만족시키는 요소 노드가 여러 개인 경우 첫 번째 요소 노드만 반환
- 인수로 전달한 CSS 선택자를 만족시키는 요소 노드가 존재하지 않는 경우 Null을 반환
- 인수로 전달한 CSS 선택자가 문법에 맞지 않는 경우 DOMException 에러 발생

- `querySelectorAll`
- 인수로 전달한 CSS 선택자를 만족시키는 모든 요소 노드를 탐색하여 반환
- 반환 값은 유사 배열 객체이면서 이터러블인 NodeList
- 인수로 전달한 CSS 선택자를 만족시키는 요소 노드가 존재하지 않는 경우 빈 NodeList 객체를 반환
- 인수로 전달한 CSS 선택자가 문법에 맞지 않는 경우 DOMException 에러 발생

- `querySelector`, `querySelectorAll` 는 Document.prototype, Element.prototype에서 정의된 메서드가 각각 있어서 특정 요소 내의 요소 노드를 탐색하여 반환할 수 있음

- `querySelector`, `querySelectorAll` 는 `getElementById`, `getElementBy***` 메서드보다 다소 느리지만,
- CSS 선택자 문법을 사용해 구체적인 조건으로 요소 노드를 취득할 수 있고 일관된 방식으로 요소 노드를 취득할 수 있다는 장점이 있음

- id 어트리뷰트가 있는 경우 `getElementById` 메서드를 사용하고 그 외의 경우 `querySelector`, `querySelectorAll` 메서드 사용하는 것을 권장

### 특정 요소 노드를 취득할 수 있는지 확인

- `Element.prototype.matches`
- 인수로 전달한 CSS 선택자를 통해 특정 요소 노드를 취득할 수 있는지 확인
- 이벤트 위임 사용할 때 유용하게 사용됨

```html
<!DOCTYPE html>
<html>
  <body>
    <ul id="fruits">
      <li class="apple">Apple</li>
      <li class="banana">Banana</li>
      <li class="orange">Orange</li>
    </ul>
  </body>
  <script>
    const $apple = document.querySelector('.apple');

    // $apple 노드는 '#fruits > li.apple'로 취득할 수 있다.
    console.log($apple.matches('#fruits > li.apple')); // true

    // $apple 노드는 '#fruits > li.banana'로 취득할 수 없다.
    console.log($apple.matches('#fruits > li.banana')); // false
  </script>
</html>
```

### HTMLCollection과 NodeList

- `HTMLCollection`, `NodeList`는 DOM API가 여러 개의 결과값을 반환하기 위한 I 컬렉션 객체
- 모두 유사 배열 객체면서 이터러블
  - for 문으로 순회 가능, 스프레드 문법으로 배열 변환 가능
- 노드 객체의 상태 변화를 실시간 반영하는 **살아 있는 객체** = live 객체
  - HTMLCollection은 언제나 Live 객체로 동작
  - NodeList는 대부분 non-live, 경우에 따라 live 객체로 동작

#### HTMLCollection

- `getElementByTagName`, `getElementByClassName` 메서드가 반환하는 HTMLCollection 객체는 상태 변화가 실시간으로 반영되는 live 객체

```html
<!DOCTYPE html>
<head>
  <style>
    .red {
      color: red;
    }
    .blue {
      color: blue;
    }
  </style>
</head>
<html>
  <body>
    <ul id="fruits">
      <li class="red">Apple</li>
      <li class="red">Banana</li>
      <li class="red">Orange</li>
    </ul>
    <script>
      // class 값이 'red'인 요소 노드를 모두 탐색하여 HTMLCollection 객체에 담아 반환한다.
      const $elems = document.getElementsByClassName('red');
      // 이 시점에 HTMLCollection 객체에는 3개의 요소 노드가 담겨 있다.
      console.log($elems); // HTMLCollection(3) [li.red, li.red, li.red]

      // HTMLCollection 객체의 모든 요소의 class 값을 'blue'로 변경한다.
      for (let i = 0; i < $elems.length; i++) {
        $elems[i].className = 'blue';
      }

      // HTMLCollection 객체의 요소가 3개에서 1개로 변경되었다.
      console.log($elems); // HTMLCollection(1) [li.red]
    </script>
  </body>
</html>
```

- 해당 예제는 첫 번째, 세 번째 li 태그만 blue로 변경되는데 이유는?

  - HTMLCollection 객체가 Live 객체이기 때문에 실시간으로 $elem.length가 변경되었기 때문

- 해결 방법은?
  - for문을 역방향으로 순회하거나
  - while 문으로 노드 객체가 남아 있지 않을 때까지 반복하는 방법
  - HTMLCollection 객체를 스프레드 문법으로 배열로 변환하여 순회

#### NodeList

- HTMLCollection 객체의 부작용을 해결하기 위해 `getElementsByTagName`, `getElementsByClassName` 메서드 대신 `querySelectorAll` 메서드를 사용할 수도 있음
- `querySelectorAll`메서드를 사용할 때 반환되는 NodeList 객체는 non-live 객체이기 때문

- 다만, **childNodes 프로퍼티가 반환하는 NodeList 객체는 HTMLCollection 객체와 같이 Live 객체로 동작하므로 주의 필요**

```html
<!DOCTYPE html>
<html>
  <body>
    <ul id="fruits">
      <li>Apple</li>
      <li>Banana</li>
    </ul>
  </body>
  <script>
    const $fruits = document.getElementById('fruits');

    // childNodes 프로퍼티는 NodeList 객체(live)를 반환한다.
    const { childNodes } = $fruits;
    console.log(childNodes instanceof NodeList); // true

    // $fruits 요소의 자식 노드는 공백 텍스트 노드(39.3.1절 "공백 텍스트 노드" 참고)를 포함해 모두 5개다.
    console.log(childNodes); // NodeList(5) [text, li, text, li, text]

    for (let i = 0; i < childNodes.length; i++) {
      // removeChild 메서드는 $fruits 요소의 자식 노드를 DOM에서 삭제한다.
      // (39.6.9절 "노드 삭제" 참고)
      // removeChild 메서드가 호출될 때마다 NodeList 객체인 childNodes가 실시간으로 변경된다.
      // 따라서 첫 번째, 세 번째 다섯 번째 요소만 삭제된다.
      $fruits.removeChild(childNodes[i]);
    }

    // 예상과 다르게 $fruits 요소의 모든 자식 노드가 삭제되지 않는다.
    console.log(childNodes); // NodeList(2) [li, li]
  </script>
</html>
```

- 안전하게 DOM 컬렉션을 사용하기 위해서는 HTMLCollection, NodeList 객체를 배열로 변환하여 사용하는 것을 권장

## 39.3 노드 탐색

- 요소 노드 취득한 후 취득 요소 노드를 기점으로 DOM 트리의 노드를 옮겨 다니며 부모,형제,자식 노드 등을 탐색해야 할 때가 있음

```html
<ul id="fruits">
  <li class="apple">Apple</li>
  <li class="banana">Banana</li>
  <li class="orange">Orange</li>
</ul>
```

- `ul#fruits` 요소는 자식 요소 3개를 가짐
- `ul#fruits` 요소 노드를 취득한 다음 자식 노드 모두 또는 자식 노드 중 하나를 탐색할 수 있음
- `li.banana` 요소도 마찬가지로 먼저 취득한 다음, 형제 노드나 부모 노드를 탐색할 수 있음

- Node, Element 인터페이스는 트리 탐색 프로퍼티를 제공함

![](https://i.imgur.com/HfqC8FQ.png)

- 노드 탐색 프로퍼티는 getter만 존재하는 읽기 전용 접근자 프로퍼티이다

### 공백 텍스트 노드

- HTML 요소 사이 스페이스, 탭, 줄바꿈 등 공백 문자는 텍스트 노드를 생성함

![](https://i.imgur.com/0SQZgUQ.png)

- HTML 문서가 파싱되어 다음과 같은 DOM을 생성
- 노드를 탐색할 때 공백 문자가 생성한 공백 텍스트 노드를 주의해야 함
- 인위적으로 HTML 문서의 공백 문자 제거할 수 있지만 가독성이 좋지 않으므로 권장하지 않음

### 자식 노드 탐색

- `Node.prototype.childNodes`: 자식 노드 모두 탐색하여 NodeList으로 반환, 요소 노드뿐 아니라 텍스트 노드도 포함되어 있을 수 있음
- `Element.prototype.children`: 자식 노드 중 요소 노드만 탐색하여 HTMLCollection에 담아 반환, 텍스트 노드 포함되지 않음
- `Node.prototype.firstChild`: 첫 번째 자식 노드 반환, 텍스트 노드이거나 요소 노드
- `Node.prototype.lastChild`: 마지막 자식 노드를 반환, 텍스트 노드이거나 요소 노드
- `Element.prototype.firstElementChild`: 첫 번째 자식 요소 노드 반환, 요소 노드만 반환
- `Element.prototype.lastElementChild`: 마지막 자식 요소 노드 반환, 요소 노드만 반환

### 자식 노드 존재 확인

- `Node.prototype.hasChildNodes` 메서드
- 자식노드가 존재하면 true, 존재하지 않으면 false 반환
- 텍스트 노드를 포함하여 자식 노드의 존재를 확인함

- 텍스트 노드가 아닌 요소 노드가 존재하는지 확인하려면 `children.length`, `Element.prototype.childElementCount` 프로퍼티 사용

```html
<!DOCTYPE html>
<html>
  <body>
    <ul id="fruits"></ul>
  </body>
  <script>
    // 노드 탐색의 기점이 되는 #fruits 요소 노드를 취득한다.
    const $fruits = document.getElementById('fruits');

    // hasChildNodes 메서드는 텍스트 노드를 포함하여 자식 노드의 존재를 확인한다.
    console.log($fruits.hasChildNodes()); // true

    // 자식 노드 중에 텍스트 노드가 아닌 요소 노드가 존재하는지는 확인한다.
    console.log(!!$fruits.children.length); // 0 -> false
    // 자식 노드 중에 텍스트 노드가 아닌 요소 노드가 존재하는지는 확인한다.
    console.log(!!$fruits.childElementCount); // 0 -> false
  </script>
</html>
```

### 요소 노드의 텍스트 노드 탐색

- 요소 노드의 텍스트 노드는 요소 노드의 자식 노드
- `firstChild` 프로퍼티로 접근할 수 있음

### 부모 노드 탐색

- `Node.prototype.parentNode` 프로퍼티로 접근할 수 있음
- 텍스트 노드는 DOM 트리의 최종단 노드인 리프 노드이므로 부모 노드가 텍스트 노드인 경우는 없음

### 형제 노드 탐색

- `Node.prototype.previousSibling`: 부모 노드가 같은 형제 노드 중 자신의 이전 형제 노드를 탐색하여 반환, 텍스트 노드일 수 있음
- `Node.prototype.nextSibling`: 부모 노드가 같은 형제 노드 중 자신의 다음 형제 노드를 탐색하여 반환, 텍스트 노드일 수 있음
- `Element.prototype.previousElementSibling`: 부모 노드가 같은 형제 요소 노드 중 이전 형제 요소 노드 탐색하여 반환, 요소 노드만 반환
- `Element.prototype.nextElementSibling`: 부모 노드가 같은 형제 요소 노드 중 다음 형제 요소 노드 탐색하여 반환, 요소 노드만 반환

## 39.4 노드 정보 취득

- `Node.prototype.nodeType`: 노드 객체의 종류, 노드 타입을 나타내는 상수를 반환
  - Node.ELEMENT_NODE: 요소 노드 타입을 나타내는 상수 1을 반환
  - Node.TEXT_NODE: 텍스트 노드 타입을 나타내는 상수 3을 반환
  - Node.DOCUMENT_NODE: 요소 노드 타입을 나타내는 상수 9를 반환
- `Node.prototype.nodeName`: 노드의 이름을 문자열로 반환
  - 요소 노드: 대문자 문자열로 태그 이름 반환(UL, LI)
  - 텍스트 노드: 문자열 `#text` 반환
  - 문서 노드: 문자열 `#document` 반환

```html
<!DOCTYPE html>
<html>
  <body>
    <div id="foo">Hello</div>
  </body>
  <script>
    // 문서 노드의 노드 정보를 취득한다.
    console.log(document.nodeType); // 9
    console.log(document.nodeName); // #document

    // 요소 노드의 노드 정보를 취득한다.
    const $foo = document.getElementById('foo');
    console.log($foo.nodeType); // 1
    console.log($foo.nodeName); // DIV

    // 텍스트 노드의 노드 정보를 취득한다.
    const $textNode = $foo.firstChild;
    console.log($textNode.nodeType); // 3
    console.log($textNode.nodeName); // #text
  </script>
</html>
```

## 39.5 요소 노드의 텍스트 조작

### nodeValue

- `Node.prototype.nodeValue` 프로퍼티는 setter/getter 모두 존재하는 접근자 프로퍼티, 참조와 할당 모두 가능함
- 노드 객체의 값을 반환 = 텍스트 노드의 텍스트
- 텍스트 노드가 아닌 문서 노드나 요소 노드의 nodeValue 프로퍼티를 참조하면 null을 반환

```html
<!DOCTYPE html>
<html>
  <body>
    <div id="foo">Hello</div>
  </body>
  <script>
    // 문서 노드의 nodeValue 프로퍼티를 참조한다.
    console.log(document.nodeValue); // null

    // 요소 노드의 nodeValue 프로퍼티를 참조한다.
    const $foo = document.getElementById('foo');
    console.log($foo.nodeValue); // null

    // 텍스트 노드의 nodeValue 프로퍼티를 참조한다.
    const $textNode = $foo.firstChild;
    console.log($textNode.nodeValue); // Hello
  </script>
</html>
```

- 텍스트 노드의 nodeValue 프로퍼티를 참조할 때만 텍스트를 반환함
- nodeValue 프로퍼티에 값을 할당하면 텍스트를 변경할 수 있음

```html
<!DOCTYPE html>
<html>
  <body>
    <div id="foo">Hello</div>
  </body>
  <script>
    // 1. #foo 요소 노드의 자식 노드인 텍스트 노드를 취득한다.
    const $textNode = document.getElementById('foo').firstChild;

    // 2. nodeValue 프로퍼티를 사용하여 텍스트 노드의 값을 변경한다.
    $textNode.nodeValue = 'World';

    console.log($textNode.nodeValue); // World
  </script>
</html>
```

![](https://i.imgur.com/vGxQRkz.png)

### textContent

- `Node.prototype.textContent` 프로퍼티는 setter/getter 모두 존재하는 접근자 프로퍼티
- 요소 노드의 텍스트와 모든 자손 노드의 텍스트를 모두 취득/변경
- textContent 프로퍼티를 참조하면 요소 노드의 콘텐츠 영역(시작 태그~종료 태그) 내의 텍스트를 모두 반환
  - childNodes 프로퍼티가 반환한 모든 노드 들의 텍스트 노드의 값을 모두 반환, HTML 마크업은 무시됨

```html
<!DOCTYPE html>
<html>
  <body>
    <div id="foo">Hello <span>world!</span></div>
  </body>
  <script>
    // #foo 요소 노드의 텍스트를 모두 취득한다. 이때 HTML 마크업은 무시된다.
    console.log(document.getElementById('foo').textContent); // Hello world!
  </script>
</html>
```

- nodeValue 프로퍼티를 사용해서도 텍스트를 취득할 수 있으나 코드가 더 복잡

```html
<!DOCTYPE html>
<html>
  <body>
    <div id="foo">Hello <span>world!</span></div>
  </body>
  <script>
    // #foo 요소 노드는 텍스트 노드가 아니다.
    console.log(document.getElementById('foo').nodeValue); // null
    // #foo 요소 노드의 자식 노드인 텍스트 노드의 값을 취득한다.
    console.log(document.getElementById('foo').firstChild.nodeValue); // Hello
    // span 요소 노드의 자식 노드인 텍스트 노드의 값을 취득한다.
    console.log(document.getElementById('foo').lastChild.firstChild.nodeValue); // world!
  </script>
</html>
```

- 요소 노드의 콘텐츠 영역에 텍스트만 존재한다면 `firstChild.nodeValue`, `textContent` 프로퍼티는 같은 결과를 반환

```html
<!DOCTYPE html>
<html>
  <body>
    <!-- 요소 노드의 콘텐츠 영역에 다른 요소 노드가 없고 텍스트만 존재 -->
    <div id="foo">Hello</div>
  </body>
  <script>
    const $foo = document.getElementById('foo');

    // 요소 노드의 콘텐츠 영역에 자식 요소 노드가 없고 텍스트만 존재한다면
    // firstChild.nodeValue와 textContent는 같은 결과를 반환한다.
    console.log($foo.textContent === $foo.firstChild.nodeValue); // true
  </script>
</html>
```

- 요소 노드의 textContent 프로퍼티에 문자열을 할당하면 요소 노드의 모든 자식 노드가 제거되고 할당한 문자열이 텍스트로 추가됨
- 할당한 문자열에 포함되어 있는 HTML 마크업도 문자열 그대로 인식됨

- `textContent` 프로퍼티와 유사한 동작을 하는 `innerText` 프로퍼티는 다음과 같은 이유로 사용하지 않는 것이 좋음
  - `innerText` 프로퍼티는 CSS에 순종적이다. `innerText` 프로퍼티는 CSS에 의해 비표시된(visibility: hidden) 요소 노드의 텍스트를 반환하지 않음
  - `innerText` 프로퍼티는 CSS를 고려해야 하므로 textContent 프로퍼티보다 느림

## 39.6 DOM 조작

- 새로운 노드를 생성하여 DOM에 추가하거나 기존 노드를 삭제/교체하는 것
- 노드가 추가/삭제되면 리플로우와 리페인트가 발생하는 원인이 되므로 성능에 영향을 주기 때문에 주의해서 다루어야 함

### innerHTML

- `Element.prototype.innerHTML` 프로퍼티는 setter/getter 모두 존재하는 접근자 프로퍼티로서 요소 노드의 HTML 마크업을 취득/변경
- 요소 노드의 콘텐츠 영역(시작 태그~종료 태그) 내에 포함된 모든 HTML 마크업을 문자열로 반환함

textContent vs. innerHTML

- textContent 프로퍼티: HTML 마크업 무시하고 텍스트만 반환
- innerHTML 프로퍼티: HTML 마크업이 포함된 문자열을 그대로 반환

![](https://i.imgur.com/91D1E7I.png)

- 요소 노드의 innerHTML 프로퍼티에 문자열을 할당하면 요소 노드의 모든 자식 노드 제거되고 할당한 문자열에 포함되어 있는 HTML 마크업이 파싱되어 요소 노드의 자식 노드로 DOM에 반영됨

```html
<!DOCTYPE html>
<html>
  <body>
    <div id="foo">Hello <span>world!</span></div>
  </body>
  <script>
    // HTML 마크업이 파싱되어 요소 노드의 자식 노드로 DOM에 반영된다.
    document.getElementById('foo').innerHTML = 'Hi <span>there!</span>';
  </script>
</html>
```

- 요소 노드의 innerHTML 프로퍼티에 할당한 HTML 마크업 문자열은 렌더링 엔진에 의해 파싱되어 요소 노드의 자식으로 DOM에 반영됨
- 사용자로부터 입력받은 데이터를 그대로 innerHTML 프로퍼티에 할당하는 것은 크로스 사이트 스크립팅 공격 (XSS) 에 취약하므로 위험함
  - HTML 마크업 내에 자바스크립트 악성 코드가 포함되어 있다면 파싱 과정에서 그대로 실행될 가능성이 있기 때문

```html
<!DOCTYPE html>
<html>
  <body>
    <div id="foo">Hello</div>
  </body>
  <script>
    // innerHTML 프로퍼티로 스크립트 태그를 삽입하여 자바스크립트가 실행되도록 한다.
    // HTML5는 innerHTML 프로퍼티로 삽입된 script 요소 내의 자바스크립트 코드를 실행하지 않는다.
    document.getElementById('foo').innerHTML
      = '<script>alert(document.cookie)</script>';
  </script>
</html>
```

- HTML5는 innerHTML 프로퍼티로 삽입된 script 요소 내의 자바스크립트 코드를 실행하지 않음
- 하지만 script 요소 없이도 XSS 공격 가능

```html
<!DOCTYPE html>
<html>
  <body>
    <div id="foo">Hello</div>
  </body>
  <script>
    // 에러 이벤트를 강제로 발생시켜서 자바스크립트 코드가 실행되도록 한다.
    document.getElementById(
      'foo'
    ).innerHTML = `<img src="x" onerror="alert(document.cookie)">`;
  </script>
</html>
```

- innerHTML 프로퍼티를 사용한 DOM 조작은 구현이 간단하고 직관적이라는 장점이 있지만 XSS 공격에 취약하다는 단점이 있음

> [! HTML 새니티제이션]
> 사용자로부터 입력받은 데이터에 의해 발생할 수 있는 XSS 공격을 예방하기 위해 잠재적 위험을 제거하는 기능을 말함
> 새니티제이션 함수를 직접 구현할 수도 있지만 DOMPurify 라이브러리를 사용하는 것을 권장함
> DOMPurify는 다음과 같이 잠재적 위험을 내포한 HTML 마크업을 새니티제이션하여 잠재적 위험을 제거함
>
> ```
> DOMPurify.sanitize('<img src="x" onerror="alert(document.cookie)">')
> // => <img src="x">
> ```

- innerHTML의 또 다른 단점은 요소 노드의 innerHTML 프로퍼티에 HTML 마크업 문자열 할당하는 경우 요소 노드의 모든 자식 노드를 제거하고 할당한 HTML 마크업 문자열을 파싱하여 DOM을 변경한다는 것

```html
<!DOCTYPE html>
<html>
  <body>
    <ul id="fruits">
      <li class="apple">Apple</li>
    </ul>
  </body>
  <script>
    const $fruits = document.getElementById('fruits');

    // 노드 추가
    $fruits.innerHTML += '<li class="banana">Banana</li>';
  </script>
</html>
```

- 위 예제는 `#fruits` 요소에 자식 요소 `li.banana`를 추가하는 것인데 `li.apple`까지 제거되었다가 새롭게 `li.apple`, `li.banana`를 생성하여 `#fruits` 자식 요소로 추가하는 것
- 유지되어도 좋은 기존의 자식 노드까지 모두 제거하고 처음부터 새롭게 자식 노드를 생성하여 DOM에 반영하므로 비효율적임

- 새로운 요소를 삽입할 때 삽입될 위치를 지정할 수 없다는 단점도 있음

### insertAdjacentHTML 메서드

- `Element.prototype.insertAdjacentHTML` 메서드는 기존 요소를 제거하지 않으면서 위치를 지정해 새로운 요소를 삽입함
- 두 번째 인수로 전달한 HTML 마크업 문자열을 파싱하고, 그 결과로 생성된 노드를 첫 번째 인수로 전달한 위치에 삽입하여 DOM에 반영함
- 첫 번째 인수로 전달할 수 있는 문자열은 'beforebegin', 'afterbegin', 'beforeend', 'afterend' 4 가지

![](https://i.imgur.com/Ku5PyOn.png)

- 기존 요소에 영향을 주지 않고 새롭게 삽입될 요소만을 파싱해 자식 요소로 추가하므로 innerHTML 프로퍼티보다 효율적이고 빠름
- XSS 공격에 취약하다는 점은 동일

### 노드 생성과 추가

```html
<!DOCTYPE html>
<html>
  <body>
    <ul id="fruits">
      <li>Apple</li>
    </ul>
  </body>
  <script>
    const $fruits = document.getElementById('fruits');

    // 1. 요소 노드 생성
    const $li = document.createElement('li');

    // 2. 텍스트 노드 생성
    const textNode = document.createTextNode('Banana');

    // 3. 텍스트 노드를 $li 요소 노드의 자식 노드로 추가
    $li.appendChild(textNode);

    // 4. $li 요소 노드를 #fruits 요소 노드의 마지막 자식 노드로 추가
    $fruits.appendChild($li);
  </script>
</html>
```

#### 요소 노드 생성

- `document.createElement` 메서드는 요소 노드를 생성하여 반환함
- 매개변수에는 태그 이름을 나타내는 문자열을 인수로 전달

```js
const $li = document.createElement('li');
```

- `createElement` 메서드로 생성한 요소 노드는 기존 DOM에 추가되지 않고 홀로 존재하는 상태

![](https://i.imgur.com/YRtLshe.png)

- 따라서 이후에 생성된 요소 노드를 DOM에 추가하는 처리를 해야 함
- `createElement` 메서드로 생성한 요소 노드는 아무런 자식 노드, 텍스트 노드를 가지고 있지 않음

#### 텍스트 노드 생성

- `document.createTextNode` 메서드는 텍스트 노드를 생성하여 반환함
- 매개변수에 텍스트 노드의 값으로 사용할 문자열을 인수로 전달하여 사용

```js
const textNode = document.createTextNode('Banana');
```

- 텍스트 노드를 별도 생성, 요소 노드의 자식 노드로 추가되지 않고 홀로 존재하는 상태
- 생성된 텍스트 노드를 요소 노드에 추가하는 처리가 별도로 필요

#### 텍스트 노드를 요소 노드의 자식 노드로 추가

- `Node.prototype.appendChild` 메서드는 매개변수로 전달한 노드를 appendChild 메서드를 호출한 노드의 마지막 자식노드로 추가함

```js
$li.appendChild(textNode);
```

- 그러면 요소 노드와 텍스트 노드는 부자 관계로 연결되었지만 DOM에 추가되지는 않은 상태임
- 요소 노드에 자식 노드가 하나도 없는 경우에는 텍스트 노드 생성하고 요소 노드의 자식으로 추가하는 것보다 **textContent 프로퍼티를 사용하는 것이 간편함**
- 단, 요소 노드에 자식 노드가 있는 경우에는 모든 자식 노드가 제거되고 할당한 문자열이 텍스트로 추가되므로 주의

#### 요소 노드를 DOM에 추가

- `Node.prototype.appendChild` 메서드로 텍스트 노드와 부자 관계로 연결한 요소 노드를 `#fruits` 요소 노드의 마지막 자식 요소로 추가

```js
$fruits.appendChild($li);
```

- 기존의 DOM에 요소 노드를 추가하는 처리는 이 과정뿐
- DOM이 변경될 때마다 리플로우, 리페인트가 실행됨

### 복수의 노드 생성과 추가

```html
<!DOCTYPE html>
<html>
  <body>
    <ul id="fruits"></ul>
  </body>
  <script>
    const $fruits = document.getElementById('fruits');

    ['Apple', 'Banana', 'Orange'].forEach((text) => {
      // 1. 요소 노드 생성
      const $li = document.createElement('li');

      // 2. 텍스트 노드 생성
      const textNode = document.createTextNode(text);

      // 3. 텍스트 노드를 $li 요소 노드의 자식 노드로 추가
      $li.appendChild(textNode);

      // 4. $li 요소 노드를 #fruits 요소 노드의 마지막 자식 노드로 추가
      $fruits.appendChild($li);
    });
  </script>
</html>
```

- 3개의 요소 노드를 생성하여 DOM에 3번 추가하므로 DOM이 3번 변경되고 리플로우/리페인트도 3번 실행됨
- DOM을 여러 번 변경하는 문제를 피하기 위해 컨테이너 요소 사용하기
  - 컨테이너 요소를 미리 생성한 후, DOM에 추가할 3개의 요소 노드를 컨테이너 요소에 자식 노드로 추가하고, 컨테이너 요소를 `#fruits` 요소에 자식으로 추가하여 DOM 변경을 1번으로 줄일 수 있음

```html
<!DOCTYPE html>
<html>
  <body>
    <ul id="fruits"></ul>
  </body>
  <script>
    const $fruits = document.getElementById('fruits');

    // 컨테이너 요소 노드 생성
    const $container = document.createElement('div');

    ['Apple', 'Banana', 'Orange'].forEach((text) => {
      // 1. 요소 노드 생성
      const $li = document.createElement('li');

      // 2. 텍스트 노드 생성
      const textNode = document.createTextNode(text);

      // 3. 텍스트 노드를 $li 요소 노드의 자식 노드로 추가
      $li.appendChild(textNode);

      // 4. $li 요소 노드를 컨테이너 요소의 마지막 자식 노드로 추가
      $container.appendChild($li);
    });

    // 5. 컨테이너 요소 노드를 #fruits 요소 노드의 마지막 자식 노드로 추가
    $fruits.appendChild($container);
  </script>
</html>
```

- 그러나 불필요한 컨테이너 (div)가 DOM에 추가됨
- `DocumentFragment` 노드를 통해 해결할 수 있음
  - 문서, 요소, 어트리뷰트, 텍스트 노드와 같은 노드 객체의 일종으로 부모 노드가 없어 기존 DOM과 별도로 존재한다는 특징 있음
  - 자식 노드들의 부모 노드로서 별도의 서브 DOM을 구성해 기존 DOM에 추가하기 위한 용도로 사용됨
  - `DocumentFragment` 노드를 DOM에 추가하면 자신은 제거되고 자신의 자식 노드만 DOM에 추가됨
  - `Document.prototype.createDocumentFragment` 메서드로 비어 있는 `DocumentFragment` 노드 생성 가능

```Html
<!DOCTYPE html>
<html>
  <body>
    <ul id="fruits"></ul>
  </body>
  <script>
    const $fruits = document.getElementById('fruits');

    // DocumentFragment 노드 생성
    const $fragment = document.createDocumentFragment();

    ['Apple', 'Banana', 'Orange'].forEach(text => {
      // 1. 요소 노드 생성
      const $li = document.createElement('li');

      // 2. 텍스트 노드 생성
      const textNode = document.createTextNode(text);

      // 3. 텍스트 노드를 $li 요소 노드의 자식 노드로 추가
      $li.appendChild(textNode);

      // 4. $li 요소 노드를 DocumentFragment 노드의 마지막 자식 노드로 추가
      $fragment.appendChild($li);
    });

    // 5. DocumentFragment 노드를 #fruits 요소 노드의 마지막 자식 노드로 추가
    $fruits.appendChild($fragment);
  </script>
</html>
```

### 노드 삽입

#### 마지막 노드로 추가

- `Node.prototype.appendChild`
  - 인수로 전달받은 노드를 자신을 호출한 노드의 마지막 자식 노드로 DOM에 추가
  - 추가될 위치 지정 불가, 언제나 마지막 자식 노드로 추가함

#### 지정한 위치에 노드 삽입

- `Node.prototype.insertBefore`
  - 첫 번째 인수로 전달받은 노드를
  - 두 번째 인수로 전달받은 노드 앞에 삽입

```html
<!DOCTYPE html>
<html>
  <body>
    <ul id="fruits">
      <li>Apple</li>
      <li>Banana</li>
    </ul>
  </body>
  <script>
    const $fruits = document.getElementById('fruits');

    // 요소 노드 생성
    const $li = document.createElement('li');

    // 텍스트 노드를 $li 요소 노드의 마지막 자식 노드로 추가
    $li.appendChild(document.createTextNode('Orange'));

    // $li 요소 노드를 #fruits 요소 노드의 마지막 자식 요소 앞에 삽입
    $fruits.insertBefore($li, $fruits.lastElementChild);
    // Apple - Orange - Banana
  </script>
</html>
```

- 두 번째 인수로 전달받은 노드는 반드시 insertBefore 메서드를 호출한 노드의 자식노드여야 함
- 두 번째 인수로 전달받은 노드가 null이면 insertBefore 메서드를 호출한 노드의 마지막 자식 노드로 추가됨 = appendChild 처럼 동작

### 노드 이동

- DOM에 이미 존재하는 노드를 appendChild/insertBefore 메서드를 사용하여 DOM에 다시 추가하면 현재 위치에서 노드를 제거하고 새로운 위치에 노드가 추가됨 = **노드 이동**
- % 오 노드를 이동시킬 수도 있구나

```html
<!DOCTYPE html>
<html>
  <body>
    <ul id="fruits">
      <li>Apple</li>
      <li>Banana</li>
      <li>Orange</li>
    </ul>
  </body>
  <script>
    const $fruits = document.getElementById('fruits');

    // 이미 존재하는 요소 노드를 취득
    const [$apple, $banana] = $fruits.children;

    // 이미 존재하는 $apple 요소 노드를 #fruits 요소 노드의 마지막 노드로 이동
    $fruits.appendChild($apple); // Banana - Orange - Apple

    // 이미 존재하는 $banana 요소 노드를 #fruits 요소의 마지막 자식 노드 앞으로 이동
    $fruits.insertBefore($banana, $fruits.lastElementChild);
    // Orange - Banana - Apple
  </script>
</html>
```

### 노드 복사

- `Node.prototype.cloneNode([deep: true|false])`
  - 노드의 사본을 생성하여 반환
  - 매개변수 deep에 true를 전달하면 노드를 깊은 복사하여 모든 자손노드가 포함된 사본을 생성
  - False를 전달하거나 생략하면 노드를 얕은 복사하여 노드 자신만의 사본을 생성
    - 얕은 복사로 생성된 요소 노드는 자손 노드를 복사하지 않으므로 텍스트 노드도 없음

```html
<!DOCTYPE html>
<html>
  <body>
    <ul id="fruits">
      <li>Apple</li>
    </ul>
  </body>
  <script>
    const $fruits = document.getElementById('fruits');
    const $apple = $fruits.firstElementChild;

    // $apple 요소를 얕은 복사하여 사본을 생성. 텍스트 노드가 없는 사본이 생성된다.
    const $shallowClone = $apple.cloneNode();
    // 사본 요소 노드에 텍스트 추가
    $shallowClone.textContent = 'Banana';
    // 사본 요소 노드를 #fruits 요소 노드의 마지막 노드로 추가
    $fruits.appendChild($shallowClone);

    // #fruits 요소를 깊은 복사하여 모든 자손 노드가 포함된 사본을 생성
    const $deepClone = $fruits.cloneNode(true);
    // 사본 요소 노드를 #fruits 요소 노드의 마지막 노드로 추가
    $fruits.appendChild($deepClone);
  </script>
</html>
```

### 노드 교체

- `Node.prototype.replaceChild(newChild, oldChild)`
  - 자신을 호출한 노드의 자식 노드를 다른 노드로 교체
  - 첫 번째 매개변수: 교체할 새로운 노드
  - 두 번째 매개변수: 이미 존재하는 교체될 노드
    - replaceChild 메서드를 호출한 노드의 자식 노드여야 함
- oldChild 노드는 newChild 노드로 교체됨
- oldChild 노드는 DOM에서 제거됨

```html
<!DOCTYPE html>
<html>
  <body>
    <ul id="fruits">
      <li>Apple</li>
    </ul>
  </body>
  <script>
    const $fruits = document.getElementById('fruits');

    // 기존 노드와 교체할 요소 노드를 생성
    const $newChild = document.createElement('li');
    $newChild.textContent = 'Banana';

    // #fruits 요소 노드의 첫 번째 자식 요소 노드를 $newChild 요소 노드로 교체
    $fruits.replaceChild($newChild, $fruits.firstElementChild);
  </script>
</html>
```

### 노드 삭제

- `Node.prototype.removeChild(child)`
  - 매개변수에 인수로 전달한 노드를 DOM에서 삭제
    - removeChild 메서드를 호출한 노드의 자식노드여야 함

```html
<!DOCTYPE html>
<html>
  <body>
    <ul id="fruits">
      <li>Apple</li>
      <li>Banana</li>
    </ul>
  </body>
  <script>
    const $fruits = document.getElementById('fruits');

    // #fruits 요소 노드의 마지막 요소를 DOM에서 삭제
    $fruits.removeChild($fruits.lastElementChild);
  </script>
</html>
```

## 39.7 어트리뷰트

### 어트리뷰트 노드와 attributes 프로퍼티

- HTML 요소는 여러 개의 어트리뷰트를 가질 수 있음

```html
<input id="user" type="text" value="ttaerrim" />
```

- 글로벌 어트리뷰트와 이벤트핸들러 어트리뷰트는 모든 HTML 요소에서 공통적으로 사용할 수 있지만 특정 HTML 요소에만 한정적으로 사용 가능한 어트리뷰트도 있음

  - & ex
    - 모든 HTML 요소에 사용 가능: id, class, style
    - input 요소에만 사용 가능: type, value, checked

- HTML 어트리뷰트당 하나의 어트리뷰트 노드 생성
- 모든 어트리뷰트 노드의 참조는 NamedNodeMap 객체에 담겨 요소 노드의 attributes 프로퍼티에 저장됨

### HTML 어트리뷰트 조작

- 요소 노드의 attributes 프로퍼티는 getter만 존재하는 읽기 전용 접근자 프로퍼티
- `Element.prototype.getAttribute/setAttribute` 메서드를 사용하여 간편하게 요소 노드에서 직접 HTML 어트리뷰트 값을 취득/변경할 수 있음

- `Element.prototype.hasAttribute(attributeName)`: 특정 HTML 어트리뷰트가 존재하는지 확인
- `Element.prototype.removeAttribute(attributeName)`: 특정 HTML 어트리뷰트 삭제

### HTML 어트리뷰트 vs. DOM 프로퍼티

- 요소 노드 객체에는 HTML 어트리뷰트에 대응하는 프로퍼티가 존재하고 이 DOM 프로퍼티들은 HTML 어트리뷰트 값을 초기값으로 가짐
- ~ ex
  `<input id="user" type="text" value="ttaerrim">`
  - id, type, value 어트리뷰트에 대응하는 id, type, value 프로퍼티가 존재하며
  - 이 DOM 프로퍼티들은 HTML 어트리뷰트의 값을 초기값으로 가지고 있음

![](https://i.imgur.com/vLL4GZg.png)

### data 어트리뷰트와 dataset 프로퍼티

- DOM 프로퍼티는 setter/getter 모두 존재하는 접근자 프로퍼티

  - = 참조/변경 가능

- HTML 어트리뷰트는 DOM에서 중복 관리되는 것처럼 보임

  - 요소 노드의 attributes 프로퍼티에서 관리하는 어트리뷰트 노드
  - HTML 어트리뷰트에 대응하는 요소 노드의 프로퍼티 (DOM 프로퍼티)

- 요소 노드는 초기 상태와 최신 상태를 관리해야 함
  - HTML 어트리뷰트: HTML 요소의 초기 상태
  - DOM 프로퍼티: 요소 노드의 최신 상태
  - ~ ex
    - `<input type="text" value="hi">` 에서 value의 초기 상태는 hi,
    - 이후 사용자가 다른 값 입력하면 그것이 최신 상태

#### 어트리뷰트 노드

- HTML 어트리뷰트로 지정한 HTML 요소의 초기 상태는 어트리뷰트 노드에서 관리함
- 사용자의 입력에 의해 상태가 변경되어도 초기 상태를 그대로 유지

getAttribute/setAttribute

- HTML 요소에 지정한 어트리뷰트 값은 사용자의 입력에 변하지 않으므로 결과 언제나 동일

```js
document.getElementById('user').getAttribute('value');
```

```html
<!DOCTYPE html>
<html>
  <body>
    <input id="user" type="text" value="ungmo2" />
    <script>
      // HTML 요소에 지정한 어트리뷰트 값, 즉 초기 상태 값을 변경한다.
      document.getElementById('user').setAttribute('value', 'foo');
    </script>
  </body>
</html>
```

![](https://i.imgur.com/0a21EkK.png)

#### DOM 프로퍼티

- 사용자가 입력한 최신 상태는 HTML 어트리뷰트에 대응하는 요소 노드의 DOM 프로퍼티가 관리함
- DOM 프로퍼티는 사용자의 입력에 의한 상태 변화에 반응하여 언제나 최신 상태를 유지

- DOM 프로퍼티에 값을 할당하는 것은 HTML 요소의 최신 상태 값을 변경하는 것을 의미

  - 어트리뷰트 값에는 영향 X

- HTML 어트리뷰트는 HTML 요소의 초기 상태 값을 관리하고 DOM 프로퍼티는 사용자의 입력에 의해 변경되는 최신 상태를 관리
  - 모든 DOM 프로퍼티가 최신 상태를 관리하는 것은 아님
  - ~ ex
  - 사용자 입력에 의한 상태 변화와 관계 없는 id 어트리뷰트와 id 프로퍼티는 항상 동일한 값을 유지
  - id 어트리뷰트 값이 변하면 id 프로퍼티 값도 변하고 그 반대도 마찬가지

```html
<!DOCTYPE html>
<html>
  <body>
    <input id="user" type="text" value="ungmo2" />
    <script>
      const $input = document.getElementById('user');

      // id 어트리뷰트와 id 프로퍼티는 사용자 입력과 관계없이 항상 동일한 값으로 연동한다.
      $input.id = 'foo';

      console.log($input.id); // foo
      console.log($input.getAttribute('id')); // foo
    </script>
  </body>
</html>
```

- 이처럼 사용자 입력에 의한 상태 변화와 관계있는 DOM 프로퍼티만 최신 상태 값을 관리함
- 외에 사용자 입력에 의한 상태 변화와 관계없는 어트리뷰트와 DOM 프로퍼티는 항상 동일한 값으로 연동

#### HTML 어트리뷰트와 DOM 프로퍼티의 대응 관계

- 대부분의 HTML 어트리뷰트는 HTML 어트리뷰트 이름과 동일한 DOM 프로퍼티와 1:1 대응
- 언제나 1:1 대응하는 것은 아니며, HTML 어트리뷰트 이름과 DOM 프로퍼티 키가 반드시 일치하는 것도 아님
  - id 어트리뷰트와 id 프로퍼티는 1:1 대응하며, 동일한 값으로 연동한다.
  * input 요소의 value 어트리뷰트는 value 프로퍼티와 1:1 대응한다. 하지만 value 어트리뷰트는 초기 상태를, value 프로퍼티는 최신 상태를 갖는다.
  * class 어트리뷰트는 className, classList 프로퍼티와 대응한다.
  * for 어트리뷰트는 htmlFor 프로퍼티와 1:1 대응한다.
  * td 요소의 colspan 어트리뷰트는 대응하는 프로퍼티가 존재하지 않는다.
  * textContent 프로퍼티는 대응하는 어트리뷰트가 존재하지 않는다.
  * 어트리뷰트 이름은 대소문자를 구별하지 않지만 대응하는 프로퍼티 키는 카멜 케이스를 따른다(maxlength → maxLength).

#### DOM 프로퍼티 값의 타입

- getAttribute 메서드로 취득한 어트리뷰트 값은 언제나 문자열
- DOM 프로퍼티로 취득한 최신 상태 값은 문자열이 아닐 수도 있음
- ~ ex: checkbox 요소
  - checked 어트리뷰트 값: 문자열
  - checked 프로퍼티 값: 불리언

### data 어트리뷰트와 dataset 프로퍼티

- data 어트리뷰트와 dataset 프로퍼티를 사용하면 HTML 요소에 정의한 사용자 정의 어트리뷰트와 자바스크립트 간 데이터를 교환할 수 있음
- data-user-id, data-role과 같이 data- 접두사 다음에 임의의 이름을 붙여 사용

- HTMLElement.dataset 프로퍼티로 취득 가능

```html
<!DOCTYPE html>
<html>
  <body>
    <ul class="users">
      <li id="1" data-user-id="7621" data-role="admin">Lee</li>
      <li id="2" data-user-id="9524" data-role="subscriber">Kim</li>
    </ul>
    <script>
      const users = [...document.querySelector('.users').children];

      // user-id가 '7621'인 요소 노드를 취득한다.
      const user = users.find((user) => user.dataset.userId === '7621');
      // user-id가 '7621'인 요소 노드에서 data-role의 값을 취득한다.
      console.log(user.dataset.role); // "admin"

      // user-id가 '7621'인 요소 노드의 data-role 값을 변경한다.
      user.dataset.role = 'subscriber';
      // dataset 프로퍼티는 DOMStringMap 객체를 반환한다.
      console.log(user.dataset); // DOMStringMap {userId: "7621", role: "subscriber"}
    </script>
  </body>
</html>
```

- data- 뒤의 이름을 카멜케이스로 변환한 프로퍼티를 가지는 DOMStringMap 객체를 반환

- 새로운 dataset 프로퍼티를 추가할 수 있음
- 카멜케이스 프로퍼티 키는 케밥 케이스로 자동 변경되어 추가됨

```html
<!DOCTYPE html>
<html>
  <body>
    <ul class="users">
      <li id="1" data-user-id="7621">Lee</li>
      <li id="2" data-user-id="9524">Kim</li>
    </ul>
    <script>
      const users = [...document.querySelector('.users').children];

      // user-id가 '7621'인 요소 노드를 취득한다.
      const user = users.find((user) => user.dataset.userId === '7621');

      // user-id가 '7621'인 요소 노드에 새로운 data 어트리뷰트를 추가한다.
      user.dataset.role = 'admin';
      console.log(user.dataset);
      /*
    DOMStringMap {userId: "7621", role: "admin"}
    -> <li id="1" data-user-id="7621" data-role="admin">Lee</li>
    */
    </script>
  </body>
</html>
```

## 39.8 스타일

### 인라인 스타일 조작

- `HTMLElement.prototype.styles` 프로퍼티

  - setter/getter 모두 존재하는 접근자 프로퍼티
  - 요소 노드의 인라인 스타일을 취득/추가/변경

- style 프로퍼티를 참조하면 CSSStyleDeclaration 타입의 객체를 반환함
- CSSStyleDeclaration 객체는 다양한 CSS 프로퍼티에 대응하는 프로퍼티를 가지고 있으며
- 이 프로퍼티에 값을 할당하면 해당 CSS 프로퍼티가 인라인 스타일로 HTML 요소에 추가되거나 변경됨

- CSS 프로퍼티는 케밥 케이스
- CSSStyleDeclaration 객체의 프로퍼티는 카멜 케이스
- 단위 지정이 필요한 값은 반드시 단위 지정해야 함

```js
$div.style.backgroundColor = 'yellow';
$div.style['background-color'] = 'yellow';
$div.style.width = '100px';
```

### 클래스 조작

- `.`으로 시작하는 클래스 선택자를 사용하여 CSS 클래스를 미리 정의한 다음 HTML 요소의 클래스 어트리뷰트 값을 변경하여 스타일을 변경할 수도 있음
- class 어트리뷰트에 대응하는 DOM 프로퍼티는 className, classList
  - 자바스크립트에서 class가 예약어이기 때문

#### className

- `Element.prototype.className`

  - setter/getter 모두 존재하는 접근자 프로퍼티
  - class 어트리뷰트 값을 취득/변경

- className 프로퍼티를 참조하면 class 어트리뷰트 값을 문자열로 반환하므로 공백으로 구분된 여러 개의 클래스를 반환하는 경우 다루기 불편함

#### classList

- `Element.prototype.classList`
  - 클래스 어트리뷰트 정보를 담은 DOMTokenList 객체를 반환

```html
<!DOCTYPE html>
<html>
  <head>
    <style>
      .box {
        width: 100px;
        height: 100px;
        background-color: antiquewhite;
      }
      .red {
        color: red;
      }
      .blue {
        color: blue;
      }
    </style>
  </head>
  <body>
    <div class="box red">Hello World</div>
    <script>
      const $box = document.querySelector('.box');

      // .box 요소의 class 어트리뷰트 정보를 담은 DOMTokenList 객체를 취득
      // classList가 반환하는 DOMTokenList 객체는 HTMLCollection과 NodeList와 같이
      // 노드 객체의 상태 변화를 실시간으로 반영하는 살아 있는(live) 객체다.
      console.log($box.classList);
      // DOMTokenList(2) [length: 2, value: "box blue", 0: "box", 1: "blue"]

      // .box 요소의 class 어트리뷰트 값 중에서 'red'만 'blue'로 변경
      $box.classList.replace('red', 'blue');
    </script>
  </body>
</html>
```

- `DOMTokenList`
  - class 어트리뷰트의 정보를 나타내는 컬렉션 객체
  - 유사배열객체이면서 이터러블

DOMTokenList는 다음과 같은 유용한 메서드 제공

- `add(...className)`: 인수로 전달한 1개 이상의 문자열을 class 어트리뷰트 값으로 추가
- `remove(...className)`: 인수로 전달한 1개 이상의 문자열과 일치하는 클래스를 class 어트리뷰트에서 삭제. 일치하는 클래스가 class 어트리뷰트에 없을 경우 에러 없이 무시됨
- `item(index)`: 인수로 전달한 index에 해당하는 클래스를 class 어트리뷰트에서 반환
- `contains(className)`: 인수로 전달한 문자열과 일치하는 클래스가 class 어트리뷰트에 포함되어 있는지 확인
- `replace(oldClassName, newClassName)`: class 어트리뷰트에서 첫 번째 인수로 전달한 문자열을 두 번째 인수로 전달한 문자열로 변경
- `toggle(className[, force])`: class 어트리뷰트에 인수로 전달한 문자열과 일치하는 클래스가 존재하면 제거, 존재하지 않으면 추가
  - 두 번째 인수로 불리언 값으로 평가되는 조건식 전달 가능. 조건식 평가 결과가 true이면 첫 번째 인수로 전달받은 문자열 강제 추가, false이면 강제 제거
- 이 밖에도 forEach, entries, keys, values, supports 메서드 제공

### 요소에 적용되어 있는 CSS 스타일 참조

- style 프로퍼티는 인라인 스타일만 반환
- 클래스를 적용한 스타일이나 상속을 통해 암묵적으로 적용된 스타일은 참조 불가능
- 적용되어 있는 모든 스타일 참조하기 위해 **`getComputedStyle` 메서드 사용**

- 첫 번째 인수로 전달한 요소 노드에 적용되어 있는 평가된 스타일을 `CSSStyleDeclaration` 객체에 담아 반환함
- 요소 노드에 적용되어 있는 모든 스타일(링크 스타일, 임베딩 스타일, 인라인 스타일, 자바스크립트에서 적용한 스타일, 상속된 스타일, 기본 스타일)을 말함

```Html
<!DOCTYPE html>
<html>
<head>
  <style>
    body {
      color: red;
    }
    .box {
      width: 100px;
      height: 50px;
      background-color: cornsilk;
      border: 1px solid black;
    }
  </style>
</head>
<body>
  <div class="box">Box</div>
  <script>
    const $box = document.querySelector('.box');

    // .box 요소에 적용된 모든 CSS 스타일을 담고 있는 CSSStyleDeclaration 객체를 취득
    const computedStyle = window.getComputedStyle($box);
    console.log(computedStyle); // CSSStyleDeclaration

    // 임베딩 스타일
    console.log(computedStyle.width); // 100px
    console.log(computedStyle.height); // 50px
    console.log(computedStyle.backgroundColor); // rgb(255, 248, 220)
    console.log(computedStyle.border); // 1px solid rgb(0, 0, 0)

    // 상속 스타일(body -> .box)
    console.log(computedStyle.color); // rgb(255, 0, 0)

    // 기본 스타일
    console.log(computedStyle.display); // block
  </script>
</body>
</html>
```

- `getComputedStyle` 메서드의 두 번째 인수로 :after, :before와 같은 의사 요소 지정 문자열 전달 가능
  - 의사 요소 아닌 경우 두 번째 인수 생략

```html
<!DOCTYPE html>
<html>
  <head>
    <style>
      .box:before {
        content: 'Hello';
      }
    </style>
  </head>
  <body>
    <div class="box">Box</div>
    <script>
      const $box = document.querySelector('.box');

      // 의사 요소 :before의 스타일을 취득한다.
      const computedStyle = window.getComputedStyle($box, ':before');
      console.log(computedStyle.content); // "Hello"
    </script>
  </body>
</html>
```

## 39.9 DOM 표준

- HTML과 DOM 표준은 W3C와 WHATWG이라는 두 단체가 협력하며 공통된 표준을 만들어 왔음
- 그런데 2019년부터 두 단체가 서로 다른 결과물을 내놓기 시작
- 별개의 HTML과 DOM 표준을 만드는 것은 이롭지 않으므로 2018년 4월부터 구글, 애플, 마이크로소프트, 모질라로 구성된 4개의 주류 브라우저 벤더사가 주도하는 WHATWG이 단일 표준을 내놓기로 함!
- 현재 다음과 같이 4개의 레벨(버전)이 있음

| 레벨        | 표준 문서                               |
| ----------- | --------------------------------------- |
| DOM Level 1 | https://www.w3.org/TR/REC-DOM-Level-1/  |
| DOM Level 2 | https://www.w3.org/TR/DOM-Level-2-Core/ |
| DOM Level 3 | https://www.w3.org/TR/DOM-Level-3-Core/ |
| DOM Level 4 | https://dom.spec.whatwg.org             |

- [웹표준 – W3C와 WHATWG, HTML5.2와 HTML5.3 그리고 HTML Living Standard는 무엇인가? – 형우의 웹개발](https://mytory.net/archives/13130)
