# JSX

> JSX는 자바스크립트의 문법 확장입니다. 리액트에서는 개발을 쉽게하기 위해서 HTML과 비슷한 문법으로 작성하면 `React.createElement()`를 사용하는 자바스크립트 형태로 변환시켜줍니다.

## 1. 규칙

XML 형태의 코드를 자바스크립트로 변환하기 때문에 몇가지 규칙을 준수해야 합니다.

### 꼭 닫혀야 하는 태그

태그는 꼭 닫혀있어야 합니다. `div`와 같은 태그 뿐만 아니라 `<input />`과 `<img />`같은 스스로 닫는 태그도 닫아줘야 합니다.

```JSX
<div>
  <input type="text" />
</div>
```

### 감싸져 있는 엘리먼트

두개 이상의 엘리먼트는 무조건 하나의 엘리먼트로 감싸져있어야 합니다.

```JSX
<div className="container">
  <div className="first-element">
    Hello
  </div>
  <div className="second-element">
    World
  </div>
</div>
```

스타일 관련 설정 등의 이유로 새로운 div로 감싸는 것이 제한될 경우 `Fragment`를 사용하면 됩니다.

```JSX
import React, { Component, Fragment } from 'react';
```

```JSX
<Fragment>
  <div className="first-element">
    Hello
  </div>
  <div className="second-element">
    World
  </div>
</Fragment>
```

### 자바스크립트 값 사용하기

JSX 내부에서 자바스크립트 값을 사용 할 때는 다음과 같이 사용합니다.

```JSX
const data = 'world';
return (
  <div>
    hello {name}
  </div>
);
```

### 조건부 렌더링

JSX 내부에서 조건부 렌더링을 할 때 if문을 사용할 수 없습니다. 대신 삼항 연선자를 사용합니다.

```JSX
<div>
  {1 + 1 === 2 ? (<div>True</dic>) : (<div>False</div>)}
</div>
```

삼항 연산자는 true일 때와 false일 때 다른 것들을 보여줍니다. 하지만 AND 연산자를 사용하면 true일 때만 보여줍니다.

```JSX
<div>
  {1 + 1 === 2 && (<div>True</div>)}
</div>
```

대부분의 상환은 위의 방식으로 해결이 가능합니다. 하지만 복잡한 조건을 작성해야 할 때도 있습니다. 그런 조건들은 JSX 밖에서 처리하는 것이 좋지만, 꼭 내부에서 작성해야 한다면 IIFE를 사용합니다.

```JSX
<div>
  {
    (() => {
      if (value === 1) return (<div>One</div>);
      if (value === 2) return (<div>Two</div>);
      return (<div>Else</div>);
    })()
  }
</div>
```

### 스타일

리액트에서는 스타일을 객체 형태로 작성합니다.

```JSX
const style = {
  backgroundColor: 'black',
  padding: '16px',
  color: 'white',
  fontSize: '12px',
};
 
return (
  <div style={style}>
    Hello World
  </div>
);
```

### 클래스

리액트에서는 `class` 대신에 `className`을 사용합니다.

```JSX
<div className="App">
  Hello World
</div>
```

### 주석

`{/* ... */}` 사이에 넣거나 태그 사이에 넣을 수 있습니다.

```JSX
<div>
  {/* 주석 예시 01 */}
  <h1
    // 주석 예시 02
  >
    Hello World
  </h1>
</div>
```

## 2. 출처

- [Https://velopert.com](https://velopert.com/3626)
