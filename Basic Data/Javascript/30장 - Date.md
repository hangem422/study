# 30. Date

표준 빌트인 객체인 `Date`는 날짜와 시간(연, 월, 일, 시, 분, 초, 밀리초(Millisecond/ms))을 위한 메서드를 제공하는 빌트인 객체이면서 생성자 함수입니다. 현재 날짜와 시간은 자바스크립트 코드가 실행된 시스템의 시계에 의해 결정됩니다.

**UTC**(협정 세계시, Coordinated Universal Time)는 국제 표준시를 말합니다. UTC는 GMT(그리니치 평균 시, Creenwich Mean Time)로 불리기도 합니다. UTC와 GMT는 초의 소수점 단위에서만 차이가 나기 때문에 일상에서는 혼용되어 사용됩니다. 기술적인 표기에서는 UTC가 사용됩니다. KST(한국 표순시, Korean Standard Time)는 UTCㅇ체 9시간을 더한 시간입니다. 즉, KST는 UTC보다 9시간이 빠릅니다.

## 30.1 Date 생성자 함수

`Date`는 생성자 함수입니다. `Date` 생성자 함수로 생성한 `Date` 객체는 내부적으로 날짜와 시간을 나타내는 정수값을 갖습니다. 이 값은 1970년 1월 1일 00:00:00(UTC)을 기점으로 `Date` 객체가 나타내는 날짜와 시간까지의 밀리초를 나타냅니다. `Date` 생성자 함수로 객체를 생성하는 방법은 다음과 같이 4가지가 있습니다.

### 30.1.1 new Date()

`Date` 생성자 함수를 인수 없이 `new` 연산자와 함꼐 호출하면 현재 날짜와 시간을 가지는 `Date` 객체를 반환합니다. `Date` 생성자 함수를 `Date` 객체는 내부적으로 날짜와 시간을 나타내는 정수값을 갖지만 `Date` 객체를 콘솔에 출력하면 기본적으로 날짜와 시간 정보를 출력합니다.

```javascript
const date = new Date();

console.log(date); // Mon Jul 06 2020 01:03:18 GMT+0900 (대한민국 표준시)
console.log(typeof date); // object
```

`Date` 생성자 함수를 `new` 연산자 없이 호출하면 `Date` 객체를 반환하지 않고 날짜와 시간 정보를 나타내는 문자열을 반환합니다.

```javascript
const date = Date();

console.log(date); // Mon Jul 06 2020 01:03:18 GMT+0900 (대한민국 표준시)
console.log(typeof date); // string
```

### 30.1.2 new Date(milliseconds)

`Date` 생성자 함수에 숫자 타입의 밀리초를 인수로 전달하면 1970년 1월 1일 00:00:00(UTC)을 기점으로 인수로 전달된 밀리초만큼 경과한 날짜와 시간을 나타내는 `Date` 객체를 반환합니다.

```javascript
new Date(0); // Thu Jan 01 1970 09:00:00 GMT+0900 (대한민국 표준시)
new Date(86400000); // `Fri Jan 02 1970 09:00:00 GMT+0900 (대한민국 표준시)
```

### 30.1.3 new Date(dateString)

`Date` 생성자 함수에 날짜와 시간을 나타내는 문자열을 인수로 전달하면 지정된 날짜와 시간을 나타내는 `Date` 객체를 반환합니다. 이때 인수로 전달한 문자열은 `Date.parse` 메서드에 의해 해석 가능한 형식이어야 합니다.

```javascript
new Date("May 26, 2020, 10:00:00"); // Tue May 26 2020 10:00:00 GMT+0900 (대한민국 표준시)
new Date("2020/03/26/10:00:00"); // Thu Mar 26 2020 10:00:00 GMT+0900 (대한민국 표준시)
```

### 30.1.4 new Date(year, month[, day, hour, minute, second, milliscond])

`Date` 생성자 함수에 연, 월, 일, 시 분, 초 밀리초를 의미하는 숫자를 인수로 전달하면 지정된 날짜와 시간을 나타내는 `Date` 객체를 반환합니다. 이때 연, 월은 반드시 지정해야 합니다. 연, 월을 지정하지 않을 경우 1970년 1월 1일 00:00:00(UTC)을 나타내는 `Date` 객체를 반환합니다. 지정하지 않은 옵션 정보는 `0`또는 `1`로 초기화 됩니다. 인수는 다음과 같습니다.

| 인수       | 내용                                                                      |
| ---------- | ------------------------------------------------------------------------- |
| year       | 연을 나타내는 1900년 이후의 정수, 0부터 99는 1900부터 19999로 처리됩니다. |
| month      | 월을 나타내는 `0 ~ 11`까지의 정수(주의: 9부터 시작, 0 = 1월)              |
| day        | 일을 나타내는 `1 ~31`까지의 정수                                          |
| hour       | 시를 나타내는 `0 ~ 23`까지의 정수                                         |
| minute     | 분을 나타내는 `0 ~ 59`까지의 정수                                         |
| second     | 초를 나타내는 `0 ~ 59`까지의 정수                                         |
| millsecond | 밀리초를 나타내는 `0 ~ 999`까지의 정수                                    |

```javascript
// 2020/3/1/00:00:00:00
new Date(2020, 2);

// 2020/6/26/10:00:00:00
new Date(2020, 2, 26, 10, 0, 0, 0);
```

## 30.2 Date 메서드

### 30.2.1 Date.now

1970년 1월 1일 00:00:00(UTC)을 기점으로 현재 시간까지 경과한 밀리초를 숫자로 반환합니다.

```javascript
Date.now(); // 1593971539112
```

### 30.2.2 Date.parse

1970년 1월 1일 00:00:00(UTC)을 기점으로 진수로 전달된 지어 시간(`new Date(dateString)`의 인수와 동일한 형식)까지의 밀리초를 숫자로 변환합니다.

```javascript
Date.parse("Jan 2, 1970 00:00:00 UTC"); // 86400000
Date.parse("Jan 2, 1970 09:00:00"); // 86400000
Date.parse("1970/01/01/09:00:00"); // 86400000
```

### 30.2.3 Date.UTC

1970년 1월 1일 00:00:00(UTC)을 기점으로 인수로 전달된 지정 시간까지의 밀리초를 숫자로 변환합니다. `Date.UTC` 메서드는 `new Date(year, month[, dat, hour, minute, millisecond])`와 같은 형식의 인수를 사용해야 합니다. `Date.UTC` 메서드의 인수는 로캘 타임(KST)이 아닌 UTC로 인식 됩니다.

```javascript
Date.UTC(1970, 0, 2); // 86400000
Date.UTC("1970/1/2"); // NaN
```

### 30.2.4 Date.prototype.getFullYear

`Date` 객체의 연도를 나타내는 정수를 반환합니다.

```javascript
new Date("2020/07/24").getFullYear(); // 2020
```

### 30.2.5 Date.prototype.setFullYear

`Date` 객체에 연도를 나타내는 정수를 설정합니다. 연도 이외에 옵션으로 월, 일도 설정할 수 있습니다.

```javascript
const today = new Date();

today.setFullYear(2000);
console.log(today.getFullYear()); // 2020

today.setFullYear(1900, 0, 1);
console.log(today.getFullYear()); // 1900
```

### 30.2.6 Date.prototype.getMonth

`Date` 객체의 월을 나타내는 `0 ~ 11`의 정수를 반환합니다.

```javascript
new Date("2020/07/24").getMonth(); // 6
```

### 30.2.7 Date.prototype.setMonth

`Date` 객체에 월을 나타내는 `0 ~ 11`의 정수를 설정합니다. 월 이외에도 옵션으로 일도 설정할 수 있습니다.

```javascript
const today = new Date();

today.setMonth(0);
console.log(today.getMonth()); // 0

today.setMonth(11, 1);
console.log(today.getMonth()); // 11
```

### 30.2.8 Date.prototype.getDate

`Date` 객체의 날짜를 나타내는 `0 ~ 31`의 정수를 반환합니다.

```javascript
new Date("2020/07/24").getDate(); // 24
```

### 30.2.9 Date.prototype.setDate

`Date` 객체에 날짜를 나타내는 `0 ~ 31`의 정수를 설정합니다.

```javascript
const today = new Date();

today.setDate(1);
console.log(today.getDate()); // 1
```

### 30.2.10 Date.prototype.getDay

`Date` 객체의 요일을 나타내는 `0 ~ 6`의 정수를 반환합니다.

|  요일  | 반환값 |
| :----: | :----: |
| 일요일 |  `0`   |
| 월요일 |  `1`   |
| 화요일 |  `2`   |
| 수요일 |  `3`   |
| 목요일 |  `4`   |
| 금요일 |  `5`   |
| 토요일 |  `6`   |

```javascript
new Date("2020/07/24").getDay(); // 5
```

### 30.2.11 Date.prototype.getHours

`Date` 객체의 사간을 나타내는 `0 ~ 23`의 정수를 반환합니다.

```javascript
new Date("2020/07/24/12:00").getHours(); // 12
```

### 30.2.12 Date.prototype.setHours

`Date` 객체의 시간을 나타내는 `0 ~ 23`의 정수를 설정합니다. 시간 이외에 옵션으로 분, 초, 밀리초도 설정할 수 있습니다.

```javascript
const today = new Date();

today.setHours(7);
console.log(today.getHours()); // 7

today.setHours(0, 0, 0, 0);
console.log(today.getHours()); // 0
```

### 30.2.13 Date.prototype.getMinutes

`Date` 객체의 분을 나타내는 `0 ~59`의 정수를 반환합니다.

```javascript
new Date("2020/07/24/12:00").getMinutes(); // 30
```

### 30.2.14 Date.prototype.setMinutes

`Date` 객체의 분을 나타내는 `0 ~ 59`의 정수를 설정합니다. 분 이외에 옵션으로 초, 밀리초도 설정할 수 있습니다.

```javascript
const today = new Date();

today.setMinutes(50);
console.log(today.getMinutes()); // 50

today.setMinutes(5, 10, 999);
console.log(today.getMunutes()); // 5
```

### 30.2.15 Date.prototype.getSeconds

`Date` 객체의 초를 나타내는 `0 ~ 59`의 정수를 반환합니다.

```javascript
new Date("2020/07/24/12:30:10").getSeconds(); // 10
```

### 30.2.16 Date.prototype.setSeconds

`Date` 객체의 초를 나타내는 `0 ~ 59`의 정수를 설정합니다. 초 이외에 옵션으로 밀리초도 설정할 수 있습니다.

```javascript
const today = new Date();

today.setSeconds(30);
console.log(today.getSeconds()); // 30

today.setSeconds(10, 0);
console.log(today.getSeconds()); // 10
```

### 30.2.17 Date.prototype.getMilliseconds

`Date` 객체의 밀리초를 나타내는 `0 ~ 999`의 정수를 반환합니다.

```javascript
new Date("2020/07/24/12:30:10:150").getMilliseconds(); // 150
```

### 30.2.18 Date.prototype.setMilliseconds

`Date` 객체의 밀리초를 나타내는 `0 ~ 999`의 정수를 설정합니다.

```javascript
const today = new Date();

today.setMilliseconds(123);
console.log(today.getMilliseconds()); // 123
```

### 30.2.19 Date.prototype.getTime

1970년 1월 1일 00:00:00(UTC)를 기점으로 `Date` 객체의 시간까지 경과된 밀리초를 반환합니다.

```javascript
new Date("2020/07/24/12:30").getTime(); // 1595561400000
```

### 30.2.20 Date.prototype.setTime

`Date` 객체에 1970년 1월 1일 00:00:00(UTC)를 기점으로 경과된 밀리초를 설정합니다.

```javascript
const today = new Date();

today.setTime(86400000);
console.log(today); // Fri Jan 02 1970 09:00:00 GMT+0900 (대한민국 표준시)
```

### 30.2.21 Date.prototype.getTimezoneOffset

UTC와 `Date` 객체에 지정된 로캘 시간과의 차이를 분 단위로 반환합니다.

```javascript
const today = new Date();
console.log(today.getTimezonOffset() / 60); // -9;
```

### 30.2.22 Date.prototype.toDateString

사람이 읽을 수 있는 형식의 문자열로 `Date` 객체의 날짜를 반환합니다.

```javascript
const today = new Date("2020/7/24/12:30");

console.log(today.toString()); // Fri Jul 24 2020 12:30:00 GMT+0900 (대한민국 표준시)
console.log(today.toDateString()); // Fri Jul 24 2020
```

### 30.2.23 Date.prototype.toTimeString

사람이 읽을 수 있는 형식으로 `Date` 객체의 시간을 표현한 문자열을 반환합니다.

```javascript
const today = new Date("2020/7/24/12:30");

console.log(today.toString()); // Fri Jul 24 2020 12:30:00 GMT+0900 (대한민국 표준시)
console.log(today.toTimeString()); // 12:30:00 GMT+0900 (대한민국 표준시)
```

### 30.2.24 Date.prototype.toISOString

ISO 8601 형식으로 `Date` 객체의 날짜와 시간을 표현한 문자열을 반환합니다.

```javascript
const today = new Date("2020/7/24/12:30");

console.log(today.toString()); // Fri Jul 24 2020 12:30:00 GMT+0900 (대한민국 표준시)
console.log(today.toISOString()); // 2020-07-24T03:30:00.000Z
```

### Date.prototype.toLocaleString

인수로 전달한 로캘을 기준으로 `Date` 객체의 날짜와 시간을 표현한 문자열을 반환합니다. 인수를 생략한 경우 브라우저가 동작 중인 시스템의 로캘을 적용합니다.

```javascript
const today = new Date("2020/7/24/12:30");

console.log(today.toString()); // Fri Jul 24 2020 12:30:00 GMT+0900 (대한민국 표준시)
console.log(today.toLocaleString()); // 2020. 7. 24. 오후 12:30:00
console.log(today.toLocaleString("ko-KR")); // 2020. 7. 24. 오후 12:30:00
console.log(today.toLocaleString("en-US")); // 7/24/2020, 12:30:00 PM
console.log(today.toLocaleString("ja-JP")); // 2020/7/24 12:30:00
```

### 30.2.26 Date.prototype.toLocaleTimeString

인수로 전달한 로캘을 기준으로 `Date` 객체의 시간을 표현한 문자열을 반환합니다. 인수를 생략한 경우 브라우저가 동작 중인 시스템의 로캘을 적용합니다.

```javascript
const today = new Date("2020/7/24/12:30");

console.log(today.toString()); // Fri Jul 24 2020 12:30:00 GMT+0900 (대한민국 표준시)
console.log(today.toLocaleTimeString()); // 오후 12:30:00
console.log(today.toLocaleTimeString("ko-KR")); // 오후 12:30:00
console.log(today.toLocaleTimeString("en-US")); // 12:30:00 PM
console.log(today.toLocaleTimeString("ja-JP")); // 12:30:00
```

## 30.3 Date를 활용한 시계 예제

```javascript
const DAY_NAMES = [
  "(일요일)",
  "(월요일)",
  "(화요일)",
  "(수요일)",
  "(목요일)",
  "(금요일)",
  "(토요일)",
];

function printNow() {
  const today = new Date();

  const day = DAY_NAMES[today.getDay()];
  const year = today.getFullYear();
  const month = today.getMonth() + ``;
  const date = today.getDate();

  let hour = today.getHours();
  let minute = today.getMinutes();
  let second = today.getSeconds();
  const ampm = hour >= 12 ? "PM" : "AM";

  hour = hour % 12 || 12;
  minute = minute < 10 ? "0" + minute : minute;
  second = second < 10 ? "0" + second : second;

  const now = `${year}년 ${month}월 ${date}일 ${day} ${hour}:${minute}:${second} ${ampm}`;
  console.log(now);
}

printNow();
setInterval(printNow, 1000);
```
