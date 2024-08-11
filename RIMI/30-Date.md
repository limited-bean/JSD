# 30. Date

표준 빌트인 객체 Date는 날짜와 시간을 위한 메서드를 제공하는 빌트인 객체이면서 생성자 함수이다.

- UTC: 국제 표준시, 그리니치 평균시(GMT)
- KST: 한국 표준시는 UTC + 9시간
  - UTC보다 9시간 빠름
  - UTC 00:00 AM === KST 09:00 AM

## 30.1 Date 생성자 함수

- Date 생성자 함수로 생성한 Date 객체는 내부적으로 날짜와 시간을 나타내는 정수값을 가짐
- 1970년 1월 1일 00:00:00을 기점으로 Date 객체가 나타내는 날짜와 시간까지의 밀리초를 나타냄

### 1. new Date()

```js
new Date(); // -> Mon Jul 06 2020 01:03:18 GMT+0900 (대한민국 표준시)
Date(); // -> "Mon Jul 06 2020 01:10:47 GMT+0900 (대한민국 표준시)"
```

- 생성자 함수로 new 연산자와 함께 호출하면 현재 날짜와 시간을 가지는 Date 객체 반환
- new 연산자 없이 호출하면 Date 객체를 반환하지 않고 날짜와 시간 정보를 나타내는 문자열을 반환

### 2. new Date(milliseconds)

- 숫자 타입의 밀리초를 인수로 전달
- 1970-01-01 00:00:00으로부터 인수 밀리초만큼 경과된 날짜와 시간을 나타내는 Date 객체를 반환함

```js
// 한국 표준시 KST는 협정 세계시 UTC에 9시간을 더한 시간이다.
new Date(0); // -> Thu Jan 01 1970 09:00:00 GMT+0900 (대한민국 표준시)

/*
86400000ms는 1day를 의미한다.
1s = 1,000ms
1m = 60s * 1,000ms = 60,000ms
1h = 60m * 60,000ms = 3,600,000ms
1d = 24h * 3,600,000ms = 86,400,000ms
*/
new Date(86400000); // -> Fri Jan 02 1970 09:00:00 GMT+0900 (대한민국 표준시)
```

### 3. new Date(dateString)

- 날짜와 시간을 나타내는 문자열을 인수로 전달하면 지정된 날짜와 시간을 나타내는 Date 객체를 반환
  - 인수로 전달한 문자열은 Date.parse 메서드에 의해 해석 가능해야 함

```js
new Date('May 26, 2020 10:00:00');
// -> Tue May 26 2020 10:00:00 GMT+0900 (대한민국 표준시)

new Date('2020/03/26/10:00:00');
// -> Thu Mar 26 2020 10:00:00 GMT+0900 (대한민국 표준시)
```

### 4. new Date(year, month[, day, hour, minute, second, miilisecond)

- year, month까지는 필수 값

```js
// 월을 나타내는 2는 3월을 의미한다. 2020/3/1/00:00:00:00
new Date(2020, 2);
// -> Sun Mar 01 2020 00:00:00 GMT+0900 (대한민국 표준시)

// 월을 나타내는 2는 3월을 의미한다. 2020/3/26/10:00:00:00
new Date(2020, 2, 26, 10, 00, 00, 0);
// -> Thu Mar 26 2020 10:00:00 GMT+0900 (대한민국 표준시)

// 다음처럼 표현하면 가독성이 훨씬 좋다.
new Date('2020/3/26/10:00:00:00');
// -> Thu Mar 26 2020 10:00:00 GMT+0900 (대한민국 표준시)
```

## 30.2 Date 메서드

### 1. Date.now

- 1970년 1월 1일 00:00:00을 기점으로 현재 시간까지 경과한 밀리초를 숫자로 반환

```js
const now = Date.now();
new Date(now); // Wed May 29 2024 09:59:35 GMT+0900 (한국 표준시)
console.log(now); // 1716944375380
```

### 2. Date.parse

- 1970년 1월 1일 00:00:00을 기점으로 인수로 전달된 지정 시간까지의 밀리초를 숫자로 반환한다

```js
// UTC
Date.parse('Jan 2, 1970 00:00:00 UTC'); // -> 86400000

// KST
Date.parse('Jan 2, 1970 09:00:00'); // -> 86400000

// KST
Date.parse('1970/01/02/09:00:00'); // -> 86400000

Date.parse(new Date()); // 1716944496000
```

### 3. Date.UTC

- 1970년 1월 1일 00:00:00을 기점으로 인수로 전달된 지정 시간까지의 밀리초를 숫자로 반환
- `new Date(year, month[, day, hour, minute, second, miilisecond)`와 같은 형식의 인수를 사용해야 함
- 인수는 KST가 아닌 UTC로 인식됨

```js
Date.UTC(1970, 0, 2); // -> 86400000
Date.UTC('1970/1/2'); // -> NaN
```

### 4. Date.prototype.getFullYear

- Date 객체의 연도를 나타내는 정수 반환

```js
new Date().getFullYear(); // 2024
```

### 5. Date.prototype.setFullYear

- Date 객체에 연도를 나타내는 정수 설정
- 옵션으로 월, 일도 설정 가능
-

```js
const today = new Date();

// 년도 지정
today.setFullYear(1999);
console.log(today); // Sat May 29 1999 10:03:44 GMT+0900 (한국 표준시)
today.getFullYear(); // -> 1999

// 년도/월/일 지정
today.setFullYear(1900, 0, 1);
console.log(today); // Mon Jan 01 1900 10:03:44 GMT+0827 (한국 표준시)
today.getFullYear(); // -> 1900
```

### 6. Date.prototype.getMonth

- Date 객체의 월을 나타내는 0 ~ 11 사이의 정수를 반환
- 1월은 0, 12월은 11

```js
new Date('2020/07/24').getMonth(); // -> 6
new Date().getMonth(); // 4 (오늘은 5월)
```

### 7. Date.prototype.setMonth

- Date 객체의 월을 나타내는 0 ~ 11 사이의 정수를 설정
- 월 이외 옵션으로 일도 설정 가능

```js
const today = new Date();

// 월 지정
today.setMonth(0); // 1월
today.getMonth(); // -> 0
console.log(today); // Mon Jan 29 2024 10:07:37 GMT+0900 (한국 표준시)

// 월/일 지정
today.setMonth(11, 1); // 12월 1일
today.getMonth(); // -> 11
console.log(today); // Sun Dec 01 2024 10:07:37 GMT+0900 (한국 표준시)
```

### 8. Date.prototype.getDate

- Date 객체의 일을 나타내는 1 ~ 31 사이의 정수를 반환

```js
new Date('2020/07/24').getDate(); // -> 24
```

### 9. Date.prototype.setDate

- Date 객체의 일을 나타내는 1 ~ 31 사이의 정수를 설정 가능

```js
const today = new Date();

// 날짜 지정
today.setDate(1);
today.getDate(); // -> 1
console.log(today); // Wed May 01 2024 10:0
```

### 10. Date.prototype.getDay

- Date 객체의 요일을 나타내는 정수를 반환
  - 일요일: 0, 월요일: 1, ~ , 토요일: 6

```js
const today = new Date();
today.getDay(); // 4
console.log(today); // Thu May 30 2024 09:53:14 GMT+0900 (한국 표준시)
```

### 11. Date.prototype.getHours

- 객체의 시간을 나타내는 정수를 반환

```js
const today = new Date();
today.getHours(); // 9
console.log(today); // Thu May 30 2024 09:53:14 GMT+0900 (한국 표준시)
```

### 12. Date.prototype.setHours

- 객체에 시간을 나타내는 정수를 설정
  - 시간 외에 분, 초, 밀리초도 설정 가능

```js
const today = new Date();

// 시간 지정
today.setHours(7);
today.getHours(); // -> 7
console.log(today); // Thu May 30 2024 07:03:57 GMT+0900 (한국 표준시)

// 시간/분/초/밀리초 지정
today.setHours(0, 0, 0, 0); // 00:00:00:00
today.getHours(); // -> 0
console.log(today); // Thu May 30 2024 00:00:00 GMT+0900 (한국 표준시)
```

### 13. Date.prototype.getMinutes

- Date 객체의 분을 나타내는 정수를 반환

```js
const today = new Date();
today.getMinutes(); // 4

console.log(today); // Thu May 30 2024 10:04:40 GMT+0900 (한국 표준시)
```

### 14. Date.prototype.setMinutes

- Date 객체에 분을 나타내는 정수를 설정
  - 분 이외에 옵션으로 초, 밀리초도 설정 가능

```js
const today = new Date();

// 분 지정
today.setMinutes(50);
today.getMinutes(); // -> 50

// 분/초/밀리초 지정
today.setMinutes(5, 10, 999); // HH:05:10:999
today.getMinutes(); // -> 5
```

### 15. Date.prototype.getSeconds

- Date 객체의 초를 나타내는 정수를 반환

```js
new Date('2020/07/24/12:30:10').getSeconds(); // -> 10
```

### 16. Date.prototype.setSeconds

- Date 객체에 초를 나타내는 정수를 설정
  - 초 이외에 옵션으로 밀리초도 설정 가능

```js
const today = new Date();

// 초 지정
today.setSeconds(30);
today.getSeconds(); // -> 30

// 초/밀리초 지정
today.setSeconds(10, 0); // HH:MM:10:000
today.getSeconds(); // -> 10
```

### 17. Date.prototype.getMilliseconds

- Date 객체의 밀리초를 나타내는 정수를 반환

### 18. Date.prototype.setMilliseconds

- Date 객체에 밀리초를 나타내는 정수를 설정

### 19. Date.prototype.getTime

- 1970년 1월 1일 00:00:00 (UTC)를 기점으로 Date 객체의 시간까지 경과된 밀리초를 반환

```js
new Date('2020/07/24/12:30').getTime(); // -> 1595561400000
```

### 20. Date.prototype.setTime

- 1970년 1월 1일 00:00:00 (UTC)를 기점으로 Date 객체의 시간까지 경과된 밀리초를 설정

```js
const today = new Date();

// 1970년 1월 1일 00:00:00(UTC)를 기점으로 경과된 밀리초 설정
today.setTime(86400000); // 86400000는 1day를 나타낸다.
console.log(today); // -> Fri Jan 02 1970 09:00:00 GMT+0900 (대한민국 표준시)
```

### 21. Date.prototype.getTimezoneOffset

- UTC와 Date 객체에 지정된 locale 시간과의 차이를 분 단위로 반환
- UTC = KST - 9h

```js
const today = new Date(); // today의 지정 로캘은 KST다.

//UTC와 today의 지정 로캘 KST와의 차이는 -9시간이다.
today.getTimezoneOffset() / 60; // -9
```

### 22. Date.prototype.toDateString

- 사람이 읽을 수 있는 형식의 문자열로 Date 객체의 날짜를 반환

```js
const today = new Date('2020/7/24/12:30');

today.toString(); // -> Fri Jul 24 2020 12:30:00 GMT+0900 (대한민국 표준시)
today.toDateString(); // -> Fri Jul 24 2020
```

### 23. Date.prototype.toTimeString

- 사람이 읽을 수 있는 형식으로 Date 객체의 시간을 표현한 문자열을 반환

```js
const today = new Date('2020/7/24/12:30');

today.toString(); // -> Fri Jul 24 2020 12:30:00 GMT+0900 (대한민국 표준시)
today.toTimeString(); // -> 12:30:00 GMT+0900 (대한민국 표준시)
```

### 24. Date.prototype.toISOString

- ISO 8601 형식으로 Date 객체의 날짜와 시간을 표현한 문자열을 반환

```js
const today = new Date('2020/7/24/12:30');

today.toString(); // -> Fri Jul 24 2020 12:30:00 GMT+0900 (대한민국 표준시)
today.toISOString(); // -> 2020-07-24T03:30:00.000Z

today.toISOString().slice(0, 10); // -> 2020-07-24
today.toISOString().slice(0, 10).replace(/-/g, ''); // -> 20200724
```

### 25. Date.prototype.toLocaleString

- 인수로 전달한 locale을 기준으로 Date 객체의 날짜와 시간을 표현한 문자열을 반환
  - 인수 생략시 브라우저가 동작 중인 시스템의 locale을 적용

```js
const today = new Date('2020/7/24/12:30');

today.toString(); // -> Fri Jul 24 2020 12:30:00 GMT+0900 (대한민국 표준시)
today.toLocaleString(); // -> 2020. 7. 24. 오후 12:30:00
today.toLocaleString('ko-KR'); // -> 2020. 7. 24. 오후 12:30:00
today.toLocaleString('en-US'); // -> 7/24/2020, 12:30:00 PM
today.toLocaleString('ja-JP'); // -> 2020/7/24 12:30:00
```

### 26. Date.prototype.toLocaleTimeString

- 인수로 전달한 locale을 기준으로 Date 객체의 시간을 표현한 문자열을 반환
  - 인수 생략시 브라우저가 동작 중인 시스템의 locale을 적용

```js
const today = new Date('2020/7/24/12:30');

today.toString(); // -> Fri Jul 24 2020 12:30:00 GMT+0900 (대한민국 표준시)
today.toLocaleTimeString(); // -> 오후 12:30:00
today.toLocaleTimeString('ko-KR'); // -> 오후 12:30:00
today.toLocaleTimeString('en-US'); // -> 12:30:00 PM
today.toLocaleTimeString('ja-JP'); // -> 12:30:00
```

## 30.3 Date를 활용한 시계 예제

```js
(function printNow() {
  const today = new Date();

  const dayNames = [
    '(일요일)',
    '(월요일)',
    '(화요일)',
    '(수요일)',
    '(목요일)',
    '(금요일)',
    '(토요일)',
  ];
  // getDay 메서드는 해당 요일(0 ~ 6)을 나타내는 정수를 반환한다.
  const day = dayNames[today.getDay()];

  const year = today.getFullYear();
  const month = today.getMonth() + 1;
  const date = today.getDate();
  let hour = today.getHours();
  let minute = today.getMinutes();
  let second = today.getSeconds();
  const ampm = hour >= 12 ? 'PM' : 'AM';

  // 12시간제로 변경
  hour %= 12;
  hour = hour || 12; // hour가 0이면 12를 재할당

  // 10미만인 분과 초를 2자리로 변경
  minute = minute < 10 ? '0' + minute : minute;
  second = second < 10 ? '0' + second : second;

  const now = `${year}년 ${month}월 ${date}일 ${day} ${hour}:${minute}:${second} ${ampm}`;

  console.log(now);

  // 1초마다 printNow 함수를 재귀 호출한다. 41.2.1절 "setTimeout / clearTimeout" 참고
  setTimeout(printNow, 1000);
})();
```
