# Express 라우터와 async/await

## 1. async/await

ES8(ECMAScript 2017)에서는 제너레이터보다 간단하고 가독성 좋게 비동기 처리를 동기 처리처럼 동작하도록 구현할 수 있는 `async/await`가 도입되었습니다. `async/await`는 프로미스를 기반으로 동작합니다. `async/await`를 사용하면 프로미스 `then`, `catch`, `finally` 후속 처리 메서드에 콜백 함수를 전달해서 비동기 처리 결과를 후속 처리할 필요 없이 마치 동기 처리처럼 프로미스를 사용할 수 있습니다. 다시 말해, 프로미스의 후속 처리 메서드 없이 마치 동기 처리처럼 프로미스가 처리 결과를 반환하도록 구현할 수 있습니다.

### 1.1 async 함수

`await` 키워드는 반드시 `async` 함수 내부에서 사용해야 합니다. `async` 함수는 `async` 키워드를 사용해 정의하며 언제나 프로미스를 반환합니다. `asnyc` 함수가 명시적으로 프로미스를 반환하지 않더라도 `asnyc` 함수는 암묵적으로 반환값을 `resolve`하는 프로미스를 반환합니다.

```javascript
// async 함수 선언문
async function foo(n) {
  return n;
}
foo(1).then((v) => console.log(v)); // 1

// async 함수 표현식
const bar = async function (n) {
  return n;
};
bar(2).then((v) => console.log(v)); // 2

// async 화살표 함수
const baz = async (n) => n;
baz(3).then((v) => console.log(v)); // 3

// async 메서드
const obj = {
  async foo(n) {
    return n;
  },
};
obj.foo(n).then((v) => console.log(v)); // 4

// async 클래스 메서드
class MyClass {
  async bar(n) {
    return n;
  }
}
new MyClass().bar(5).then((v) => console.log(v)); // 5
```

클래스의 `constructor` 메서드는 `async` 메서드가 될 수 없습니다. 클래스의 `constructor` 메서드는 인스턴스를 반환해야 하지만 `async` 함수는 언제나 프로미스를 반환해야 합니다.

```javascript
class MyClass {
  async constructor() {
    // SyntaxError: Class constructor may not be an async method
  }
}

const myClass = new MyClass();
```

### 1.2 await 키워드

`await` 키워드는 프로미스가 `settled` 상태(비동기 처리가 수행된 상태)가 될 때까지 대기하다가 `settled` 상태가 되면 프로미스가 `resolve`한 처리 결과를 반환합니다. `await` 키워드는 반드시 프로미스 앞에서 사용해야 합니다.

```javascript
const fetch = require("node-fetch");

const getFithubUserName = async (id) => {
  const res = await fetch(`https://api.github.com/users/${id}`);
  const { name } = await res.json();
  console.log(nams);
};

getFithubUserName("ungmo2");
```

## 1.3 에러 처리

비동기 처리를 위한 콜백 패턴의 단점 중 가장 심각한 것은 에러 처리가 곤란하다는 것입니다. 에러는 **호출자**(Caller) 방향으로 전파됩니다. 즉, 콜 스택의 아래 방향(실행 중인 실행 컨텍스트가 푸시되기 직전에 푸시된 실행 컨텍스트 방향)으로 전파됩니다. 하지만 비동기 함수의 콜백 함수를 호출한 것은 비동기 함수가 아니기 때문에 `try ... catch`문을 사용해 에러르 캐치할 수 없습니다.

```javascript
try {
  setTimeout(() => {
    throw new Error("Error!");
  }, 3000);
} catch (e) {
  console.error(e);
}
```

`async/await`에서 에러 처리는 `try ... catch` 문을 사용할 수 있습니다. 콜백 함수를 인수로 전달받는 비동기 함수와는 달리 프로미스를 반환하는 비동기 하수는 명시적으로 호출할 수 있기 때문에 호출자가 명확합니다.

```javascript
const fetch = require("node-fetch");

const foo = async () => {
  try {
    const wrongUrl = "https://wrong.url";

    const response = await fetch(wrongUrl);
    const data = await response.json();
    console.log(data);
  } catch (e) {
    console.error(e); // TypeError: Failed to fetch
  }
};

foo();
```

`async` 함수 내에서 `catch` 문을 사용해서 에러 처리를 하지 않으면 `async` 함수는 발생한 에러를 `reject`하는 프로미스를 반환합니다. 따라서 `async` 함수는 호출하고 `Promise.prototype.catch` 후속 처리 메서드를 사용해 에러를 캐치할 수도 있습니다.

```javascript
const fetch = require("node-fetch");

const foo = async () => {
  const wrongUrl = "https://wrong.url";

  const response = await fetch(wrongUrl);
  const data = await response.json();

  return data;
};

foo().then(console.log).catch(console.error);
```

## 2. Express.js 에서의 오류 처리

Express.js에서는 라우팅 핸들러 함수에서 오류가 발생한 경우, 암묵적으로 미들웨어의 세번째 파라미터인 `next` 콜백함수에 `error`를 전달해서 마지막 에러 처리 미들웨어에서 받아 적절하게 처리할 수 있도록 하고 있습니다.

```javascript
app.use("/", (req, res) => {
  throw new Error();
});
```

하지만 `async` 메서드는 Promise를 리턴하기 떄문에, 위 경우와 다르게 `UnhandledPromiseRejectionWarning` 오류와 함꼐 나중에는 앱이 종료될 수 있다는 경고를 출력합니다.

### 2.1 데코레이터 패턴

`async` 함수가 던지는 오류를 `catch` 하려면 간단하게 `async` 함수 안에서 실행하고 그 안에서 발생한 오류를 적절하게 처리해주면 됩니다. 이 아이디어를 활용해서 `async` 라우팅 핸들러를 주입받아 작업을 추가한 새로운 라우팅 핸들러를 리턴하도록 합니다.

```javascript
const asyncWrapper = (fn) => async (req, res, next) =>
  await fn(req, res, next).catch(next);

async function routeHandler(req, res) {
  // ...
}

router.get("/", asyncWrapper(routeHandler));
```

핸들러 함수가 `async` 함수가 아닐 때엔 `catch` 메서드가 없을 수도 있으니 랩핑 함수의 로직을 좀더 안전하게 `try/catch`로 아래와 같이 바꿔줄 수 있습니다.

```javascript
function asyncWrapper(fn) {
  return async function (req, res, next) {
    try {
      await fn(req, res, next);
    } catch (err) {
      next(err);
    }
  };
}
```

### 2.2 또 다른 방법

런타임 상에서 생성한 `express` 인스턴스와 `router` 인스턴스의 라우팅 메서드 자체를 수정해버리는 것입니다. Express 내부적으로 하나의 핸들러 한수 처리 로직으로 `get`, `post`, `put`, `all` 등 수많은 HTTP 메소드 함수를 처리해주는, 반대로 `async` 라우팅 핸들러를 지원하게 만들기 위해 수많은 라우팅 메서드 함수들을 하나하나 다 `async` 함수를 처리해줄 수 있도록 변경해주어야 합니다. 비효율적인 것 같지만 이렇게 처리해주면 앞서 랩핑 함수를 사용하는 것과 달리 바로 라우팅 핸들러 함수에 `async` 함수를 사용해도 되어 소스가 깔끔해보일 수 있습니다.

```javascript
const express = require("express");
const asyncify = require("expree-asyncify");

const app = asyncify(express());

// ...

app.get("/", async (req, res) => {
  // ...
});
```

## 3. 출처

- 모던 자바스크립트 Deep Dive - 이웅모
- [Express.js 라우팅 핸들러에 async/await 을 적용할 수 있을까? - 감성 프로그래밍](https://programmingsummaries.tistory.com/399)
