# Babel & Webpack

크롬, 사파리, 파이어폭스, 엣지 같은 **에버그린 브라우저**(Evergreen browser)의 ES6 지원율은 약 98%로 거의 대부분의 ES6 사양을 지원합니다. 하지만 IE 11의 ES6 지원율은 11%입니다. 그리고 매년 새롭게 도입되는 ES6 이상의 버전(ES6+)과 제안 단계에 있는 ES 제안 사양(ES.NEXT)은 브라우저에 따라 지원율이 제각각입니다. 따라서 ES6+와 ES.NEXT의 최신 ECMAScript 사양을 사용하여 프로젝트를 진행하려면 최신 사양으로 작성된 코드를 경우에 따라 IE를 포함한 구형 브라우저에서 문제 없이 동작시키기 위한 개발 환경을 구축하는 것이 필요합니다.

또한 대부분의 프로젝트가 모듈을 사용하므로 모듈 로더도 필요합니다. ES6 모듈(ESM)은 대부분의 모던 브라우저(Chrome 61, FF 60, SF10.1, Edge 16 이상)에서 사용할 수 있습니다. 하지만 다음과 같은 이유로 아직까지는 ESM보다는 별도의 모듈 로더를 사용하는 것이 일반적입니다.

- IE를 포함한 구형 브라우저는 ESM을 지원하지 않습니다.
- ESM을 사용하더라도 트랜스파일링이나 번들링이 필요한 것은 변함이 없습니다.
- ESM이 아직 지원하지 않는 기능(Bare import 등)이 있고 점차 해결되고 있지만 아직 몇 가지 이슈가 존재합니다.

## 1. Babel

IE 같은 구형 브라우저에서는 ES6의 화살표 함수와 ES7의 지수 연산자를 지원하지 않을 수 있습니다. 이처럼 Babel은 ES^+/ES.NEXT로 구현된 최신 사양의 소스코드를 IE 같은 구형 브라우저에서도 동작하는 ES5 사양의 소스코드로 변환(트랜스파일링)할 수 있습니다.

### 1.1 Babel 설치

```bash
$ npm install --save-dev @babel/core @babel/cli
```

### 1.2 Babel 프리셋 설치와 babel.config.json 설정 파일 작성

Babel을 사용하려면 `@babel/preset-env`를 설치해야 합니다. `@babel/preset-env`는 함께 사용되어야 하는 Babel 플러그인을 모아 둔 것으로 Babel 프리셋이라고 부릅니다. Babel이 제공하는 공식 Babel이 제공하는 공식 Babel 프리셋은 다음과 같습니다.

- @babel/preset-env
- @babel/preset-flow
- @babel/preset-react
- @babel/preset-typescript

`@babel/preset-env`는 필요한 플러그인들을 프로젝트 지원 환경에 맞춰 동적으로 결정해 줍니다. 프로젝트 지원 환경은 Browserlist 형식으로 `.browserslistrc` 파일에 상세히 설정할 수 있습니다. 만약 프로젝트 지원 환경 설적 작업을 생략하면 기본값으로 설정됩니다.

```bash
$ npm install --save-dev @babel/preset-env
```

설치가 완료되면 프로젝트 루트 폴더에 `babel.config.json` 설정 파일을 생성하고 다음과 같이 작성합니다. 지금 설치한 `@bebel/preset-env`를 사용하겠다는 의미입니다.

```json
{
  "presets": ["@babel/preset-env"]
}
```

### 1.3 트랜스파일링

Babel을 사용하여 ES6+/ES.NEXT 사양의 소스 코드를 ES5 사양의 소스코드로 트랜스파일링해봅니다. Babel CLI 명령어를 사용할 수도 있으나 트랜스파일링할 때만다 매번 Babel CLI 명령어를 입력하는 것은 번거로우므로 `npm scripts`에 Babel CLI 명령어를 등록하여 사용합니다.

```json
{
  "scripts": {
    "build": "babel src/js -w -d dist/js"
  }
}
```

위 `npm scripts`의 `build`는 `src/js` 폴더(타깃 폴더)에 있는 모든 자바스크립트 파일들을 트랜스파일링한 후, 그 결과물을 `dist/js` 폴더에 저장합니다. 사용한 옵션의 의미는 다음과 같습니다.

- `-w`: 타깃 폴더에 있는 모든 자바스크립트 파일들의 변경을 감지하여 자동으로 트랜스파일합니다. (`--watch` 옵션의 축양형)
- `-d`: 트랜스파일링된 결과물이 저장될 폴더를 지정합니다. 만약 지정된 폴더가 존재하지 않으면 자동 생성합니다. (`--out-dir` 옵션의 축양형)

### 1.4 Babel 플러그인 설치

설치가 필요한 Babel 플러그인은 [Babel 홈페이지](https://babeljs.io)에서 검색할 수 있습니다. Babel 홈페이지 상단 메뉴의 **Search**란에 제안 사양의 이름을 입력하면 해당 플로그인을 검색할 수 있습니다. 여기서는 클래스 필드 정의 제안 플로그인을 검색하기 위해 `class field`를 입력해봅니다. 검색된 Babel 플러그인 중에서 public/private 클래스 필드를 지원하는 `@babel/plugin-proposal-class-properties`를 설치합니다.

```bash
$ npm install --save-dev- @babel/plugin-proposal-class-properties
```

설치한 플러그인은 `babel.config.json` 설정 파일에 추가해야 합니다.

```json
{
  "presets": ["@babel/preset-env"],
  "plugins": ["@babel/plugin-proposal-class-properties"]
}
```

### 1.5 브라우저에서 모듈 로딩 테스트

Babel이 모듈을 트랜스파일링한 것은 Node.js가 기본 지원하는 CommonJS 방식의 모듈 로딩 시스템에 따른 것ㅇ비니다. 브라우저는 CommonJS 방식의 `require` 함수를 지원하지 않으므로 트랜스파일링된 결과를 그대로 브라우저에서 실행하면 에러가 발생할 수 있습니다. 브라우저의 ES6 모듈(ESM)을 사용하도록 Babel을 설정할 수도 있으나 잎서 설명한 바와 같이 ESM을 사용하는 것은 분제가 있습니다. Webpack을 통해 이러한 문제를 해결해봅시다.

## 2. Webpack

Webpack은 의존 관계에 있는 자바스크립트, CSS , 이미지 등의 리소스들을 하나(또는 여러 개)의 파일로 번들링하는 모듈 번들러입니다. Webpack을 사용하면 의존 모듈이 하나로 번들링하므로 HTML 파일에서 `script` 태스로 여러 개의 자바스크립트 파일을 로드해야하는 번거로움도 사라집니다.

### 2.1 Webpack 설치

```bash
$ npm install --save-dev webpack webpack-cli
```

### 2.2 babel-loader 설치

Webpack이 모듈을 번들링할 떄 Babel을 사용하여 ES6+/ES.NEXT 사양의 소스코드를 ES5 사양의 소스코드로 트랜스파일링하도록 `babel-loader`를 설치합니다.

```bash
$ npm install --save-sev babel-loader
```

이제 `npm scripts`를 변경하여 Babel 대신 Webpack을 실행하도록 수정합니다.

```json
{
  "scripts": {
    "build": "webpack"
  }
}
```

### 2.3 webpack.config.js 설정 파일 작성

`webpack.config.js`는 Webpack이 실행될 때 참조하는 설정 파일입니다. 프로젝트 루트 폴더에 `webpack.config.js` 파일을 생성하고 다음과 같이 작성합니다.

```javascript
const path = require("path");

module.exports = {
  // entry file
  // https://webpack.js.org/confiuration/entry-context/#entry
  entry: "./src/js/main.js",
  // 번들링된 js 파일의 이름(filename)과 저장될 경로(path)를 지정
  // htts://webpack.js.org/configuration/output/#outputpath
  // htts://webpack.js.org/configuration/output/#outputfilename
  output: {
    path: path.resolve(__dirname, "dist/js"),
    filename: "bundle.js",
  },
  // https://webpack.js.org/configuration/module
  module: {
    rules: [
      {
        test: /\.js$/,
        include: [path.resolve(__dirname, "src.js")],
        exclude: /node_modules/,
        use: {
          loader: "babel-loader",
          options: {
            presets: [
              [
                "@babel/preset-env",
                {
                  targets: { node: "current" }, // 노드일 경우만
                  modules: "false",
                },
              ],
            ],
            plugins: ["@babel/plugin-proposal-class-properties"],
          },
        },
      },
    ],
  },
  devtool: "source-map",
  // https://webpack.js.org/configuration/mode
  mode: "development",
};
```

`env` 다음에 `tragets`과 `module: false`라는 옵션이 들어 있습니다. `modules`를 `false`로 해야 **트리 쉐이킹**이 됩니다. ES2015 모듚 시스템 `import`되지 않은 `export`들을 정리해주는 기능입니다. 단, commonJS나 AMD, UMD같은 모듈 시스템을 사용해야하는 클라이언트에서는 쓰면 제대로 처리되지 않습니다.

이제 Webpack을 실행하여 트랜스파일링 및 번들링을 실행해봅니다. 트랜스파일링은 Babel이 수행하고 번들링은 Webpack이 수행합니다.

```bash
$ npm run build
```

#### 2.3.1 entry

빌드할 파일을 알려주는 역할을 합니다. 아래와 같이 하면 `app.js`, `zero.js` 두 개가 생성됩니다. 결과물로 여러 JS를 만들고 싶을 때 이렇게 구현합니다. 보통 멀티 페이지 웹 사이트에서 entry를 여러 개 넣어줍니다.

```javascript
module.exports = {
  entry: {
    app: "파일 경로",
    zero: "파일 경로",
  },
};
```

하나의 entry에 여러 파일들을 넣고 싶을 대는 아래와 같이 배열을 사용합니다. `a.js`랑 `b.js`가 한 파일로 엮여 `app.js`라는 결과물로 나옵니다.

```javascript
module.exports = {
  entry: {
    app: ["a.js", "b.js"],
  },
};
```

js 파일 대신 npm 모듈들을 넣어도 됩니다. 보통 `@babel/polyfill`이나 `eventsource-polyfill` 같은 것들을 다음과 같이 적용합니다. 아래는 리액트에서 주로 사용하는 예시입니다. `app.js`와 `vendor.js`가 결과물로 나옵니다.

```javascript
module.exports = {
  entry: {
    vendor: ["@babel/polyfill", "eventsource-polyfill", "react", "react-dom"],
    app: ["@babel/polyfill", "eventsource-polyfill", "./client.js"],
  },
};
```

#### 2.3.2 output

```javascript
module.exports = {
  // ...
  output: {
    path: "/dist",
    filename: "[name].js",
    publicPath: "/",
  },
};
```

- **path:** output으로 나올 파일이 저장될 경로입니다.
- **publicPath:** 파일들이 위치할 서버 상의 경로입니다. Express로 비유하면 `express.static` 경로와 비슷한 겁니다.
- **filename:** `[name]`은 entry의 app이나 vendor가 들어가 결과물이 나옵니다. `[hash]`는 컴파일할 때마다 랜덤 문자열을 붙여줍니다. `[chunkhash]`는 파일이 달라질 때에만 랜덤 값이 바뀝니다.

#### 2.3.3 mode

웹팩 빌드 옵션입니다. `production`은 최적화되어 빌드되어지는 특징을 가지고 있고, `development`는 빠르게 빌드하는 특징, `none` 같은 경우는 아무 기능 없이 웹팩으로 빌드합니다.

#### 2.3.4 resolve

웹팩이 알아서 경로나 확장자를 처리할 수 있게 도와주는 옵션입니다. `modules`에 `node_modules`를 넣으셔야 디렉토리의 node_modules를 인식할 수 있습니다. 그리고 **extensions**에 넣은 확장자들은 웹팩에서 알아서 처리해주기 때문에 파일에 저 확장자들을 입력할 필요가 없어집니다.

#### 2.3.5 rule

test 정규식조건(js나 jsx 파일)에 부합하는 파일들을 **loader**에 지정한 로더가 컴파일해줍니다. **options는** 로더에 대한 옵션으로 아까 설치한 presets들을 적용하고 있는게 보입니다. **exclude**는 제외할 폴더나 파일로, 바벨로 컴파일하지 않을 것들을 지정해줍니다. 바벨로는 컴파일하지 않지만 웹팩으로는 컴파일합니다. 반대로 **include**로 꼭 이 로더를 사용해서 컴파일할 것들을 지정해줄 수도 있습니다.

### 2.4 babel-polyfill 설치

Babel을 사용하여 ES6+/ES.NEXT 사양의 소스코드를 ES5 사양의 소스코드로 트랜스파일링해도 브라우저가 지원하지 않는 코드가 남아 있을 수 있습니다. 예를 들어, ES6에서 추가된 `Promise`, `Object.assign`, `Array.from` 등은 ES5 사양으로 트랜스파일링해도 ES5 사양에 대체할 기능이 없기 때문에 트랜스파일링되지 못하고 그대로 남습니다. 따라서 IE 같은 구형 브라우저에도 이런 객체나 메서드를 사용하기 위해서는 `@babel/polyfill`을 설치해야 합니다.

```bash
$ npm install @babel/polyfill
```

`@babel-polyfill`은 개발 환경에서만 사용하는 것이 아니라 실제 운영 환경에서도 사용해야 합니다. 따라서 **개발용 의존성**(devDependencies)으로 설치하는 `--save-dev` 옵션을 지정하지 않습니다.

ES6의 `import`를 사용하는 경우에는 진입점의 선두에 먼저 폴리필을 로드하도록 합니다.

```javascript
import "@babel/polyfill";
```

Webpack을 사용하는 경우에는 위 방법 대신 `webpack.config.js` 파일의 `entry` 배열에 폴리필을 추가합니다.

```javascript
module.exports = {
  entry: ["@babel/polyfill", "./src/js/main.js"],
};
```

### 2.5 HTML 빌드하기

```bash
$ npm install --save-dev html-loader html-webpack-plugin
```

웹팩을 자바스크립트 파일뿐만 아니라 자바스크립트가 아닌 파일들도 모듈로 관리 할 수 있습니다. 웹팩에서는 **로더**(loader)라는 기능이 있습니다. 로더는 자바스크립트 파일이 아닌 파일을 웹팩이 이해할 수 있게 해줍니다. 로더를 사용하는 방법은 아래와 같습니다.

```
module: {
  reules: {
    test: '가지고올 파일 정규식',
    use: [
      {
        loader: '사용할 로더 이름',
        options: { 사용할 로더 옵션 }
      }
    ]
  }
}
```

`html-webpack-plugin`은 웹팩이 `html` 파일을 읽어서 html 파일을 빌드할 수 있게 해줍니다. 아래 있는 로더는 html 파일을 읽었을 때 `html-loader`를 실행하여 웹팻이 이해할 수 있게 하고 `options`으로는 `minimize`라는 코드 최적화 옵션을 사용하고 있습니다. 또한 `HtmlWebpackPlugin`은 웹팩 빌드시 `output`에 있는 `bundle.js`를 자동으로 import 합니다.

```javascript
const path = require("path");
const HtmlWebPackPlugin = require("html-webpack-plugin");

module.exports = {
  //...
  module: {
    rules: [
      {
        test: /\.html$/,
        use: [
          {
            loader: "html-loader",
            options: { minimize: true },
          },
        ],
      },
    ],
  },
  plugins: [
    new HtmlWebPackPlugin({
      template: "./public/index.html", // public/index.html 파일을 읽는다.
      filename: "index.html", // output으로 출력할 파일은 index.html이다.
    }),
  ],
};
```

### 2.6 React 빌드하기

```bash
$ npm install --save-dev @babel/core @babel/preset-env @babel/preset-react @babel/oreset-stage-0
```

```javascript
module.exports = {
  // ...
  module: {
    rules: [
      {
        test: /\.(js|jsx)$/,
        loader: "babel-loader",
        options: {
          presets: [
            [
              "@babel/preset-env",
              {
                targets: { node: "current" }, // 노드일 경우만
                modules: "false",
              },
            ],
            "@babel/preset-react",
            "@babel/oreset-stage-0",
          ],
        },
        exclude: ["/mode_modules"],
      },
      // ...
    ],
  },
  // ...
};
```

### 2.7 CSS 빌드하기

```bash
$ npm install --save-dev css-loader mini-css-extract-plugin style-loader
```

```javascript
const MiniCssExtractPlugin = require("mini-css-extract-plugin");

module.exports = {
  // ...
  module: {
    rules: [
      // ...
      {
        test: /\.css$/,
        use: [MiniCssExtractPlugin.loader, "css-loader"],
      },
    ],
  },
  plugins: [
    // ...
    new MiniCssExtractPlugin({
      filename: "style.css",
    }),
  ],
};
```

추가한 코드 중 `use: [MiniCssExtractPlugin.loader, 'css-loader']` 부분의 작동 순서는 오른쪽에서 왼쪽 순서로 실행 된다. 의미는 `css-loader`로 css 파일을 읽고 `MiniCssExtractPlugin.loader`로 읽은 css를 파일로 추출합니다. 파일이 아닌 style 태그로 만드려면 아래와 같이 만듭니다.

```javascript
module.exports = {
  //...
  modules: {
    rules: [
      // ...
      {
        test: /\.css$/,
        use: ["style-loader", "css-loader"],
      },
    ],
  },
};
```

참고로 서버 사이드에서 사용할 때는 `style-loader` 대신 `css-loader`에 옵션으로 `exportOnlyLocals`를 붙여줍니다. (`css-loader?exportOnlyLocals`)

### 2.8 SCSS 빌드하기

```bash
$ npm install --save-dev sass-loader
```

먼저 `sass-loader`로 scss 파일을 읽고 css로 변환한 후 `css-loader`로 css를 읽습니다. 그 후 `MinCssExtractPlugin`으로 읽은 CSS를 파일로 추출합니다.

```javascript
const path = require("path");
const HtmlWebPackPlugin = require("html-webpack-plugin");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");

module.exports = {
  // ...
  module: {
    rules: [
      // ...
      {
        test: /\.scss$/,
        use: [MiniCssExtractPlugin.loader, "css-loader", "sass-loader"],
      },
    ],
  },
  plugins: [
    // ...
    new MiniCssExtractPlugin({
      filename: "style.css",
    }),
  ],
};
```

### 2.9 기타 파일 빌드하기

```bash
$ npm install --save-dev file-loader url-loader
```

`file-loader`는 특정 파일을 그대로 내보내줍니다. 아무리 그래도 png랑 js랑 함치는건 무리가 있습니다. `url-loader`는 설정한 사이즈보다 작은 이미지나 폰트 파일을 base64로 인코딩하여 인라인화 합니다. `limit`보다 큰 파일은 알아서 `file-loader`가 처리하여 파일로 내보내줍니다.

```javascript
module.exports = {
  module: {
    rules: [
      // ...
      {
        test: /\.(ico|png|jpg|jpeg|gif|svg|woff|woff2|ttf|eot)(\?v=[0-9]\.[0-9]\.[0-9])?$/,
        loader: "url-loader",
        options: {
          name: "[hash].[ext]",
          limit: 10000,
        },
      },
    ],
  },
};
```

## 3. 웹팩 개발 서버 적용하기

지금까지는 소스코드를 수정할 때마다 웹팩으로 직접 빌드를 했으나 매번 하기에는 번거롭습니다. 이런 불편함을 해결하기 위해 소스코드를 수정하고 저장할 때마다 자동으로 웹팩이 빌드해주는 `webpack-dev-server`를 적용하려 합니다.

```bash
npm install --save-dev webpack-dev-server
```

```javascript
const path = require("path");
const HtmlWebPackPlugin = require("html-webpack-plugin");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");

module.exports = {
  // ...
  devServer: {
    content: "index.html",
    port: 9000,
  },
  // ...
};
```

이제 `npm scripts`를 변경하여 `webpack-dev-server`를 실행하도록 수정합니다.

```json
{
  "scripts": {
    "build": "webpack",
    "strat": "webpack-dev-server --hot"
  }
}
```

## 4. clean-wepack-plugin 적용하기

이번에는 **build** 디렉터리를 **clean-webpack-plugin**을 통해서 빌드될 때마다 안 쓰는 파일들을 삭제할 수 있도록 해봅니다.

```javascript
const { CleanWebpackPlugin } = require("clean-webpack-plugin");

module.exports = {
  // ...
  plugins: [
    new HtmlWebPackPlugin({
      template: "./public/index.html",
      filename: "index.html",
    }),
    new MiniCssExtractPlugin({
      filename: "style-test.css",
    }),
    new CleanWebpackPlugin(),
  ],
};
```

## 5. 출처

- 모던 자바스크립트 Deep Dive - 이웅모
- [velog.io/@hih0327](https://velog.io/@hih0327/Webpack-%EA%B8%B0%EC%B4%88)
- [ZeroCho Blog](https://www.zerocho.com/category/Webpack/post/58aa916d745ca90018e5301d)
