# 43. Ajax

## 43.1 Ajax란?

- & 아자아자 아작스!
- Ajax란 Asynchrounous JavaScript and XML으로 자바스크립트를 사용해 브라우저가 서버에게 비동기 방식으로 데이터를 요청하고, 서버가 응답한 데이터를 수신해 웹페이지를 동적으로 갱신하는 프로그래밍 방식
- 브라우저에서 제공하는 Web API인 XMLHttpRequest 객체를 기반으로 동작

  - HTTP 비동기 통신을 위한 메서드와 프로퍼티를 제공함

- 전통적인 웹페이지 생명 주기의 단점

  - 이전 웹 페이지와 차이가 없어서 변경할 필요가 없는 부분까지 포함된 HTML을 서버로부터 매번 다시 전송받기 때문에 불필요한 데이터 통신이 발생한다
  - 변경할 필요가 없는 부분까지 처음부터 다시 렌더링 해서 화면 전환 시 화면 깜빡임 발생
  - 클라이언트와 서버 통신 동기 방식으로 동작하기 때문에 서버로부터 응답 있을 때까지 다음 처리 블로킹됨

- Ajax는 이 패러다임을 전환, 다음과 같은 장점을 가짐
  - 변경 필요한 데이터만 서버로부터 전송받기 때문에 불필요한 데이터 통신 발생하지 않음
  - 변경 필요 없는 부분 렌더링하지 않아 화면 깜빡임 없음
  - 클라이언트와 서버와의 통신 비동기 방식으로 동작 > 블로킹 발생하지 않음

## 43.2 JSON

- 클라이언트와 서버 간의 HTTP 통신을 위한 텍스트 데이터 포맷
- 자바스크립트에 종속되지 않는 언어 독립형 데이터 포맷임

### JSON 표기 방식

- 키와 값으로 구성된 텍스트

```json
{
  "name": "Lee",
  "age": 20,
  "alive": true,
  "hobby": ["trabeling", "tennis"]
}
```

- JSON의 키는 반드시 **큰따옴표**로 묶어야 함 (작은따옴표 사용 불가)
- 값은 객체 리터럴와 같은 표기법 그대로 사용 가능

### JSON.stringify

- `JSON.stringify` 메서드는 객체를 JSON 포맷 문자열로 변환
- 클라이언트가 서버로 객체 전송할 때는 객체를 문자열화해야 하는데, 이를 직렬화라고 함
  = 객체를 직렬화해주는 함수

```js
const obj = {
  name: 'Lee',
  age: 20,
  alive: true,
  hobby: ['traveling', 'tennis'],
};

// 객체를 JSON 포맷의 문자열로 변환한다.
const json = JSON.stringify(obj);
console.log(typeof json, json);
// string {"name":"Lee","age":20,"alive":true,"hobby":["traveling","tennis"]}

// 객체를 JSON 포맷의 문자열로 변환하면서 들여쓰기 한다.
const prettyJson = JSON.stringify(obj, null, 2);
console.log(typeof prettyJson, prettyJson);
/*
string {
  "name": "Lee",
  "age": 20,
  "alive": true,
  "hobby": [
    "traveling",
    "tennis"
  ]
}
*/

// replacer 함수. 값의 타입이 Number이면 필터링되어 반환되지 않는다.
function filter(key, value) {
  // undefined: 반환하지 않음
  return typeof value === 'number' ? undefined : value;
}

// JSON.stringify 메서드에 두 번째 인수로 replacer 함수를 전달한다.
const strFilteredObject = JSON.stringify(obj, filter, 2);
console.log(typeof strFilteredObject, strFilteredObject);
/*
string {
  "name": "Lee",
  "alive": true,
  "hobby": [
    "traveling",
    "tennis"
  ]
}
*/
```

- ? 왜 두 번째 인수, 세 번째 인수 설명이 없지

```js
JSON.stringify(value[, replacer[, space]])
```

- 두 번째 인수(replacer)
  - 문자열화 동작 방식을 변경하는 함수이다
  - filter 함수를 전달한 것처럼 값의 타입이 Number라면 반환되지 않도록 설정할 수 있음
  - null이거나 제공하지 않으면 객체의 모든 속성이 JSON 문자열 결과에 포함됨
- 세 번째 인수(space)

  - 공백으로 사용되는 space의 수를 나타냄
  - 10보다 크면 10으로 제한됨
  - 1보다 작으면 스페이스가 사용되지 않는 것을 나타냄
  - String 값을 전달하면 그 문자가 공백으로 사용됨
  - null이거나 전달하지 않으면 공백 사용되지 않음

- 객체뿐 아니라 배열도 JSON 포맷의 문자열로 변환 가능

```js
const todos = [
  { id: 1, content: 'HTML', completed: false },
  { id: 2, content: 'CSS', completed: true },
  { id: 3, content: 'Javascript', completed: false },
];

// 배열을 JSON 포맷의 문자열로 변환한다.
const json = JSON.stringify(todos, null, 2);
console.log(typeof json, json);
/*
string [
  {
    "id": 1,
    "content": "HTML",
    "completed": false
  },
  {
    "id": 2,
    "content": "CSS",
    "completed": true
  },
  {
    "id": 3,
    "content": "Javascript",
    "completed": false
  }
]
*/
```

### JSON.parse

- `JSON.parse` 메서드는 JSON 포맷의 문자열을 객체로 변환함
- 서버로부터 전송된 JSON 데이터를 객체로 사용하려면 역직렬화가 필요함

```js
const obj = {
  name: 'Lee',
  age: 20,
  alive: true,
  hobby: ['traveling', 'tennis'],
};

// 객체를 JSON 포맷의 문자열로 변환한다.
const json = JSON.stringify(obj);

// JSON 포맷의 문자열을 객체로 변환한다.
const parsed = JSON.parse(json);
console.log(typeof parsed, parsed);
// object {name: "Lee", age: 20, alive: true, hobby: ["traveling", "tennis"]}
```

- 배열이 JSON 포맷의 문자열로 변환되어 잇는 경우 JSON.parse는 문자열을 배열 객체로 변환
- 배열의 요소가 객체인 경우 배열의 요소까지 객체로 변환

```js
const todos = [
  { id: 1, content: 'HTML', completed: false },
  { id: 2, content: 'CSS', completed: true },
  { id: 3, content: 'Javascript', completed: false },
];

// 배열을 JSON 포맷의 문자열로 변환한다.
const json = JSON.stringify(todos);

// JSON 포맷의 문자열을 배열로 변환한다. 배열의 요소까지 객체로 변환된다.
const parsed = JSON.parse(json);
console.log(typeof parsed, parsed);
/*
 object [
  { id: 1, content: 'HTML', completed: false },
  { id: 2, content: 'CSS', completed: true },
  { id: 3, content: 'Javascript', completed: false }
]
*/
```

## 43.3 XMLHttpRequest

- 자바스크립트를 사용해 HTTP 요청을 전송하려면 XMLHttpRequest 객체를 사용함
- XMLHttpRequest 객체는 HTTP 요청 전송과 HTTP 응답 수신을 위한 다양한 메서드와 프로퍼티를 제공

### XMLHttpRequest 객체 생성

- XMLHttpRequest 생성자 함수를 호출하여 생성
- 브라우저에서 제공하는 Web API이므로 브라우저 환경에서만 정상적으로 실행됨

```js
const xhr = new XMLHttpRequest();
```

### XMLHttpRequest 객체의 프로퍼티와 메서드

#### XMLHttpRequest 객체의 프로토타입 프로퍼티

| 프로토타입 프로퍼티 | 설명                                                                                                                                                                                    |
| ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| readyState          | HTTP 요청의 현재 상태를 나타내는 정수.<br>다음과 같은 XMLHttpRequest의 정적 프로퍼티를 값으로 가짐.<br>- UNSENT: 0<br>- OPENED: 1<br>- HEADERS_RECEIVED: 2<br>- LOADING: 3<br>- DONE: 4 |
| status              | HTTP 요청에 대한 응답 상태를 나타내는 정수<br>예) 200                                                                                                                                   |
| statusText          | HTTP 요청에 대한 응답 메시지를 나타내는 문자열<br>예) "OK"                                                                                                                              |
| responseType        | HTTP 응답 타입<br>예) document, json, text, blob, arraybuffer                                                                                                                           |
| response            | HTTP 요청에 대한 응답 몸체. responseType에 따라 타입이 다름.                                                                                                                            |

#### XMLHttpRequest 객체의 이벤트 핸들러 프로퍼티

| 이벤트 핸들러 프로퍼티 | 설명                                 |
| ---------------------- | ------------------------------------ |
| onreadystatechange     | readyState 프로퍼티 값이 변경된 경우 |
| onloadstart            |                                      |
| onprogress             |                                      |
| onabort                |                                      |
| onerror                | HTTP 요청에 에러가 발생한 경우       |
| onload                 | HTTP 요청이 성공적으로 완료한 경우   |
| ontimeout              |                                      |
| onloadend              |                                      |

#### XMLHttpRequest 객체의 메서드

| 메서드           | 설명                            |
| ---------------- | ------------------------------- |
| open             | HTTP 요청 초기화                |
| send             | HTTP 요청 전송                  |
| abort            | 이미 전송된 HTTP 요청 중단      |
| setRequestHeader | 특정 HTTP 요청 헤더의 값을 설정 |
| getRequestHeader |                                 |

#### XMLHttpRequest 객체의 정적 프로퍼티

| 정적 프로퍼티    | 값    | 설명                  |
| ---------------- | ----- | --------------------- |
| UNSENT           | 0     | open 메서드 호출 이전 |
| OPENED           | 1     | open 메서드 호출 이후 |
| HEADERS_RECEIVED | 2     | send 메서드 호출 이후 |
| LOADING          | 3     | 서버 응답 중          |
| **DONE**         | **4** | **서버 응답 완료**    |

### HTTP 요청 전송

- HTTP 요청을 전송하는 경우 다음 순서를 따름
  1.  XMLHttpRequest.prototype.open 메서드로 HTTP 요청 초기화
  2.  필요에 따라 XMLHttpRequest.prototype.setRequestHeader 메서드로 특정 HTTP 요청의 헤더 값을 설정함
  3.  XMLHttpRequest.prototype.send 메서드로 HTTP 요청을 전송함

```js
// XMLHttpRequest 객체 생성
const xhr = new XMLHttpRequest();

// HTTP 요청 초기화
xhr.open('GET', '/users');

// HTTP 요청 헤더 설정
// 클라이언트가 서버로 전송할 데이터의 MIME 타입 지정: json
xhr.setRequestHeader('content-type', 'application/json');

// HTTP 요청 전송
xhr.send();
```

#### XMLHttpRequest.prototype.open

- open 메서드는 서버에 전송할 HTTP 요청을 초기화한다

```js
xhr.open(method, url, async);
```

| 매개변수 | 설명                                         |
| -------- | -------------------------------------------- |
| method   | HTTP 요청 메서드 (GET, POST, PUT, DELETE 등) |
| url      | HTTP 요청을 전송할 URL                       |
| async    | 비동기 요청 여부, 옵션으로 기본값 true       |

- HTTP 요청 메서드는 클라이언트가 서버에게 요청의 종류와 목적을 알리는 방법

| HTTP 요청 메서드 | 종류           | 목적                  | 페이로드 |
| ---------------- | -------------- | --------------------- | -------- |
| GET              | index/retrieve | 모든/특정 리소스 취득 | X        |
| POST             | create         | 리소스 생성           | O        |
| PUT              | replace        | 리소스의 전체 교체    | O        |
| PATCH            | modify         | 리소스의 일부 수정    | O        |
| DELETE           | delete         | 모든/특정 리소스 삭제 | X        |

#### XMLHttpRequest.prototype.send

- send 메서드는 open 메서드로 초기화된 HTTP 요청을 서버에 전송
- 기본적으로 서버에 전송하는 데이터는 GET, POST 요청 메서드에 따라 전송 방식에 차이가 있음
  - GET 요청 메서드의 경우 데이터를 URL의 일부분인 쿼리 스트링으로 서버에 전송
  - POST 요청 메서드의 경우 데이터를 request body에 담아 전송
- payload가 객체인 경우 반드시 JSON.stringify 메서드를 사용해 직렬화한 다음 전달해야 함
- HTTP 요청 메서드가 GET인 경우 send 메서드에 payload로 전달한 인수는 무시되고 요청 몸체는 null로 설정됨

#### XMLHttpRequest.prototype.setRequestHeader

- setRequestHeader 메서드는 특정 HTTP 요청의 헤더 값을 설정함
- setRequestHeader 메서드는 반드시 open 메서드를 호출한 이후에 호출해야 함

- Content-type은 요청 몸체에 담아 전송할 데이터의 MIME 타입의 정보를 표현함
  - Multipurpose Internet Mail Extensions or MIME type (미디어 타입?)

| MIME 타입   | 서브 타입                                          |
| ----------- | -------------------------------------------------- |
| text        | text/plain, text/html, text/css, text/javascript   |
| application | application/json, application/x-www-form-urlencode |
| multipart   | multipart/formed-data                              |

- MIME 타입 지정하는 예시

```js
// XMLHttpRequest 객체 생성
const xhr = new XMLHttpRequest();

// HTTP 요청 초기화
xhr.open('POST', '/users');

// HTTP 요청 헤더 설정
// 클라이언트가 서버로 전송할 데이터의 MIME 타입 지정: json
xhr.setRequestHeader('content-type', 'application/json');

// HTTP 요청 전송
xhr.send(JSON.stringify({ id: 1, content: 'HTML', completed: false }));
```

- HTTP 클라이언트가 서버에 요청할 때 서버가 응답할 데이터의 MIME 타입을 Accept로 지정할 수 있음

```js
xhr.setRequestHeader('accept', 'application/json');
```

### HTTP 응답 처리

- 서버가 전송한 응답을 처리하려면 XMLHttpRequest 객체가 발생시키는 이벤트를 캐치해야 함
- XMLHttpRequest는 onreadystatechange, onload, onerror 같은 이벤트 핸들러 프로퍼티 중 HTTP 요청의 현재 상태를 나타내는 readyState 프로퍼티 값이 변경된 경우 발생하는 readystatechange 이벤트를 캐치해 다음과 같이 HTTP 응답을 처리할 수 있음

```js
// XMLHttpRequest 객체 생성
const xhr = new XMLHttpRequest();

// HTTP 요청 초기화
// https://jsonplaceholder.typicode.com은 Fake REST API를 제공하는 서비스다.
xhr.open('GET', 'https://jsonplaceholder.typicode.com/todos/1');

// HTTP 요청 전송
xhr.send();

// readystatechange 이벤트는 HTTP 요청의 현재 상태를 나타내는 readyState 프로퍼티가
// 변경될 때마다 발생한다.
xhr.onreadystatechange = () => {
  // readyState 프로퍼티는 HTTP 요청의 현재 상태를 나타낸다.
  // readyState 프로퍼티 값이 4(XMLHttpRequest.DONE)가 아니면 서버 응답이 완료되지 상태다.
  // 만약 서버 응답이 아직 완료되지 않았다면 아무런 처리를 하지 않는다.
  if (xhr.readyState !== XMLHttpRequest.DONE) return;

  // status 프로퍼티는 응답 상태 코드를 나타낸다.
  // status 프로퍼티 값이 200이면 정상적으로 응답된 상태이고
  // status 프로퍼티 값이 200이 아니면 에러가 발생한 상태다.
  // 정상적으로 응답된 상태라면 response 프로퍼티에 서버의 응답 결과가 담겨 있다.
  if (xhr.status === 200) {
    console.log(JSON.parse(xhr.response));
    // {userId: 1, id: 1, title: "delectus aut autem", completed: false}
  } else {
    console.error('Error', xhr.status, xhr.statusText);
  }
```

- readystatechange 이벤트가 readyState 프로퍼티가 변경될 때마다 발생함
- onreadystatechange 이벤트 핸들러 프로퍼티에 할당한 이벤트 핸들러는 HTTP 요청의 현재 상태를 나타내는 xhr.readyState가 XMLHttpRequest.DONE인지 확인하여 서버의 응답이 완료되었는지 확인함
- 서버의 응답이 완료되면 xhr.status === 200인지 확인하여 정상처리와 에러처리를 구분,
  정상적으로 도착했다면 xhr.response에서 서버가 전송한 데이터를 취득함 / 아니면 에러처리

- readystatechange 이벤트 대신 load 이벤트를 캐치해도 좋음
- load 이벤트는 HTTP 요청이 성공적으로 완료된 경우 발생함, 따라서 load 이벤트를 캐치하는 경우에는 xhr.readyState가 XMLHttpRequest.DONE인지 확인할 필요가 없음
