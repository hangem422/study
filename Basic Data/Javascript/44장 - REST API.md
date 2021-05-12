# 44. REST API

**REST**(REpresentational State Transfer)는 HTTP/1.0과 1.1의 스펙 작성에 참여했고 어파치 HTTP 서버 프로젝트의 공동 설립자인 로이 필딩(Roy Fielding)의 2000년 논문에서 처음 소개되었습니다. 발표 당시의 웹이 HTTP를 제대로 사용하지 못하고 있는 상황을 보고 HTTP의 장점을 최대한 활용할 수 잇는 아키텍처로서 REST를 소개했고 이는 HTTP 프로토콜을 의도에 맞게 디자인하도록 유도하고 있습니다. REST의 기본 원칙을 성실히 지킨 서브스 디자인을 **RESTful**이라고 표현합니다.

즉, RESTS는 HTTP를 기반으로 클라이언트가 서버의 리소스에 접근하는 방식을 규정한 아키텍처고, REST API는 REST를 기반으로 서비스 API를 구현한 것을 의미합니다.

## 44.1 REST API의 구성

REST API는 **자원**(Resource), **행위**(Verb), **표현**(Representations)의 3가지 요소로 구성됩니다. REST는 **자체 표현 구조**(Self-de-scriptiveness)로 구성되어 REST API만으로 HTTP 요청의 내용을 이해할 수 있습니다.

| 구성 요소                 | 내용                           | 표현 방법        |
| ------------------------- | ------------------------------ | ---------------- |
| **자원**(Resouce)         | 자원                           | URI(엔드포인트)  |
| **행위**(Verb)            | 자원에 대한 행위               | HTTP 요청 메서드 |
| **표현**(Representations) | 자원에 대한 행위의 구체적 내용 | 페이로드         |

## 44.2 REST API 설계 원칙

REST에서 가장 중요한 기본적인 원칙은 두 가지입니다. URI는 리소스를 표현하는 데 집중하고 행위에 대한 정의는 HTTP 요청 메서드를 통해 하는 것이 RESTful API를 설계하는 중심 규칙입니다.

#### 1. URI는 리소스를 표현해야 합니다.

URI는 리소스를 표현하는 데 중점을 두어야 합니다. 리소스를 식별할 수 있는 이름은 동사보다는 명사를 사용합니다. 따라서 이름에 get 같은 행위에 대한 표현이 들어가서는 안됩니다.

```
# Bad
GET /getTodos/1
GET /todos/show/1

# Good
GET /todos/1
```

#### 2. 리소스에 대한 행위는 HTTP 요청 메서드로 표현합니다.

HTTP 요청 메서드는 클라이언트가 서버에게 요청의 종류와 목적(리소스에 대한 행위)을 알리는 방법입니다. 주로 5가지 요청 메서드(GET, POST, PUT, PATCH, DELETE 등)을 사용하여 CRUD를 구현합니다. 리소스에 대한 행위는 HTTP 요청 메서드를 통해 표현하며 URI에 표현하지 않습니다.

| HTTP 요청 메서드 | 종류           | 목적                  | 페이로드 |
| ---------------- | -------------- | --------------------- | :------: |
| GET              | index/retrieve | 모든/특정 리소스 취득 |    X     |
| POST             | create         | 리소스 생성           |    O     |
| PUT              | replace        | 리소스의 전체 교체    |    O     |
| PATCH            | modify         | 리소스의 일부 수정    |    O     |
| DELETE           | delete         | 모든/특정 리소스 삭제 |    X     |

```
# Bad
GET /todos/delete/1

# Good
DELETE /todos/1
```

## 44.3 JSON Server를 이용한 REST API 실습

HTTP 요청을 전송하고 응답을 받으려면 서버가 필요합니다. JSON Server를 사용해 가상 REST API 서버를 구축하여 HTTP 요청을 전송하고 응답을 받는 실습을 진행해봅니다.

### 44.3.1 JSON Server 설치

JSON Server는 json 파일을 사용하여 가상 REST API 서버를 구축할 수 있는 툴입니다. 사용법은 매우 간단합니다. 먼저 npm을 사용하여 JSON Server를 설치합니다.

```bash
$ mkdir json-server-exam && cd json-server-exam
$ npm init -y
$ npm install json-server --save-dev
```

### 44.3.2 db.json 파일 생성

프로젝트 루트 폴더 (`/json-server-exam`)에 다음과 같이 `db.json` 파일을 생성합니다. `db.json` 파일은 리소스를 제공하는 데이터베이스 역할을 합닙다.

```json
{
  "todos": [
    {
      "id": 1,
      "content": "HTML",
      "completed": true
    },
    {
      "id": 2,
      "content": "CSS",
      "completed": false
    },
    {
      "id": 3,
      "content": "JavaScript",
      "completed": true
    }
  ]
}
```

### 44.3.3 JSON Server 실행

터미널에서 다음과 같이 명령어를 입력하여 JSON Server를 실행합니다. JSON Server가 데이터베이스 역할을 하는 `db.json` 파일의 변경을 감지하게 하려면 `watch` 옵션을 추가합니다. 기본 포트는 3000입니다. 포트를 변경하려면 `port` 옵션을 추가합니다.

```bash
$ json-server --watch db.json --port 5000
```

위와 같이 매번 명령어를 입력하는 것이 번거로우니 `package.json` 파일의 `scripts`를 다음과 같이 수정하여 JSON Server를 실행하여 봅시다.

```json
{
  "name": "json-server-exam",
  "version": "1,0,0",
  "scripts": {
    "start": "json-server --watch dv.json"
  },
  "devDependencies": {
    "json-server": "^0.16.1"
  }
}
```

터미널에서 `npm start` 명령어를 입력하여 JSON Server를 실행합니다.

```bash
$ npm start
```

### 44.3.4 GET 요청

GET은 리소스를 취득할 때 사용합니다.

```javascript
const xhr = new XMLHttpRequest();

xhr.open("GET", "/todos/1");
xhr.send();

xhr.onload = () => {
  if (xhr.status === 200) console.log(xhr.response);
  else console.error("Error", xhr.status, xhr.statusText);
};
/*
{
  "id": 1,
  "content": "HTML",
  "completed": true
}
*/
```

### 44.3.5 POST 요청

POST는 새로운 리소스를 생성할 때 사용합니다. POST 요청 시에는 `setRequestHeader` 메서드를 사용하여 요청 몸체에 담아 서버로 전송할 페이로드의 MIME 타입을 지정해야 합니다.

```javascript
const xhr = new XMLHttpRequest();

xhr.open("POST", "/todos");
xhr.setRequestHeader("content-type", "application/json");
xhr.send(JSON.stringify({ id: 4, content: "Angular", completed: false }));

xhr.onload = () => {
  if (xhr.status === 200 || xhr.status === 201) console.log(xhr.response);
  else console.error("Error", xhr.status, xhr.statusText);
};
/*
{
  "id": 4,
  "content": "Angular",
  "completed": false
}
*/
```

### 44.3.6 PUT 요청

POST는 특정 리소스 전체를 교체할 때 사용합니다. PUT 요청 시에는 `setRequestHeader` 메서드를 사용하여 요청 몸체에 담아 서버로 전송할 페이로드의 MIME 타입을 지정해야 합니다.

```javascript
const xhr = new XMLHttpRequest();

xhr.open("PUT", "/todos/4");
xhr.setRequestHeader("content-type", "application/json");
xhr.send(JSON.stringify({ id: 4, content: "React", completed: true }));

xhr.onload = () => {
  if (xhr.status === 200) console.log(xhr.response);
  else console.error("Error", xhr.status, xhr.statusText);
};
/*
{
  "id": 4,
  "content": "React",
  "completed": true
}
*/
```

### 44.3.7 PATCH 요청

PATCH는 특정 리소스의 일부를 수정할 때 사용합니다. PATCH 요청 시에는 `setRequestHeader` 메서드를 사용하여 요청 몸체에 담아 서버로 전송할 페이로드의 MIME 타입을 지정해야 합니다.

```javascript
const xhr = new XMLHttpRequest();

xhr.open("PATCH", "/todos/4");
xhr.setRequestHeader("content-type", "application/json");
xhr.send(JSON.stringify({ completed: false }));

xhr.onload = () => {
  if (xhr.status === 200) console.log(xhr.response);
  else console.error("Error", xhr.status, xhr.statusText);
};
/*
{
  "id": 4,
  "content": "React",
  "completed": false
}
*/
```

### 44.3.8 DELETE 요청

DELETE는 리소스를 삭제할 때 사용합니다.

```javascript
const xhr = new XMLHttpRequest();

xhr.open("DELETE", "/todos/4");
xhr.send();

xhr.onload = () => {
  if (xhr.status === 200) console.log(xhr.response);
  else console.error("Error", xhr.status, xhr.statusText);
};
/*
{}
*/
```
