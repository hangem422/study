# React 시작하기

## 1. Webpack

웹팩은 **모듈 번들러**로 알려져있습니다. 모듈 번들러는 여러 파일을 받아서 하나의 파일로 묶어줍니다. 모듈을 하나로 묶어서 얻는 두가지 이익은 **모듈성**과 **네트워크 성능**입니다.

- **모듈성:** 소스코드를 작업하기 쉽게 여러 부분 또는 모듈로 나눠서 다룰 수 있게 해줍니다.
- **네트워크 성능:** 모든 의존관계를 하나의 파일에 넣으면 단 한 번의 HTTP 요청으로 가져올 수 있으므로 추가 대기 시간을 방지할 수 있습니다.

트랜스파일링 외에 웹펙에서 처리할 수 있는 일은 다음과 같습니다.

- **코드 분리:** 코드를 여러 덩어리로 나눠서 필요할 떼 각각을 로딩할 수 있다. 이를 **롤업** 또는 **레이어**라고 부릅니다.
- **코드 축소:** 공백, 줄바꿈, 긴 변수 이름, 불필요한 코드 등을 없애서 파일 크기를 줄여줍니다.
- **특징 켜고 끄기:** 코드 기능을 테스트해야 하는 경우 코드를 각각의 환경에 맞춰 보내줍니다.
- **HMR:** 소스 코드가 바뀌었는지 감지하면서 변경된 모듈만 즉시 갱신해줍니다.

### 설치하기

`webpack` 명령은 누구나 호출할 수 있게 만들기 위해 웹팩 모듈을 `-g` 옵션을 줘서 **글로벌 설피**해야 합니다.

```bash
sudo npm install -g webpack
```

웹팩은 바벨을 사용해 JSX와 ES6 코드를 모든 브라우저에서 사용할 수 있는 자바스크립트 코드로 트랜스파일링 합니다. 바벨을 설치하면서 작업에 필요한 몇 가지 프리셋을 함께 설치합니다.

```bash
npm install babel-core babel-loader babel-preset-env babel-preset-react babel-preset-stage-0 --save-dev
```

### 설정하기

모듈화한 앱이 작동하게 만들려면 소스 코드를 어떻게 하나의 번들 파일로 만들 수 있는지 웹팩에 알려주어야 한다. 웹팩의 디폴트 설정 파일 이름은 항상 `webpack.config.js` 입니다. `webpack.config.js` 파일은 웹팩이 취해야 하는 동작을 기술하는 자바스크립트 리터럴 객체를 외부에 익스포트하는 단순한 모듈에 불과합니다. 프로젝트의 루트 폴더에 이 파일을 만들어야 합니다.

```javascript
module.export = {
  // 웹팩에 클라이언트 시작 파일을 지정합니다.
  entry: './src/index.js',
  // 번들을 출력할 이름과 장소를 지정해줍니다.
  output: {
    path: 'dist/assets',
    filename: 'bundle.js',
  },
  module: {
    // 웹팩으로 적용할 수 있는 다양한 유형의 로더를 처리해야 하기 때문에 배열입니다.
    rules: [
      {
        // 로더가 처리해야 하는 모듈의 파일 경로를 매치시켜주는 정규식입니다.
        test: /\.js$/,
        // 제외할 파일입니다.
        exclude: /(node_modules)/,
        // babel-loader는 실행 시 ES6와 리액트를 위한 프리셋을 사용합니다.
        loader: 'babel-loader',
        query: {
          prresets: ['env', 'stage-0', 'rreact'],
        },
      },
    ],
  },
}
```

### 소스 매핑

코드를 하나의 번들 파일로 만들먄 브라우저에서 앱을 디버깅할 때 약간 곤란해집니다. **소스 맵**은 번들과 원래 소스 파일을 연결해주는 파일입니다. `dev-tool`프로퍼티를 `#source-map`읋 설정하면 웹팻이 소스 매핑을 사용하게 됩니다. 소스 매핑을 사용하려면 `sourceMapFilename` 프로퍼티가 필요합니다.

```javascript
module.export = {
  //...
  output: {
    //...
    sourceMapFilename: 'bundle.map',
  }
  devtool: '#source-map',
  //...
};
```

이제 브라우저의 개발자 도구에 있는 **소스탭**에서 `webpack://`라고 쓰인 폴더를 찾을 수 있습니다. 그 안에는 번들에 들어 잇는 모든 소스 파일이 있습니다.

### 번들 최적화하기

번들 파일에 들어 잇는 텍스트의 양을 줄이면 파일 크기가 줄고 그에 따라 HTTP 전송을 통한 번들 로딩도 더 빨라집닏마. 자바스크립트 파일의 크기를 줄이는 작업을 **축소** 또는 **난독화**라고 합니다.

```javascript
// UglifyJsPlugin을 사용하려면 웹팩을 require해야 합니다.
var webpack = require('webpack');

module.exports = {
  //...
  plugins: [
    new webpack.optimize.UglifyJsPlugin({
      // 난독화하면 코드를 알아볼 수 없으므로 원본과 난독화 이후 코드를 연결해주는 소스 맵이 필요합니다.
      sourceMap: true,
      // export한 번들에서 콘솔 경고를 제거해줍니다.
      warnings: false,
      mangle: true,
    }),
  ],
};
```

## 2. Babel

자바스크립트는 **인터프리터** 언어라서 브라우적가 코드 텍스트를 해석하기 때문에 컴파일할 필요가 없다습니다. 하지만 모든 브라우저가 **ES6**나 **ES&** 문법을 지원하지 않습니다. 그리고 어떤 브라우저는 **JSX**도 지원하지 않습니다. 만약 우리가 이런 기능들 활용하고 싶다면 소스 코드를 브라우저각 해석할 수 있는 코드로 변롼해줄 수단이 필요합니다. 이런 변환 과정을 **트랜스파일링**이라 부르며 **바벨**이 그런 일을 해줍니다.

### babel-standalone

가장 간단한 방법은 **babel-standalone** 트랜스파일러에 대한 링크를 HTML에 포함시키는 것입니다. 바벨은 타입이 `text/babel`인 script 태그 안에 있는 모든 코드를 크랜스파일링 해줍니다. 바벨은 클라이언특가 script안의 코드를 수행하기 전에 트랭스파일링을 수행합니다. 프로덕션에서 사용할 수 있는 가장 좋은 방법은 아니지만 JSX를 처음 시작하는 데는 아주 좋은 방법입니다.

```html
<script src="https://unpkg.com/react@16/umd/react.development.js"></script>
<script src="https://unpkg.com/react-dom@16/umd/react-dom.development.js"></script>
<script src="https://unpkg.com/babel-standalone@6.15.0/babel.min.js"></script>
<script type="text/babel">
  // JSX 코드를 여기에 넣거나 별도의 자바스크립트 파일에 대한 링크를 script 태그에 넣습니다.
</script>
```

## 3. create-react-app

이름에서 알 수 있듯이, 리액트 앱을 만들어주는 도구입니다.

### 설치하기

```bash
# npm을 통해 설치
npm install -g create-react-app

# yarn을 통해 설치
yarn global add create-react-app
```

### 사용하기

```bash
create-react-app [Project Name]
```

## 4. 출처
- [VELOPERT.LOG](https://velopert.com/3621)
- Learnig React_한빛미디어