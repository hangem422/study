# 49장. Babel과 Webpack을 이용한 ES6+/ES.NEXT 개발 환경 구축

크롬, 사파리, 파이어폭스, 엣지 같은 **에버그린 브라우저**(Evergreen browser)의 ES6 지원율은 약 98%로 거의 대부분의 ES6 사양을 지원합니다. 하지만 IE 11의 ES6 지원율은 11%입니다. 그리고 매년 새롭게 도입되는 ES6 이상의 버전(ES6+)과 제안 단계에 있는 ES 제안 사양(ES.NEXT)은 브라우저에 따라 지원율이 제각각입니다. 따라서 ES6+와 ES.NEXT의 최신 ECMAScript 사양을 사용하여 프로젝트를 진행하려면 최신 사양으로 작성된 코드를 경우에 따라 IE를 포함한 구형 브라우저에서 문제 없이 동작시키기 위한 개발 환경을 구축하는 것이 필요합니다.

또한 대부분의 프로젝트가 모듈을 사용하므로 모듈 로더도 필요합니다. ES6 모듈(ESM)은 대부분의 모던 브라우저(Chrome 61, FF 60, SF10.1, Edge 16 이상)에서 사용할 수 있습니다. 하지만 다음과 같은 이유로 아직까지는 ESM보다는 별도의 모듈 로더를 사용하는 것이 일반적입니다.

- IE를 포함한 구형 브라우저는 ESM을 지원하지 않습니다.
- ESM을 사용하더라도 트랜스파일링이나 번들링이 필요한 것은 변함이 없습니다.
- ESM이 아직 지원하지 않는 기능(Bare import 등)이 있고 점차 해결되고 있지만 아직 몇 가지 이슈가 존재합니다.

이번 장에서는 **트랜스파일러**인 Babel과 **모듈 번틀러**(Module Bundler)인 Webpack을 이용하여 ES6+/ES.NEXT 개발 환경을 구축해 봅니다. 이울러 Webpack을 통해 Babel을 로드하여 ES6+/ES.Next 사양의 소스코드를 IE 같은 구형 브라우저에서도 동작하도록 ES5 사양의 소스코드로 트랜스파일링하는 방법도 알아 볼 것입니다. 이 책에서 사용한 Node.js와 npm의 버전은 다음과 같습니다.

- Node.js: 14.3.0
- npm: 6.14.5

Babel, Webpack, 플러그인 버전은 다음과 같습니다.

| 패키지/플러그인  | 패키지 이름                             | 버전   |
| ---------------- | --------------------------------------- | ------ |
| Babel            | @babel/cli                              | 7.10.3 |
| Babel            | @babel/core                             | 7.10.3 |
| Babel 프리셋     | @babel/preset-env                       | 7.10.3 |
| Babel 플러그인   | @babel/plugin-proposal-class-properties | 7.10.1 |
| Babel 플러그인   | @babel/polyfill                         | 7.10.1 |
| Webpack          | webpack                                 | 4.43.0 |
| Webpack          | webpack-cli                             | 3.3.12 |
| Webpack 플러그인 | babel-loader                            | 8.1.0  |

## 49.1 Babel

IE 같은 구형 브라우저에서는 ES6의 화살표 함수와 ES7의 지수 연산자를 지원하지 않을 수 있습니다. 이처럼 Babel은 ES^+/ES.NEXT로 구현된 최신 사양의 소스코드를 IE 같은 구형 브라우저에서도 동작하는 ES5 사양의 소스코드로 변환(트랜스파일링)할 수 있습니다. Babel을 사용하기 위한 개발 환경을 구축해 봅시다.

### 49.1.1 Babel 설치

npm을 사용하여 Babel을 설치해 봅시다. 터미널에서 다음과 같이 명령어를 입력하여 Babel을 설치합니다.

```bash
$ npm install --save-dev @babel/core @babel/cli
```

참고로 Babel, Webpack, 플로그인의 버전은 빈번하게 업그레이드됩니다. `npm insatall`은 언제나 최신 버전의 패키지를 설치하므로 위 버전과 다른 최신 버전의 패키지가 설치될 수도 있습니다. 만약 위 버전 그대로 설치하고 싶다면 다음과 같이 패키지 이름 뒤에 `@`과 설치하고 싶은 버전을 지정합니다.

```bash
npm install --save-dev @babel/core@7.10.3 @babel/cli@7.10.3
```

### 49.1.2 Babel 프리셋 설치와 babel.config.json 설정 파일 작성

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

### 49.1.3 트랜스파일링

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

### 49.1.4 Babel 플러그인 설치

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

### 49.1.5 브라우저에서 모듈 로딩 테스트

Babel이 모듈을 트랜스파일링한 것은 Node.js가 기본 지원하는 CommonJS 방식의 모듈 로딩 시스템에 따른 것ㅇ비니다. 브라우저는 CommonJS 방식의 `require` 함수를 지원하지 않으므로 트랜스파일링된 결과를 그대로 브라우저에서 실행하면 에러가 발생할 수 있습니다. 브라우저의 ES6 모듈(ESM)을 사용하도록 Babel을 설정할 수도 있으나 잎서 설명한 바와 같이 ESM을 사용하는 것은 분제가 있습니다. Webpack을 통해 이러한 문제를 해결해봅시다.

## 49.2 Webpack

Webpack은 의존 관계에 있는 자바스크립트, CSS , 이미지 등의 리소스들을 하나(또는 여러 개)의 파일로 번들링하는 모듈 번들러입니다. Webpack을 사용하면 의존 모듈이 하나로 번들링하므로 HTML 파일에서 `script` 태스로 여러 개의 자바스크립트 파일을 로드해야하는 번거로움도 사라집니다.

### 49.2.1 Webpack 설치

터미널에서 다음과 같이 명령어를 입력하여 Webpack을 설치합니다.

```bash
$ npm install --save-dev webpack webpack-cli
```

### 49.2.2 babel-loader 설치

Webpack이 모듈을 번들링할 떄 Babel을 사용하여 ES6+/ES.NEXT 사양의 소스코드를 ES5 사양의 소스코드로 트랜스파일링하도록 `babel-loader`를 설치합니다.

```bash
$ npm install --save-sev babel-loader
```

이제 `npm scripts`를 변경하여 Babel 대신 Webpack을 실행하도록 수정합니다.

```json
{
  "scripts": {
    "build": "webpack -w"
  }
}
```

### 49.2.3 webpack.config.js 설정 파일 작성

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
            presets: ["@babel/preset-env"],
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

이제 Webpack을 실행하여 트랜스파일링 및 번들링을 실행해봅니다. 트랜스파일링은 Babel이 수행하고 번들링은 Webpack이 수행합니다.

```bash
$ npm run build
```

### 49.2.4 babel-polyfill 설치

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
