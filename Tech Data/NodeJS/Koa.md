# Koa

Koa는 Node.js v7 이상버전에서 사용되는 것이 권장됩니다. 그 이유는 `async/await`를 바벨을 통해 트랜스파일링하지 않아도 자체적으로 사용 할 수 있기 때문입니다.

## 1. 프로젝트 생성 및 ESLint 설정

### 1.1 프로젝트 생성

패키지 정보를 생성한 후, **koa**를 설치합니다.

```
$ npm init
$ npm install koa
```

### 1.2 ESLint 설정

**ESLint**를 글로벌 설치 후, 초기화 명령어를 실행합ㄴ디ㅏ. 이 명령어를 실행하면 몇가지 질문이 나옵니다.

```
$ npm install -g eslint
$ eslint --init
? How would you like to configure ESList? Use a popular style guide
? Which style guild do you want to follow? Standard
? What format do you want your config file to be in? JavaScript
```

## 2. Koa 기본 사용법

### 2.1 기본 포멧

src 디렉토리를 생성하여 index.js 파일을 만듭니다.

##### src/index.js

```javascript
const Koa = require("koa");
const app = new Koa();

app.use((ctx) => {
  ctx.body = "Hello Koa";
});

app.listen(4000, () => {
  console.log("Server is listening to port 4000");
});
```

다음과 같이 실행합니다.

```
$ node src
Server is listening to port 4000
```

### 2.2 미들웨어

**Koa** 어플리케이션은, 미들웨어의 배열로 구성되어있습니다. `app.use`라는 함수를 사용하여 미들웨어를 어플리케이션에 등록해줍니다.

```javascript
app.use((ctx) => {
  ctx.body = "Hello Koa";
});
```

**koa**의 미들웨어함수에서는, 두가지 파라미터를 받습니다. 첫째 `ctx`는, 웹 요청과, 응답에 대한 정보를 지니고 있고, `next`는 다음 미들웨어를 실행시키는 함수입니다. 만약에 미들웨어에서 next를 호출하지 않게 된다면, 그 부분에서 요청처리를 완료시키고 응답을 하게됩니다.

```javascript
app.use((ctx, next) => {
  console.log(1);
  next();
});
```

### 2.3 next()는 프로미스

`next()`를 실행하면 프로미스를 반환합ㄴ디ㅏ. 따라서, 작업들이 끝나고나서 할 작업들을 정해줄 수도 있습니다.

```javascript
app.use((ctx, next) => {
  console.log(1);
  const started = new Date();
  next().then(() => {
    console.log(new Date() - started + "ms");
  });
});
```

### 2.4 ansync/await 사용하기

**Koa**에서는 별도의 작업없이 `async/await`를 바로 사용 할 수 있습니다.

```javascript
app.use(async (ctx, next) => {
  console.log(1);
  const started = new Date();
  await next();
  console.log(new Date() - started + "ms");
});
```

## 3. Nodemon 사용하기

서버 코드를 변경 할 때마다 서버를 자동으로 재시작해줍니다.

```
$ npm install -g nodemon
& nodemon --watch src/ src/index.js
```

위 명령어는, `src/` 디렉토리에서 코드변화가 감지되면 재시작을 하다록 설정하고, `src/index.js`를 실행시켜줍니다.

## 4. koa-router 사용하기

`koa-router`는 요청이 들어왔을때 경로에 따라 다른 작업을 할 수 있게 해줍니다.

```
$ npm install --save koa-router
```

### 4.1 기본 사용법

```javascript
const Koa = require("koa");
const Router = require("kos-router");

const app = new Koa();
const router = new Router();

router.get("/", (ctx, next) => {
  ctx.body = "홈";
});

router.get("/:name", (ctx, next) => {
  const { name } = ctx.params;
  ctx.body = name + "의 소개";
});

router.get("/post", (ctx, next) => {
  const { id } = ctx.request.query;
  ctx.body = "포스트 #" + id;
});

app.use(router.routes()).use(router.allowedMethods());

app.listen(4000, () => {
  console.log("Server is listening to port 4000");
});
```

### 4.2 라우트 모듈화

##### src/api/sample/index.js

```javascript
const Router = require("koa-router");
const sample = new Router();

sample.get("/", (ctx, next) => {
  ctx.body = "GET " + ctx.request.path;
});

module.exports = books;
```

##### src/api/index.js

```javascript
const Router = require("koa-router");

const api = new Router();
const sample = require("./sample");

api.use("/sample", sample.routes());

module.exports = api;
```

##### src/index,js

```javascript
const Koa = require("koa");
const Router = require("koa-router");

const app = new Koa();
const router = new Router();
const api = require("./api");

router.use("/api", api.routes());

app.use(router.routes()).use(router.allowedMethods());

app.listen(4000, () => {
  console.log("Server is listening to port 4000");
});
```

### 4.3 여러 메소드 사용하기

REST API 에서는, 요청의 종류에 따라 다른 HTTP 메소드를 사용합니다.

- **GET:** 데이터를 가져올 때 사용합니다.
- **POST:** 데이터를 등록할 때 사용됩니다. 혹은, 인증작업을 거칠때도 사용됩니다.
- **DELETE:** 데이터를 지울 때 사용됩니다.
- **PUT:** 데이터를 교체 할 때 사용됩니다.
- **PATCH:** 데이터의 특정 필드를 수정 할 때 사용됩니다.

##### src/sample/index

```javascript
const Router = require("koa-router");

const books = new Router();

const handler = (ctx, next) => {
  ctx.body = `${ctx.request.method} ${ctx.request.path}`;
};

books.get("/", handler);

books.post("/", handler);

books.delete("/", handler);

books.put("/", handler);

books.patch("/", handler);

module.exports = books;
```

## 5. 출처

- [Koa 를 사용하여 웹서버 만들기 - Git Book](https://backend-intro.vlpt.us/1/)
