# Webpack 기초

> 모듈 번들러는 여러개의 자바스크립트 파일을 하나의 파일로 묶어서 한 번에 요청을 통해 가지고 올 수 있게 하고 최신 자바스크립트 문법을 브라우저에서 사용할 수 있게 해줍니다.

## 1. 프로젝트 설정하기

라이브러리를 설치합니다.

```zsh
$ yarn add -D webpack webpack-cli
```

웹팩 3까지는 `webpack`만 설치해ㅔ도 되었는데 웹팩 4부터는 `webpack-cli`를 같이 설치해야 커맨드라인에 **webpack**이란 명령어를 사용할 수 있습니다.

## 2. 웹팩으로 자바스크림트 파일 빌드하기

파일명이 `webpack.config.js`여야 웹팩이 바로 인식합니다. 이름을 다르게 하고싶다면 (예를들어 webpack.config.prod.js) 명령 프롬프트에서 실행할 때 `webpack --config webpack.config.prod.js`라고 `--config` 플래그를 사용해 경로를 알려주면 됩니다.

###### ./webpack.config.js

```javascript
const path = require("path");

module.exports = {
  entry: "./src/test.js",
  output: {
    filename: "bundle.js",
    path: path.resolve(__dirname + "/build"),
  },
  mode: "none",
};
```

###### ./package.json

```json
{
  "script": {
    "build": "webpack"
  }
}
```

이렇게하면 `src/test.js` 파일 기준으로 `import`되어 있는 모든 파일드를 찾아 하나의 파일로 합치게 됩니다.

#### entry

빌드할 파일을 알려주는 역할을 합니다. 아래와 같이 하면 app.ja, zero.js 두 개가 생성됩니다. 결과물로 여러 JS를 만들고 싶을 때 이렇게 구현합니다. 보통 멀티 페이지 웹 사이트에서 entry를 여러 개 넣어줍니다.

```javascript
module.exports = {
  entry: {
    app: "파일 경로",
    zero: "파일 경로",
  },
};
```

하나의 entry에 여러 파일들을 넣고 싶을 대는 아래와 같이 배열을 사용합니다. a.js랑 b.js가 한 파일로 엮여 app.js라는 결과물로 나옵니다.

```javascript
module.exports = {
  entry: {
    app: ["a.js", "b.js"],
  },
};
```

js 파일 대신 npm 모듈들을 넣어도 됩니다. 보통 `@babel/polyfill`이나 `eventsource-polyfill` 같은 것들을 다음과 같이 적용합니다. 아래는 리액트에서 주로 사용하는 예시입니다. app.js와 vendor.js가 결과물로 나옵니다.

```javascript
module.exports = {
  entry: {
    vendor: ["@babel/polyfill", "eventsource-polyfill", "react", "react-dom"],
    app: ["@babel/polyfill", "eventsource-polyfill", "./client.js"],
  },
};
```

#### output

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
- **publicPath:** 파일들이 위치할 서버 상의 경로입니다. Express로 비유하면 `express.static` 경로와 비슷한 겁ㄴ디ㅏ.
- **filename:** `[name]`은 entry의 app이나 vendor가 들어가 결과물이 나옵니다. `[hash]`는 컴파일할 때마다 랜덤 문자열을 붙여줍니다. `[chunkhash]`는 파일이 달라질 때에만 랜덤 값이 바뀝니다.

#### mode

웹팩 빌드 옵션입니다. `production`은 최적화되어 빌드되어지는 특징을 가지고 있고, `development`는 빠르게 빌드하는 특징, `none` 같은 경우는 아무 기능 없이 웹팩으로 빌드합니다.

#### resolve

웹팩이 알아서 경로나 확장자를 처리할 수 있게 도와주는 옵션입니다. `modules`에 node_modules를 넣으셔야 디렉토리의 node_modules를 인식할 수 있습니다. 그리고 **extensions**에 넣은 확장자들은 웹팩에서 알아서 처리해주기 때문에 파일에 저 확장자들을 입력할 필요가 없어집니다.

## 3. 웹팩으로 HTML 파일 빌드하기

```zsh
$ yarn add -D html-loader html-webpack-plugin
```

웹팩을 자바스크립트 파일뿐만 아니라 자바스크립트가 아닌 파일들도 모듈로 관리 할 수 있습니다. 웹팩에서는 로더(loader)라는 기능이 있습니다. 로더는 자바스크립트 파일이 아닌 파일을 웹팩이 이해할 수 있게 해줍니다. 로더를 사용하는 방법은 아래와 같습니다.

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

###### ./webpack.config.js

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

`html-webpack-plugin`은 웹팩이 `html` 파일을 읽어서 html 파일을 빌드할 수 있게 해줍니다.

위에 있는 로더는 html 파일을 읽었을 때 `html-loader`를 실행하여 웹팻이 이해할 수 있게 하고 `options`으로는 `minimize`라는 코드 최적화 옵션을 사용하고 있습니다. 또한 `HtmlWebpackPlugin`은 웹팩 빌드시 `output`에 있는 `bundle.js`를 자동으로 import 합니다.

## 4. 웹팩으로 리액트 빌드하기

```zsh
$ yarn add -D @babel/core @babel/preset-env @babel/preset-react babel-loader @babel/oreset-stage-0
```

바벨(babel)은 ES6에서 ES5로 자바스크립트를 변환해주는 역할을 합니다. 아래 내용은 바벨이 ES6와 릭액트를 ES5로 변환할 수 있게 해준다는 내용입니다. `babel-loader`와 `babel-core`는 필수입니다. 나머지 preset들은 선택입니다. react는 react를 사용할 때, env는 브라우저에 필요한 ecmascript 버전을 자동으로 파악해서 알아서 polyfill을 넣어줍니다.

###### ./webpack.config.js

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

`env` 다음에 `tragets`과 `module: false`라는 옵션이 들어 있습니다. `modules`를 `false`로 해야 **트리 쉐이킹**이 됩ㄴ디ㅏ. ES2015 모듚 시스템 `import`되지 않은 `export`들을 정리해주는 기능입니다. 단, commonJS나 AMD, UMD같은 모듈 시스템을 사용해야하는 클라이언트에서는 쓰면 제대로 처리되지 않습니다.

위와 같이하면 test 정규식조건(js나 jsx 파일)에 부합하는 파일들을 **loader에** 지정한 로더가 컴파일해줍니다. **options는** 로더에 대한 옵션으로 아까 설치한 presets들을 적용하고 있는게 보입니다. **exclude**는 제외할 폴더나 파일로, 바벨로 컴파일하지 않을 것들을 지정해줍니다. 바벨로는 컴파일하지 않지만 웹팩으로는 컴파일합니다. 반대로 **include**로 꼭 이 로더를 사용해서 컴파일할 것들을 지정해줄 수도 있습니다.

## 5. 웹팩에서 CSS 사용하기

```zsh
$ yarn add -D css-loader mini-css-extract-plugin style-loader
```

###### ./webpack.config.js

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

추가한 코드 중 `use: [MiniCssExtractPlugin.loader, 'css-loader']` 부분의 작동 순서는 오른쪽에서 왼쪽 순서로 실행 된다. 의미는 **css-loader**로 css 파일을 읽고 **MiniCssExtractPlugin.loader**로 읽은 css를 파일로 추출합니다. 파일이 아닌 style 태그로 만드려면 아래와 같이 만듭니다.

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

## 6. 웹팩에서 SCSS 사용하기

```zsh
$ yarn add -D sass-loader
```

###### ./webpack.config.js

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

먼저 **sass-loader**로 scss 파일을 읽고 css로 변환한 후 **css-loader**로 css를 읽습니다. 그 후 **MinCssExtractPlugin**으로 읽은 CSS를 파일로 추출합니다.

## 7. 웹팩에서 기타 파일 적용하기

```zsh
$ yarn add -D file-loader url-loader
```

**file-loader**는 특정 파일을 그대로 내보내줍니다. 아무리 그래도 png랑 js랑 함치는건 무리가 있습니다. **url-loader**는 설정한 사이즈보다 작은 이미지나 폰트 파일을 base64로 인코딩하여 인라인화 합니다. `limit`보다 큰 파일은 알아서 `file-loader`가 처리하여 파일로 내보내줍니다.

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

## 7. 웹팩 개발 서버 적용하기

지금까지는 소스코드를 수정할 때마다 웹팩으로 직접 빌드를 했으나 매번 하기에는 번거롭습니다. 이런 불편함을 해결하기 위해 소스코드를 수정하고 저장할 때마다 자동으로 웹팩이 빌드해주는 **webpack-dev-server**를 적용하려 합니다.

###### ./webpack.config.js

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

###### ./package.json

```json
{
  "scripts": {
    "build": "webpack",
    "strat": "webpack-dev-server --hot"
  }
}
```

## 8. clean-wepack-plugin 적용하기

이번에는 **build** 디렉터리를 **clean-webpack-plugin**을 통해서 빌드될 때마다 안 쓰는 파일들을 삭제할 수 있도록 해봅니다.

###### ./webpack.config.js

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

## 9. 웹팩 빌드 모드 나누기

웹팩을 빌드 모드가 **Development**와 **Production**에 따라 차이가 있다.

- **Development:** 빠르게 빌드하기 위해 빌드할 때 최적화를 안 하는 특징
- **Production:** 빌드할 떄 최적화 작업을 함

## 10. optimization

웹팩 4에서 최적화 관련 프러그인들이 모두 이쪽 속성으로 통합되었습니다. 예를 들자면 `minimize`가 `Uglify JsPlugin`을 계승하고, `splitChunk`가 `CommonsChunkPlugin`을 계승합니다. 또한 mode가 `production`일 때 자동으로 이 두 송성이 켜집니다.

```javascript
module.exports = {
  optimization: {
    minimize: true,
    splitChunk: {},
    concatenateModules: true,
  },
};
```

## 11. 출처

- [velog.io/@hih0327](https://velog.io/@hih0327/Webpack-%EA%B8%B0%EC%B4%88)
- [ZeroCho Blog](https://www.zerocho.com/category/Webpack/post/58aa916d745ca90018e5301d)
