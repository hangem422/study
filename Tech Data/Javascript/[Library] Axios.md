# React에서의 Ajax 요청

자바스크립트 라이브러리중 오직 AJAX 기능만을 위하여 만들어진 라이브러리들이 있습니다. 그 중 유명한 것들이 `axios`, `fetch`, `superagent`, `request` 등 입니다.

## 1. axios 설치

**axios**는 Promise 기반의 HTTP Client입니다. 장점은 다음과 같습니다.

- Promise 기반입니다.
- 클라이언트/서버에서 동일하게 작동합니다.
- 다양한 브라우저를 지원합니다.
- 편리합니다.

```bash
npm install --save axios
```

## 2. axios 사용하여 요청하기

### 2.1 GET 요청

```javascript
axios
  .get("/user?ID=12345")
  .then((response) => {
    console.log(response);
  })
  .catch((error) => {
    console.log(error);
  });
```

### 2.2 POST 요청

```javascript
axios
  .post("/user", {
    firstName: "Fred",
    lastName: "Flintstone",
  })
  .then((response) => {
    console.log(response);
  })
  .catch((error) => {
    console.log(error);
  });
```

위에 다룬 예제들과 같이 `axios.` 뒤에 `get`과 `post` 외에도 `delete`, `head`, `put`, `patch` 메소드를 뒤에 붙여 사용할 수 있습니다.

### 2.3 요청에 옵션을 설정할 때

```javascript
axios
  .get("/user", {
    params: {
      ID: 12345,
    },
  })
  .then((response) => {
    console.log(response);
  })
  .catch((error) => {
    console.log(error);
  });
```

### 2.4 메소드 타입을 옵션으로 지정

```javascript
axios({
  method: "post",
  url: "/user/12345",
  data: {
    firstName: "Fred",
    lastName: "Flintstone",
  },
});
```

### 2.5 Response 스키마 형식

```javascript
{
  // data는 서버에서 반환한 데이터입니다.
  data: {},

  // status는 서버에서 반환한 HTTP 상태입니다.
  status: 200,

  // statusText는 HTTP 상태 메시지입니다.
  statusText: 'OK',

  // header는 서버에서 반환한 헤더값입니다.
  headers: {},

  // config는 axios 요청시 전달했던 설정값입니다.
  config: {},
}
```

## 3. API 함수 모듈화

**axios**를 컴포넌트에서 불러와 바로 사용해도 되지만, 좀 더 코드를 정리해서 작성하기 위해 함수로 모듈화 할 수도 있습니다.

```javascript
import axios from "axios";

export function getPost(postId) {
  return axios.get(`https://jsonplaceholder.typicode.com/posts/${postId}`);
}

export function getComments(postId) {
  return axios.get(
    `https://jsonplaceholder.typicode.com/posts/${postId}/comments`
  );
}
```

## 4. 출처

- [[React.JS] 강좌: React 컴포넌트 구성 & AJAX 비동기 작업 처리하기 & CSS 애니메이션 처리 - VELOPERT.LOG](https://velopert.com/2597)
