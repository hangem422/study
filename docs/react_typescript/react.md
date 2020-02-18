# 리액트에서 사용하기

> 타입스크립트가 적용된 리액트 프로젝트를 만드는 방법을 알아보고, 리액트 컴포넌트를 타입스크립트로 작성하는 방법에 대하여 다뤄봅니다.

## 1. 프로젝트 생성

타입스크립트를 사용하는 리액트 프로젝트를 만들때는 다음과 같이 명령어를 사용합니다.

```zsh
$ npx create-react-app sample-project --typescript
```

프로젝트의 src 디럭터리 안에 App.tsx라느 파일이 생성됩니다. 타입스크립트를 사용하는 리액트 컴포넌트는 이와 같이 `.tsx` 확장자를 사용합니다.

## 2. 새로운 컴포넌트 만들기

컴포넌트의 props에 대한 타입을 선얼 할 때에는 `type`을 써도 되고, `interface`를 써도 됩니다. 일관성만 유지하면 됩니다.

```TSX
import Reat from "react";

type GreatingsProps = {
  name: string;
};

const Greatings: React.Fc<GreatingsProps> = ({ name }) => (
  <div>Hello, {name}</div>
);
```

#### React.FC의 장점

1. 기본적으로 `children`이 들어가있다.
2. defaultProps, propTypes, contextTypes를 설정 할 때 자동완성이 될 수 있다.

#### React.FC의 단점

`children`이 옵션ㄹ 형태로 들어가니 컴포넌트 props의 타입이 명백하지 않습니다. 예를 들어 `children`이 무조건 있어야 하는 경우나 들어가먼 안디는 경우를 처리하기 위해서는 결국 Props 타입 안에 `children`을 명시해야 합니다. 차라리 `React.FC`를 사용하지 않고 Props 타입을 통해 `children`이 있다 없다를 명백하게 명시하는게 덜 헷갈립니다.

```TSX
type GreateProps = {
  name: string;
  children: React.ReactNode;
}
```

추가적으로, (아직까지는) `React.FC`를 사용하는 경우 `defaultProps`가 제대로 작동하지 않습니다. 하지만 `React.FC`를 생략하면, 오히려 잘 작동합니다. 이러한 이슈 때문에 `React.FC`를 사용하지 말라는 팁도 존재합니다.

```TSX
import React from 'react';

type GreatingProps = {
  name: string;
  mark: string;
};

function Greatings({ name, mark }: GreatringsProps) {
  return <div>Hello, {name} {mark}</div>;
}

Greatings.defaultProps = {
  mark: '!'
};
```

#### 컴포넌트에 생략 할 수 있는 props 설정하기

props 중에서 생략해도 되는 값이 잇다면 `?` 문자를 사용하면 됩니다.

```TSX
type GreatingProps = {
  name: string;
  mark: string;
  optional?: string;
}
```

#### 함수 props 받아오기
