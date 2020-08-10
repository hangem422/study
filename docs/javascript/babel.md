# BabelJS

> ES2015+ 문법을 사용한 코드를 예전 ES5 자바스크립트 코드로 바꿔주는 도구들이 있습니다. 그 중 가장 유명한 도구가 바벨입니다.

```zsh
npm install --save-dev @babel/cli @babel/core @babel/preset-env
```

## .babelrc

**.babelrc**는 바벨에 대한 설정파일입니다. 여기에 `preset`이나 `plugin`을 연결하면 됩니다. `preset`은 여러 플러그인의 모음집입니다.

유명한 preset으로는 `react`나 `es2015`, `es2016`, `es2017`, `env`, `stage-0`, `stage-1`, `stage-2`, `stage-3` 등이 있습니다. 7버전부터 `stage-0`, `stage-1`, `stage-2`, `stage-3`은 deprecated되었습니다. 앞으로는 새로운 스펙은 프리셋 대신 플러그인 형식으로 넣어야 합니다. `react` 프리셋은 React 환경(jsx)을 위한 프리셋이고, `es2015`는 ES2015 문법을 사용할 수 있게 도와주는 플러그인들의 프리셋입니다. `env`는 한술 더 더서 특정한 문법 버전을 입력할 필요 없이, 타겟 브라우저를 입력하면 알아서 사용자가 환경에 맞춰 최신 EcmaScript를 사용할 수 있게 해줍니다.

```JSON
{
  "presets": [
    [ "@babel/preset-env", { "targets": { "browsers": [ "last 2 version", ">= 5% in KR" ] } } ]
  ]
}
```

만약 브라우저 대신 노드 환경에서 바벨을 쓰고 싶다면 `{targets: { node: 'current' } }` 등을 지정하면 됩니다. 그리고 package.json의 scripts에 바벨 컴파일을 추가합니다. 아래는 src 폴더에 있는 최신 자바스크립트 코드를 컴파일하여 결과를 lib 폴더에 넣으라는 뜻입니다.

```JSON
{
  "scripts": {
    "build": "babel src -d lib"
  }
}
```
