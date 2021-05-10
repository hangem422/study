# 43. Ajax

## 43.1 Ajax란?

**Ajax**(Asynchronous Javascript and XML)란 자바스크립트를 사용하여 브라우저가 서버에게 비동기 방식으로 데이터를 요청하고, 서버가 응답한 데이터를 수신하여 웹페이지를 동적으로 갱신하는 프로그래밍 방식을 말합니다. Ajax는 브라우저에게 제공하는 Web API인 `XMLHttpRequest` 객체를 기반으로 동작합니다. `XMLHttpRequest`는 HTTP 비동기 통신을 위한 메서드와 프로퍼티를 제공합니다.

1999년 마이크로소프트가 개발한 `XMLHttpRequest`는 그다지 큰 주목을 받지 못하다가 2005년 구글이 발표한 구글 맵스를 통해 웹 애플리케이션 개발 프로그래밍 언어로서 자바스크립트의 가능성을 확인하는 계기를 마련했습니다. 웹 브라우저에서 자바스크립트와 Ajax를 기반으로 동작하는 구글 맵스가 데스크톱 애플리케이션과 비교해 손색이 없을 정도의 퍼포먼스와 부드러원 화면 전환 효과를 보여준 것입니다.

![전통적인 웹페이지의 생명 주기](../_images/javascript4301.png)

이전의 웹페이지는 `html` 태그로 시작해서 `html` 태그로 끝나는 완전한 HTML을 서버로부터 전송받아 웹페이지 전체를 처음부터 다시 렌더링하는 방식으로 동작했습니다. 따라서 화면이 전환되면 서버로부터 새로운 HTML을 전송받아 웹페이지 전체를 처음부터 다시 렌더링했습니다. 이러한 전통적인 방식은 다음과 같은 단점이 있습니다.

1. 이전 웹페이지와 차이가 없어서 변경할 필요가 없는 부분까지 포함된 완전한 HTML을 서버로부터 매번 다시 전송받기 때문에 불필요한 데이터 통신이 발생했습니다.
2. 변경할 필요가 없는 부분까지 처음부터 다시 랜더링합니다. 이로 인해 화면 전환이 일어나면 화면이 순간적으로 깜빡이는 현상이 발생합니다.
3. 클라이언트와 서버와의 통신이 동기 방식으로 동작하기 때문에 서버로부터 응답이 있으 ㄹ때까지 다음 처리는 블로킹됩니다.

![Ajax](../_images/javascript4302.png)

Ajax의 등장은 이전의 전통적인 패러다임을 획기적으로 전환했습니다. 즉, 서버로부터 웹페이지의 변경에 필요한 데이터만 비동기 방식으로 전송받아 웹페이지를 변경할 필요가 없는 부분은 다시 렌더링하지 않고, 변경할 필요가 있는 부분만 한정적으로 렌더링하는 방식이 가능해진 것입니다. 이를 통해 브라우저에서도 데스크톱 애플리케이션과 유사한 빠른 퍼포먼스와 부드러운 화면 전환이 가능해졌습니다. Ajax는 전통적인 방식과 비교했을 때 다음과 같은 장점이 있습니다.

1. 변경할 부분을 갱신하는데 필요한 데이터만 서버로부터 전송받기 때문에 불필요한 데이터 통신이 발생하지 않습니다.
2. 변경할 필요가 없는 부분은 다시 렌더링하지 않습니다. 따라서 화면이 순간적으로 깜빡이는 현상이 발생하지 않습니다.
3. 클라이언트와 서버와의 통신이 비동기 방식으로 동작하기 때문에 서버에게 요청을 보낸 이후 블로킹이 발생하지 않습니다.

## 40.2 JSON

**JSON**(Javascript Object Notation)은 클라이언트와 서버 간의 HTTP 통신을 위한 텍스트 데이터 포맷입니다. 자바스크립트에 종속되지 않는 언어 독립형 데이터 포맷으로, 대부분의 프로그래밍 언어에서 사용할 수 있습니다.

### 43.2.1 JSON 표기 방식

JSON은 자바스크립트의 객체 리터럴과 유사하게 키와 값으로 구성된 순수한 텍스트입니다. JSON의 키는 반드시 큰따옴표(작은따옴표 사용 불가)로 묶어야 합니다. 값은 객체 리터럴과 같은 표기법을 그대로 사용할 수 있습니다. 하지만 문자열은 반드시 끈따옴표(작은따옴표 사용 불가)로 묶어야 합니다.

```json
{
  "name": "Lee",
  "age": 20,
  "alive": true,
  "hobby": ["traveling", "tennis"]
}
```

### 43.2.2 JSON.stringify

`JSON.stringify` 메서드는 객체를 JSON 포맷의 문자열로 변환합니다. 클라이언트가 서버로 객체를 전송하려면 객체를 문자열화해야 하는데 이를 **직렬화**(Serializing)라 합니다.

```javascript
const obj = {
  name: "Lee",
  age: 20,
  alive: true,
  hobby: ["traveling", "tennis"],
};

const json = JSON.stringify(obj);
console.log(typeof json, json); // string {"name":"Lee","age":20,"alive":true,"hobby":["trabeling","tennis"]}

// 객체를 JSON 포맷의 문자열로 변환하면서 들여쓰기 합니다.
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
};
*/

// 값의 타입이 Number이면 필터링되어 반환되지 않습니다.
const filter = (key, value) => (typeof value === "number" ? undefined : value);
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
};
*/
```

`JSON.stringify` 메서드는 객체뿐만 아니라 배열도 JSON 포맷의 문자열로 변환합니다.

```javascript
const todos = [
  { id: 1, content: "HTML", compoeted: false },
  { id: 2, content: "CSS", compoeted: true },
  { id: 3, content: "JavaScript", compoeted: false },
];

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
    "content": "JavaScript",
    "completed": false
  }
]
*/
```

### 43.2.3 JSON.parse

`JSON.parse` 메서드는 JSON 포맷의 문자열을 객체로 변환합니다. 서버로부터 클라이언트에게 전송된 JSON 데이터는 문자열입니다. 이 문자열을 객체로서 사용하려면 JSON 포멧의 문자열을 객체화해야 하는데 이를 **역직렬화**(Deserializing)라 합니다.

```javascript
const obj = {
  name: "Lee",
  age: 20,
  alive: true,
  hobby: ["traveling", "tennis"],
};

const json = JSON.stringify(obj);
const parsed = JSON.parse(json);
console.log(typeof parsed); // object
```

배열이 JSON 포맷의 문자열로 변환되어 있는 경우 `JSON.parse`는 문자열을 배열 객체로 변환합니다. 배열의 요소가 객체인 경우 배열의 요소까지 객체로 변환합니다.

```javascript
const todos = [
  { id: 1, content: "HTML", compoeted: false },
  { id: 2, content: "CSS", compoeted: true },
  { id: 3, content: "JavaScript", compoeted: false },
];

const json = JSON.stringify(todos, null, 2);
const parsed = JSON.parse(json);
console.log(typeof parsed); // object
```

## 43.3 XMLHttpRequest

브라우저는 주소창이나 HTMl의 `form` 태그 또는 `a` 태그를 통해 HTTP 요청 전송 기능을 기본 제공합니다. 자바스크립트를 사용하여 HTTP 요청을 전송하려면 `XMLHttpRequestr` 객체를 사용합니다. Web API인 `XMLHttpRequest` 객체는 HTTP 요청 전송과 HTTP 응답 수신을 위한 다양한 메서드와 프러퍼티를 제공합니다.

### 43.3.1 XMLHttpRequest 객체 생성

`XMLHttpRequest` 객체는 `XMLHttpRequest` 생성자 함수를 호출하여 생성합니다. `XMLHttpRequest` 객체는 브라우저에서 제공하는 Web API이므로 브라우저 환경에서만 정상적으로 실행됩니다.

```javascript
const xhr = new XMLHttpRequest();
```

### 43.3.2 XMLHttpRequest 객체의 프로퍼티 메서드

`XMLHttpRequest` 객체는 다양한 프로퍼티와 메서드를 제공합니다. 대표적인 프로퍼티와 메서드는 다음과 같습니다. 중요한 프로퍼티와 메서드는 볼드체로 표시했습니다.

#### XMLHttpRequest 객체의 프로토타입 프로퍼티

| 프로토타입 프로퍼티 | 설명                                                                                                             |
| ------------------- | ---------------------------------------------------------------------------------------------------------------- |
| **readyState**      | HTTP 요청의 현재 상태를 나타내는 정수 (`UNSENT: 0`, `OPENED: 1`, `HEADERS_RECEIVED: 2`, `LOADING: 3`, `DONE: 4`) |
| **status**          | HTTP 요청에 대한 응답 상태(HTTP 상태 코드)를 나타내는 정수                                                       |
| **statusText**      | HTTP 요청에 대한 응답 메시지를 나타내는 문자열                                                                   |
| **responseType**    | HTTP 응답 타입                                                                                                   |
| **resoponse**       | HTTP 요청에 대한 응답 **몸체**(Response Body). `responseType`에 따라 타입이 다릅니다.                            |
| responseText        | 서버가 전송한 HTTP 요청에 대한 응답 문자열                                                                       |

#### XMLHttpRequest 객체의 이벤트 핸들러 프로퍼티

| 이벤트 핸들러 프로퍼티 | 설명                                                         |
| ---------------------- | ------------------------------------------------------------ |
| **onreadystatechange** | readyState 프로퍼티 값이 변경된 경우                         |
| onloadstart            | HTTP 요청에 대한 응답을 받기 시작한 경우                     |
| onprogress             | HTTP 요청에 대한 응답을 받는 도중 주기적으로 발생            |
| onabort                | `abort` 메서드에 의해 HTTP 요청이 중단된 경우                |
| **onerror**            | HTTP 요청에 에러가 발생한 경우                               |
| **onload**             | HTTP 요청이 성공적으로 완료한 경우                           |
| ontimeout              | HTTP 요청 시간이 초과한 경우                                 |
| onloadend              | HTTP 요청이 완료한 경우. HTTP 요청이 성공 또는 실패하면 발생 |

#### XMLHttpRequest 객체의 메서드

| 메서드               | 설명                                     |
| -------------------- | ---------------------------------------- |
| **open**             | HTTP 요청 초기화                         |
| **send**             | HTTP 요청 전송                           |
| **abort**            | 이미 전송된 HTTP 요청 중단               |
| **setRequestHeader** | 특정 HTTP 요청 헤더의 값을 설정          |
| getResponseHeader    | 특정 HTTP 요청 헤더의 값을 문자열로 반환 |

#### XMLHttpRequest 객체의 정적 프로퍼티

| 정적 프로퍼티    | 값  | 설명                                  |
| ---------------- | --- | ------------------------------------- |
| UNSENT           | 0   | `open` 메서드 호출 이전               |
| OPEND            | 1   | `open` 메서드 호출 이후               |
| HEADERS_RECEIVED | 2   | `send` 메서드 호출 이후               |
| LOADING          | 3   | 서버 응답 중(응답 데이터 미완성 상태) |
| **DONE**         | 4   | 서버 응답 완료                        |

### 43.3.3 HTTP 요청 전송

HTTP 요청을 전송하는 경우 다음 순서를 따릅니다.

1. `XMLHttpRequest.prototype.open` 메서드로 HTPP 요청을 초기화합니다.
2. 필요에 따라 `XMLHttpRequest.prototype.setRequestHeader` 메서드로 특정 HTTP 요청의 헤더 값을 설정합니다.
3. `XMLHttpRequest.prototype.send` 메서드로 HTTP 요청을 전송합니다.

```javascript
const xhr = new XMLHttpRequest();

xhr.open("GET", "/user");
xhr.setRequestHeader("content-type", "application/json");
xhr.send();
```

#### XMLHttpRequest.prototype.open

`open` 메서드는 서버에 전송할 HTTP 요청을 초기화합니다. `open` 메서드를 호출하는 방법은 다음과 같습니다.

```
xhr.open(mehod, url[, async]);
```

| 매개변수 | 설명                                                                        |
| -------- | --------------------------------------------------------------------------- |
| method   | HTTP 요청 메서드("GET", "POST", "PUT", "DELETE" 등)                         |
| url      | HTTP 요청을 전송할 URL                                                      |
| async    | 비동기 요청 여부. 옵션으로 기본값은 `true`이며, 비동기 방식으로 동작합니다. |

HTTP 요청 메서드는 클라이언트가 서버에게 요청의 종류와 목적(리소스에 대한 행위)을 알리는 방법입니다. 주로 5가지 요청 메서드를 사용하여 CRUD를 구현합니다.

| HTTP 요청 메서드 | 종류           | 목적                  | 페이로드 |
| ---------------- | -------------- | --------------------- | :------: |
| GET              | index/retrieve | 모든/특정 리소스 취득 |    X     |
| POST             | create         | 리소스 생성           |    O     |
| PUT              | replace        | 리소스의 전체 교체    |    O     |
| PATCH            | modify         | 리소스의 일부 수정    |    O     |
| DELETE           | delete         | 모든/특정 리소스 삭제 |    X     |

#### XMLHttpRequest.prototype.send

`send` 메서드는 `open` 메서드로 초기화된 HTTP 요청을 서버에 전송합니다. 기본적으로 서버로 전송하는 데이터는 GET, POST, 요청 메서드에 따라 전송 방식의 차이가 있습니다.

- GET 요청 메서드의 경우 데이터를 URL의 일부분인 **쿼리 문자열**(Query String)로 서버에 전송합니다.
- POST 요청 메서드의 경우 데이터(페이로드)를 **요청 몸체**(Request body)에 담아 전송합니다.

![HTTP 요청/응답 메시지](../_images/javascript4303.png)

`send` 메서드에는 요청 몸체에 담아 전송할 데이터(페이로드)를 인수로 전달할 수 있습니다. 페이로드가 객체인 경우 반드시 `JSON.stringify` 메서드를 사용하여 직렬화한 다음 전달해야 합니다. HTTP 요청 메서드가 GET인 경우 `send` 메서드에 페이로드로 전달한 인수는 무시되고 요청 몸체는 `null`로 설정됩니다.

```javascript
xhr.send(JSON.stringify({ id: 1, content: "HTML", complete: false }));
```

#### XMLHttpRequest.prototype.setRequestHeader

`setRequestHeader` 메서드는 특정 HTTP 요청의 헤더 값을 설정합니다. `setRequestHeader` 메서드는 반드시 `open` 메서드를 호출한 이후에 호출해야 합니다. 자주 사용하는 HTTP 요청 헤더인 Content-type과 Accept에 대해 살펴봅시다. Content-type은 요청 몸체에 담아 전송할 데이터의 MIME 타입의 정보를 표현합니다. 또한 HTTP 클라이언트가 서버에 요청할 때 서버가 응답할 데이터의 MIME 타입을 Accept로 지정할 수 있습니다. 만약 Accept 헤더를 설정하지 않으면 `send` 메서드가 호출될 때 Accept 헤더가 */*으로 전송됩니다. 자주 사용되는 MIME 타입은 다음과 같습니다.

| MIME 타입   | 서브 타입                                                |
| ----------- | -------------------------------------------------------- |
| text        | `text/plain`, `text/html`, `text/css`, `text/javascript` |
| application | `application/json`, `application/x-www-form-urlencode`   |
| mulipart    | `multipart/formed-data`                                  |

```javascript
xhr.setRequestHeader("content-type", "application/json");
xhr.setRequestHeader("accept", "application/json");
```

### 43.3.4 HTTP 응답 처리

서버가 전송한 응답을 처리하려면 `XMLHttpRequest` 객체가 발생시키는 이벤트를 캐치해야 합니다. `XMLHttpRequest` 객체는 `onreadystatechange`, `onload`, `onerror` 같은 이벤트 핸들러 프로퍼티를 갖습니다. 이 이벤트 핸들러 프로퍼티 중에서 HTTP 요청의 현재 상태를 나타내는 `readyState` 프로퍼티 값이 변경된 경우 발생하는 `readystatechange` 이벤트를 캐치하여 다음과 같이 HTTP 응답을 처리할 수 있습니다.

```javascript
const xhr = new XMLHttpRequest();

xhr.open("GET", "https://jsonplaceholder.typicode.com/todos/1");
xhr.send();

xhr.onreadystatechange = () => {
  if (xhr.readyState !== XMLHttpRequest.DONE) return;

  if (xhr.status === 200) {
    console.log(JSON.parse(xhr.response));
  } else {
    console.error("Error", xhr.status, xhr.statusText);
  }
};
```

`readystatechange` 이벤트 대신 `load` 이벥트를 캐치해도 좋습니다. `load` 이벤트는 HTTP 요청이 성공적으로 완료된 경우 발생합니다. 따라서 `load` 이벤트를 캐치하는 경우 `xhr.readyState`가 `XMLHttpRequest.DONE`인지 확인할 필요가 없습니다.

```javascript
xhr.onload = () => {
  if (xhr.status === 200) {
    console.log(JSON.parse(xhr.response));
  } else {
    console.error("Error", xhr.status, xhr.statusText);
  }
};
```
