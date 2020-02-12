# PostCSS 토스트 파일 적용

> PostCSS는 Autoprerfixer를 만든 Andrey Sitnik가 시작한 프로젝트이며 자바 스크립트로 작성된 플러그인을 통해 CSS를 도와주는 도구입니다.

## PostCSS 기능

- 린트를 통해 코드 스타일을 제한할 수 있습니다.
- 마치 Sass처럼 변수, 믹스읜을 CSS내에서 사용할 수 있습니다.
- babel처럼 아직 지원하지 않는 새로운 구문들을 사용할 수 있께 트랜트파일 해줍니다.

**PostCSS**는 자체로 의미있는 것이 아니라 여러 플러그인을 함꼐 사용할 대 큰 힘을 바휘할 수 있습니다. `craete-react-app`으로 프로젝트를 생성한 결과를 살펴보면 **PostCSS** 플러그인들을 기본으로 제공하는 것을 볼 수 있습니다.

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

## Pre-processor

CSS example                  | vendor
-----------------------------|--------
-webkit-border-radius: 10px; | 사파리, 크롬
-moz-border-radius: 10px;    | 파이어폭스
-o-border-radius: 10px;      | 오페라
-ms-border-radius: 10px;     | 인터넷 익스플로러
border-radius: 10px;         | 표준 속성 

위와같은 **prefix**들과 이것이 필요한 CSS 요소들을 전부 외우기에는 너무 많습니다. 또한 순서도 신경써야 합니다. PostCSS는 이 수고를 덜어줍니다.

### 설치하기

```bash
npm install --save-dev postcss postcss-loader
```

### PostCSS 설정하기

루트 경로 `postcss.config.js`에 기본적인 설정을 작성합니다. **plugin**은 순서에 영향을 받으므로 주의합니다. 순서에 영향을 받는 **plugin**들은 대부분 깃헙의 `README`에 플러그인 순서를 명시하고 있습니다.

```javascript
module.exports = {
  plugins: [
    requore('precss'),
    require('autoprefixer'),
  ]
}
```

### webpack 설정하기

`webpack.config.js`에 들어가 기존 scss 확장자들의 로더를 지정하는 부분의 설정을 바꿔준다.

```javascript
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        exclude: /node_modules/,
        use: [
          {
            loader: 'style-loader',
          },
          {
            loader: 'css-loader',
            option: {
              importLoaders: 1,
            },
          },
          {
            loader: 'postcss-loader',
          },
        ],
      },
    ],
  },
};
```