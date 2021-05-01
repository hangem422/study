# Rollup

webpack을 쓸 수도 있는데 rollup을 쓰는 이유는 webpack은 ES Module 형태로 번들을 할 수 없습니다. webpack을 사용 할 때에는 일반적으로 commonjs 형태로 번들링을 하게 되는데, commonjs로 번들링한 라이브러리를 나중에 다른 프로젝트에서 사용하게 되면 **Tree-shaking**이 지원되지 않습니다.

## 1. Rollup으로 라이브러리 번들링하기

### 1.1 패키지 설치하기

- **rollup**
- **rollup-plugin-typescript2:** TypeScript 플러그인 입니다. 리액트 사용시 필요 업습니다.
- **rollup-plugin-babel:** rollup에서 babel을 사용 할 수 있게 해주는 플로그인입니다.
- **rollup-plugin-node-resolve:** node_modules에서 써드파티 모듈을 사용하는 용도로 사용하며, js 이외의 확장자 (ts, tdsx) 파일을 불러오기 위해서도 이 플러그인을 필요로 합니다.
- **rollup-plugin-peer-deps-external:** peerDependency로 설치ㅣ돈 라이브러리의 코드가 번들링된 결과에 포함되지 않고, import 구문으로 불러와서 사용할 수 있게 만들어줍니다.
- **rollup-plugin-commonjs:** CommonJS 형태로 이루어진 모듈의 코드를 ES6로 변환하여 결과물에 포함될 수 있게 해줍니다.
- **@Svgr/rollup:** SVG를 컴포넌트 형태로 불러와서 사용 할 수 있게 해줍니다.
- **rollup-plugin-url:** data-URI 형태로 svg, png, jpg 파일 등을 불러와서 사용 할 수 있게 해줍니다. @svgr/rollup 플러그인을 사용 할 때, rollup-plugin-url과 함께 사용을 해야만 `import { ReaxtComponent as icon } from './icon.svg` 형태의 코드를 사용 할 수 있습니다.

### 1.2 peerDependency 설정하기

그 다음에 ract와 react-dom을 peerDependency로 설치해주세요.

```zsh
yarn add --peer react react-dom
# 또는 npm install --save react react-dom
```

yarn을 사용하지 않고 npm을 사용하면 `peerDependencies` 가 아닌 `dependencies` 로 설치가 되어있을 것입니다. 그런 경우에는, 그냥 텍스트를 바로 `peerDependencies로` 교체하시면 됩니다.

### 1.3 package.json 수정하기

`main`을 지우고, `module` 값을 `dist/index.js`로 설정합니다. 이는 우리가 ESModule 형태로 빌드한 결과물을 저장 할 경로를 의미합니다. 나중에 만든 패키지를 설치하고 불러오게 되면 이 `module` 값을 찹조하여 코드를 불러오게 됩니다.

```JSON
{
  "module": "dist/index.js"
}
```

### 1.4 rollup.config.js 작성하기

rollup을 사용하게 될 때 단순히 명령어로 옵션을 정해줄 수도 있지만 더욱 편히한 설정을 위하여 설정파일을 만드렁서 작업할 수 있습니다.

```javascript
import typescript from "rollup-plugin-typescript2";
import commonjs from "rollup-plugin-commonjs";
import resolve from "rollup-plugin-node-resolve";
import babel from "rollup-plugin-babel";
import external from "rollup-plugin-peer-deps-external";
import svgr from '@svgr/rollup';
import url from 'rollup-plugin-url';

import pkg from "./package.json";

// 어떤 확장자를 처리 할 지 정함
const extensions = [".js", ".jsx", ".ts", ".tsx"];

// babel-preset-react-app를 사용한다면 BABEL_ENV를 필수로 설정해야함.
porcess.env.BABEL_ENV = "production";

export default {
  // 어떤 파일부터 불러올지 정함.
  input: "./src/index.ts"
  plubins: [
    // peerDependencise로 설치한 라이브러리들을 external 모듈로 즉, 번들링된 결과에 포함시키지 않음
    external(),
    // node_modules 에서 모듈을 불러올 수 있게 해줌
    resolve({ extensions }),
    // Typescript를 사용할 수 있게 해줌
    typescript({
      rollupCommonJSResolveHack: true,
      clean: true
    }),
    // CommonJS 형태로 만들어진 모듈도 불러와서 사용 할 수 있게 해줌
    commonjs({ include: 'node_modules/**' }),
    // Babel을 사용 할 수 있게 해줌
    babel({ extensions, include: ['src/**/*'], runtimeHelpers: true }),
    // 미디어 파일을 dataURI 형태로 불러와서 사용 할 수 있게 해줌
    url(),
     // SVG를 컴포넌트로 사용 할 수 있게 해줌
    svgr()
  ],
  output: [
    {
      // 번들링한 파일을 저장 할 경로
      file: pkg.module,
      // ES Module 형태로 번들링함
      format: 'es'
    }
  ]
};
```

그 다음에 babel을 위한 설정을 해줍니다. `.babelrc` 파일을 루트경로에 생성한 뒤, 다음 코드를 입력하세요.

```JSON
{
  "presets": [["react-app", { "flow": false, "typescript": true }]]
}
```

이제 빌드를 진행합니다.

```zsh
yarn rollup -c
# 또는 ./node_modules/rollup/dist/bin/rollup -c
```

## 2. TypeScript declaration 파일 만들기

declaration이란, 우리가 만든 Typesciprt에서 사용하고 있는 타입 정보들을 지니고 있는 파일을 의미합니다. 이는 다음 명형어로 생성할 수 있습니다.

```zsh
$ tsc --emitDeclarationOnly
```

이 명령어를 실행하기 전에 tsconfig.json을 수정해야 합니다.

```JSON
{
  "compilerOptions": {
    "target": "es5",
    "lib": ["dom", "dom.iterable", "esnext"],
    "skipLibCheck": true,
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    "strict": true,
    "forceConsistentCasingInFileNames": true,
    "module": "esnext",
    "moduleResolution": "node",
    "resolveJsonModule": true,
    "jsx": "react",
    "declaration": true,
    "declarationDir": "dist/types"
  },
  "include": ["src"],
}
```

위와 같이 `declaration` 값을 `true`로 바꾸고 `declarationDir`경로를 `disy/types`로 변경합니다. 이 두가지 값을 추가하면 기존의 몇가지 옵션과 충돌이 납니다.

- **allowJs:** 자바스크입트와 혼용을 하고 있다면 declaration 파일을 만들지 못합니다. 제거해주세요.
- **noEmit:** 결과물을 잔들지 않는다는 옵션입니다. 제거해주세요.
- **isolateModules:** 아무 값도 내보내지 않는 파일을 방지하는 옵션입니다. 제거해주세요.

## 3. 출처

[Rollup을 사용해서 npm 배포하기 - 플타 앞발자의 삽질로그](https://flamingotiger.github.io/frontend/DevEnv/rollup-setup/)
