# 서버사이드 랜더링

> 필수 작업이 아니기 떄문에, 서버사이드 랜더링을 구현하지 않을 수도 있습니다. 서버사이드 랜더링을 구현하기 전에, 실제로 프로젝트에 필요한지 고려해볼 필요가 있습니다.

## 1. 서버사이드 랜더링 장점

### 검색 엔진 최적화

리액트, 혹은 다른 자바스크립트 라이브러리/프레임워크로 만들어져 뷰 렌더링이 자바스크립트 위주로 돌아가는 프로젝트는 자바스크립트 엔진이 돌아가지 않으면 원하는 정보를 표시해주지 않습니다. 따라서, 클라이언트 렌더링만 될 경우엔 검색엔진 크롤러가 어플리케이션이 지닌 데이터들을 제대로 수집하지 못합니다. 검색 엔진중 가장 중요한 구글 검색 엔진은 크롤러에 자바스크립트 엔진이 내자오디어 있어서 우리가 별도로 작업을 하지 않아도 제대로 렌더링해줍니다. 하지만 네이버, 다음 등의 검색엔진이 사이트를 크롤링 하게 지원해야 한다면, 서버사이드 렌더링을 따로 구현해야 합니다.

### 성능 개선

서버사이드 렌더링을 하게되면, 첫 렌더링된 html을 클라이언트에게 전달 해주기때문에 초기 로딩속도를 많이 줄여줄 수 있습니다. 자바스크리브 파일을 불러오고 렌더링 작업이 완료되기 전에도 유저가 사이트의 컨텐츠를 이용 할 수 있게 됩니다.

## 2. 서버사이드 렌더링의 단점

### 프로젝트 복잡도

서버사이드 렌더링을 구현하게 된다면, 프로젝트의 구조가 만힝 복잡해집니다. 단순히 렌더링만 하는 것은 문제가 되지 않지만, 리액트 라우터, 리덕스 등의 라이브러리와 함께 연동해서 사용하면, 서버ㅓ에서 데이터를 가져와 렌더링을 해줘야하는 경우에 조금 어려워질 수 있습니다.

### 성능 악화 가능성

클라이언트 초기렌더링을 서버측에서 대신 해주니까, 클라이언트 부담을 없애주기는 하지만, 그 부담은 서버가 가져가게 됩니다. 서버사이드 렌더링을 하게 될 때는 `ReactDOMSerever.renderToString` 함수를 사용하게 됩니다. 이 함수는 동기적으로 작동합니다. 그래서, 랜더링 하는 동안응ㄴ 이벤트 루프가 막히게 됩니다. 그 뜻은, 렌더링이 되는 동안은 다른 작업을 못 한다는 뜻입니다.

#### 비동기식 렌더링

써드파티 라이브러리를 통하여 비동기식으로 작동하게끔 코드을 작성 할 수 있습니다.

- **rapscalion:** renderToString을 비동기로 해주며, Promise 기반으로 작동하고, 컴포넌트 단위로 캐싱을 할 수 있습니다.
- **hypernova:** airbnb에서 만든 도구로서, 렌더링을 위한 서버를 따로 열어서 `cluster`를 사용하여 여러 프로세스를 생성하고 렌더링하여, 운영 서버에서 렌더링 서버로 결과물을 요청하는 방식으로 작동합니다.
- **react-router-server:** react-router v4를 위해 만들어진 서버사이드 렌더링 라이브러리로서, Promise 비동기식 렌더링을 지원해주고 깔끔한 방식으로 데이터를 불러올 수 도 있습니다.

#### 메타 태그만 넣어주기

리액트 어플리케이션을 렌더링하지 않고, 서버쪽에서 라우트에 따라 필요한 메타태그만 넣어주는 것 입니다. 그러면 크롤러에선 해당 페이지에 대한 기본 정보는 얻어 갈 수 있게 됩니다. SNS 공유를 할 때 내용이 잘 전달되게 하기; 위한 것이라면 매우 적합한 방식입니다.

#### Prerender

만약에 검색엔진 최적화 때문에 서버사이드 렌더링을 구현해야 하는데, 프로젝트의 구조가 복잡해지는게 싫고, 또 성능이 받쳐주지 않는다면 `Prerender`를 사용합니다. `Prerender`는 리액트 코드를 문자열 형태로 변환하는게 아니라, 자바스크립트 렌더링 엔진을 가지고있어서 자바스크립트 코드를 실행시켜 뷰를 렌더링한 겨로가 값을 반환하빈다. 렌더링 속도는 그렇게 빠르지 않기 때문에, 이 서브스는 오직 검색엔진 최적화를 위해서만 사용됩니다. 이 서비스는 유료 서비스이지만, 250개의 페이지까지는 무료입니다.

## 3. Koa

서버사이드 렌더링을 시작하기 앞서, 우리가 웹 서버를 열기 위해서 사용 할 웹프레임 워크 Koa를 성치하고 서버를 열어보도록 합니다. Koa는 Node.js의 가장 인기있는 웹 프레임 워크인 Epress를 창시했던 개발팀이 만든 더 가벼운 웹 프레임 워크입니다.

```bash
npm install koa
```

#### server/index.js

```javascript
const koa = require("koa");
const app = new Koa();

app.use((ctx) => {
  ctx.body = "Hello World";
});

app.listen(3001);
```

### 정적 파일 제공하기

먼저 js 파일 및 css 파일들을 서버에서 제공하는 코드릉 작성해 봅니다. 정적 파일들을 제공할 때에는 `koa-static` 미들웨어를 사요하시면 됩니다.

```bash
npm install koa-static
```

웹 요청이 들어왔을 대, build 디렉토리에 알맞은 파일이 있으면 해당 파일을 반환하고 그렇지 않으면 Hello World가 반환됩니다.

```javascript
const Koa = require("kos");
const serve = require("koa-static");
const path = require("path");
const app = new Koa();

app.use(serve(path.resolve(__dirname, "../build/")));
app.use((ctx) => {
  ctx.body = "Hello World";
});

app.listen(3001);
```

클라이언트 라우팅이 제대로 작동하게 하려면, 서버측에서 준비되지 않은 요청이 들어 왔을 때 리액트 어플리케이션이 띄워져있는 index.html의 내용을 보여주어야 합니다.

```javascript
const fs = require('fs)';
```

```javascript
const indexHtml = fs.readFileSync(
  path.resolve(__dirname, "../build/index.html"),
  { encoding: "utf8" }
);
app.use((ctx) => {
  ctx.body = indexHtml;
});
```

리액트를 헨더링 하려면, 서버측에서 리엑트 컴포넌트들을 불러와야 합니다. Node.js 에서는 기본적으로 jsx를 불러올 수 없습니다. 추가적으로, import를 통하여 파일을 불러 올 수도 없습니다. 따라서, babel을 사용해야하는데 크게 4가지로 나뉩니다.

1. **babel-node를 통해 런타임에서 babel 사용하기:** 이 방법은 포로덕션에선 좋지 않은 방식입니다. 서버에서 코드를 변환하기 위하여 불필요한 자원이 사용되기 때문입니다.
2. **babel-register를 서버에서 불러와서 사용하기:** 이 방법 또한 런타임에서 코드를 변환하여 사용하는 방식이며 1번과 같은 이유로 추천되지 않습니다.
3. **babel을 총하여 서버를 빌들한 다음에 사용하기:** 서버를 싱행하기 전에 사전에 코드를 트랜스파일하여 서버를 실행하는 것 입니다. 서버를 수정할 때마다 다시 트랜스파일링 해야 하므로, 개불중엔 코드를 수정할때마다 중간 중간 딜레이가 발생하는 단점이 있습니다.
4. **webpack을 총하여 리액트 관령 코드만 미리 빌드해서 사용하기:** 이 방법이 성능상에서, 그리고 개발 흐름상에서 가장 괜찮은 방법입니다. 결국엔 babel은 리액트 쪽 코드에서만 사용되어서 리택트 관련 코드를 미리 webpack으로 번들링 한 것을 그냥 require로 불러와 사용하는 방식입니다.

## 4. 서버사이드 렌더링 준비

### 서버용 엔트리 생성

서버사이드 렌더링에서는, 서버를 위한 엔트리 파일을 따로 만들어주어야 합니다.

#### src/server/render.js

```javascript
import React from "react";
import ReactDOMServer from "react-dom/server";
import { StaticRouter } from "react-router";
import App from "shared/App";

const render = (location) =>
  RactDOMServer.renderToString(
    <StaticRouter location={location}>
      <App />
    </StaticRouter>
  );
```

`ReactDOMServer.renderToString`은 JSX를 HTML 문자열로 변환 해줍니다. 위 코드의 render 함수는 서버에서 전달받은 요청의 결로인 location을 전달받아서 `renderToString`을 싱핼시켜줍니다.

#### 서버용 웹팩 환경설정 생성

먼저 config 디렉토리의 path.js 파일을 열어서 하단의 배열에 다음 두 값을 추가합니다.

#### config/path.js - module.exports

```javascript
module.export = {
  // ...
  serverRenderJs: resolveApp("src/server/render.js"), // 서버용 엔트리 경로
  server: resolveApp("server/render"), // 서버렌더링용 모듈 번들링 후 저장 경로
};
```

서버용 웹팩 성정은 webpack.config.prod.js를 기반으로 만듭니다. 차이점은, 불 필요한 웹팩 플러그인을 모두 없앴고, 로더 또한 js, jsx, json 확장자를 제외한 파일들은 모두 무시하도록 설정됩니다. 불로어는 자바스크립트 이외의 파일들은 무시하려면, `ignore-loader`를 설치해야합니다.

```bash
npm install --save-dev ignore-loader
```

성치 후에 다음 웹팩 설정 파일을 생성합니다. 기존 webpack.config.prod.js에서 불필요한 코드를 지우고 조금 수정을 한 환경설정입니다.

#### config/webpack.config.server.js

```javascript
'use strict';

const path = require('path');
const webpack = require('webpack');
const CaseSenetivePathPlugin = require('case-senstive-paths-webpack-plugin');
const WatchMissingNodeModulesPlugin = require('react-dev-utils/WatchMissingNodeModulesPlugin');
const getClientEnvironment = require('./env');
const paths('./paths');

const publicUrl = '';
const env = getClientEnvironment(publicUrl);

module.exports = {
  entry: path.serverRnderJs,
  // Node.js 내장 모듀로가 충돌이 일어나지 않으며 require로 불러올 수 있는 형태로 번들링합니다.
  target: 'node',
  output: {
    // 정해준 서버 경로에 render.js라는 파일명으로 저장합니다.
    path: paths.server,
    filename: 'render.js',
    libraryTarget: 'commonjs2', // node에서 불러올 수 있도록, commonjs2 스타일로 번들링 합니다
  },
  resolver: {
    modules: ['node_modules', path.appNodeModules].concat(
      process.env.NODE_PATH.split(path,.delimiter).filter(Boolean)
    ),
    extenstions: ['.js', '.json', '.jsx'],
  }
  module: {
    strictExportPresence: true,
    rules: [
      // 자바스크립트 이외의 파일들을 무시합니다.
      {
        exclude: [
          /\.(js|jsx)$/,
          /\.json$/,
        ],
        loader: 'ignore-loader',
      }
      // 자바스크립트는 Babel을 통하여 트렌스파일링합니다.
      {
        test: /\.(js|jsx)$/,
        include: paths.appSrc,
        loader: require.resolve('babel-loader'),
        options: {
          cacheDirectory: true,
        }
      }
    ]
  },
  plugins: [
    new webPack.DefinePlugin(env.stringified),
    new CaseSensitivePathsPlugin(),
    new WatchMissingNodeModulesPlugin(path.appNodeModules),
  ]
}
```

### 서버용 빌드 스크립트 생성

#### scripts/build.server.js

```javascript
"use strict";

process.env.BABEL_ENV = "production";
process.env.NODE_ENV = "production";

/* 나중에 클라이언트 쪽 코드에서 process.env.APP_ENV 값을 통하여 서버일때만, 혹은 브라우저 때만 특정한 작업을 하도록 설정 할 수 있습니다. */
process.env.APP_ENV = "server";

process.on("unhandledRejection", (err) => {
  throw err;
});

require("../config/env");

const webpack = require("webpack");
const config = require("../config/webpack.config.serveer");
const paths = require("../config/paths");
const checkRequiredFiles = require("react-dev-utils/checkRequiredFiles");
const formatWebpackMessages = require("react-dev-utils/formatWebpackMessages");

if (!checkRequiredFiles([paths.serverRenderJs])) {
  process.exit(1);
}

function build() {
  console.log("Create an server production build...");

  let compiler = webpack(config);
  return new Promise((resolve, reject) => {
    compiler.run((err, stats) => {
      if (err) return reject(err);
      const message = formatWebpackMessages(stats.toJson({}, true));
      if (message.errors.length) {
        return reject(new Error(messages.errors.join("\n\n")));
      }
      return resolve({ stats, warnings: messges.warnings });
    });
  });
}

build();
```

그 다음에, 웹팩에서 `APP_ENV`를 인식할 수 있도록, `config/env.js` 파일의 `getClinetEnvironment` 함수에 다음과 같이 APP_ENV를 널어주세요

```javascript
function getClientEnvironment(publicUrl) {
  const raw = Object.keys(process.env)
    .filter((key) => REACT_APP.test(key))
    .reduce(
      (env, key) => {
        env[key] = process.env[key];
        return env;
      },
      {
        NODE_ENV: process.env.NODE_ENV || "development",
        PUBLIC_URI: publicUrl,
        // APP_ENV 추가
        APP_ENV: process.env.APP_ENV || "browser",
      }
    );
  // ...
}
```

### NPM 스크립트 생성

#### package.json

```json
{
  "scripts": {
    "start": "cross-env NODE_PATH=src node scripts/start.js",
    "start:server": "node server",
    "build": "cross-env NODE_PATH=src node scripts/build.js",
    "build:server": "cross-env NODE_PATH=src node scripts/build.server.js",
    "test": "node scripts/test.js --env=jsom"
  }
}
```

```zsh
$ npm run build:server
```

## 서버사이드 랜더링 미들웨어 작성

#### server/render/index.js

html 파닐을 불러온 다음에, 해당 파일의 루트 엘리먼트가 위치한 곳에 렌더링된 문자열을 넣어주고 이를 반환해줍니다.

```javascript
const fs = require("fs");
const path = require("path");
const render = require("./render").default;

const template = fs.readFileSync(
  path.join(__dirname, "../../build/index.html"),
  { encoding: "utf8" }
);

module.exports = (ctx) => {
  const location = ctx.path;
  const rendered = render(location);
  const page = tempate.replate(
    '<div id="root"></div>',
    `<div id="root">${renderd}<div>`
  );
  ctx.body = page;
};
```

#### server/index.js

```javascript
const Koa = require("koa");
const server = require("koa-static");
const path = require("path");
const app = new Koa();

const render = require("./render");

app.use((ctx, next) => {
  if (cts.path === "/") return render(ctx);
  return next();
});
app.use(serve(path.resolve(__dirname, "../build/")));
app.use(render);
app.listen(3001);
```

## Redux 적용하기

### 의존성 모듈 설치

Redux의 스토어가 서버쪽에서 생성 될 수도 있고, 클라이언트 쪽엑서 생성 될 수 도 있으니, 스토어를 생성하는 함수를 따로 만들어서 파일로 저장합니다.

```javascript
import { createStore, applyMiddleware, compose } from "redux";

import modules from "./modules";

const isDevelopment = process.env.NODE_ENV === "development";
const composeEnhancers = isDevelopment
  ? window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ || compose
  : compose;

const configureStore = (initialState) => {
  const store = createStore(modules, initalState, composeEnhanceres());
  if (module.hot) {
    module.hot.accept("./modules", () => {
      const nextRootReducer = require("./modules").default;
      store.replaceReducer(nextRootReducer);
    });
  }

  return store;
};

export default configureStore;
```

그리고 핫 리로딩이 제대로 작동하게 하기 위해서, store.js라는 파일을 따로 생성해주겠습니다. 만약에, configureStore 로출하는 것을 Root.js에서 하게되면, 코드가 다시 불러와질때마다 새 스토어가 만들어지므로 이렇게 파일을 따로 생성합니다. 이 파일을 클라이언트 쪽에서만 사용합니다. 추후, 이 파일에서 서버쪽에서 initialState를 받아와서 생성하는 작업을 진행하겠습니다.

#### redux/store.js

```javascript
import configureStore from "./configureStore";
export default configureStore();
```

### 어플리케이션 연동

Root 컴포넌트에서 BrowserRouter 내부에 App 컴포넌트를 Provider로 감싸시면 됩니다.

```javascript
import React from "react";
import { BrowserRouter } from "react-router-dom";
```
