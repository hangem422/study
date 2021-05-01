# PostCSS 토스트 파일 적용

## 1. PostCSS 기능

PostCSS는 Autoprefixer를 만든 Andrey Sitnik가 시작한 프로젝트이며 자바 스크립트로 작성된 플러그인을 통해 CSS를 도와주는 도구입니다.

- 린트를 통해 코드 스타일을 제한할 수 있습니다.
- 마치 Sass처럼 변수, 믹스인을 CSS 내에서 사용할 수 있습니다.
- babel처럼 아직 지원하지 않는 새로운 구문들을 사용할 수 있게 트랜스파일 해줍니다.

PostCSS는 자체로 의미있는 것이 아니라 여러 플러그인을 함께 사용할 때 큰 힘을 발휘할 수 있습니다. `create-react-app`으로 프로젝트를 생성한 결과를 살펴보면 PostCSS 플러그인들을 기본으로 제공하는 것을 볼 수 있습니다.

```javascript
{
  loader: require.resolve('postcss-loader'),
  options: {
    indent: 'postcss',
    plugins: () => [
      require('postcss-flexbugs-fixes'),
      require('postcss-preset-env')({
        autoprefixer: {
          flexbox: 'no-2009',
        },
        stage: 3,
      }),
    ],
    sourceMap: isEnvProdiction && shouldUseSourceMap,
  },
}
```

### 1.1 Pre-Processor

| CSS example                  | vendor            |
| ---------------------------- | ----------------- |
| -webkit-border-radius: 10px; | 사파리, 크롬      |
| -moz-border-radius: 10px;    | 파이어폭스        |
| -o-border-radius: 10px;      | 오페라            |
| -ms-border-radius: 10px;     | 인터넷 익스플로러 |
| border-radius: 10px;         | 표준 속성         |

위와같이 prefix들과 이것이 필요한 CSS 요소들을 전부 외우기에는 너무 많습니다. 또한 순서도 신경써야 합니다. PostCSS는 이 수고를 덜어줍니다.

### 1.2 설치하기

```bash
npm install --save-dev postcss postcss-loader
```

### 1.3 설정하기

루트 경로 `postcee.config.js`에 기본적인 설정을 작성합니다. plugin은 순서에 영향을 받으므로 주의합니다. 순서에 영향을 받는 plugin들은 대부분 깃험의 README에 플러그인 순서를 명시하고 있습니다.

```javascript
module.exports = {
  parser: "postcss-scss", // 커스텀 파서를 사용할 지 설정할 수 있다. default값을 false이다.
  plugins: [requore("precss"), require("autoprefixer")],
};
```

### 1.4 Webpack 설정하기

`webpack.config.js`에 들어가 기존 scss 확장자들의 로더를 지정하는 부분의 설정을 바꿔줍니다. 추가적으로 `postcss-scss`는 SCSS를 CSS로 컴파일 해주지 않습니다. Sass를 사용할 것이라면 `sass-loader`를 거친 후 나온 결과에 `postcss-loader`를 사용하는 것이 좋습니다.

```javascript
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        exclude: /node_modules/,
        use: [
          {
            loader: "style-loader",
          },
          {
            loader: "css-loader",
            option: {
              importLoaders: 1,
            },
          },
          {
            loader: "postcss-loader",
          },
        ],
      },
    ],
  },
};
```

### 1.5 지원 브라워 제한

브라우저를 명시하지 않을 경우 `browserlist`에서 디폴트로 지정한 프라우저에 맞게 컴파일됩니다. 브라우저를 명시하고 싶다면 **.browserslistrc** 파일을 생성하여 작성해야 합니다.

## 2. 플러그인 소개

[PostCss 플러그인 리스트](https://github.com/postcss/postcss/blob/master/docs/plugins.md)

### Autoprefixer

`Autoprefixer`를 파싱할 때 자동으로 vendor별 prefix를 붙여주는 플러그인입니다. `Autoprefixer`는 ployfill이나 핵을 제공하는 것이 아니라 단순히 prefix가 붙는 것이기 때문에 필요하다면 다른 플러그인을 활용해야 합니다.

### postcss-flexbugs-fixes

`postcss-flexbugs-flexs`는 주로 IE10, 11에서 발생하는 flex 관련 버그가 일어날 부분들을 찾아 수정해줍니다. 예를 들어 `flex`의 축약형을 이용해서 단위가 없는 `flex-basis`는 무시 되는 버그가 E10, 11애서 존재합니다.

### PostCSS Preset Env

`PostCss Preset Env`는 미래에 추가될 것으로 논의되고 있는 css 기능들을 여러 브라우저에서 사용할 수 있게 변경해주는 플러그인이다. `Postcss-flexbug-fixes`와 같이 CRA에 기본으로 포함되어있는 플러그인이다.

### cssnano

`cssnano`는 css 작성 시 실수할 수 있는 요소들, 불필요하게 길게 작성하는 요소들을 정리해주는 플러그인입니다.

### Oldie

이름에서 느껴지는 것처럼 `Oldie`는 IE8 이하의 브라우저에서 작동하지 않는 css 요소들을 변경해주는 플러그인입니다. `calc`, `media`, `:root`, `:rbba` 등 IE8에서 사용할 수 없는 요소들을 변경해줍니다.

## 3. 출처

- [PostCSS 토스트파일 적용기 - Jung Han](https://medium.com/jung-han/postcss-%ED%86%A0%EC%8A%A4%ED%8A%B8%ED%8C%8C%EC%9D%BC-%EC%A0%81%EC%9A%A9%EA%B8%B0-86cd33ba6aa9)
- [github - postcss](https://github.com/postcss/postcss#options)
