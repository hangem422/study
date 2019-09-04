# Component

> 리액트를 사용하면 웹 애플리케이션에서 사용하는 유저 인터페이스를 재사용 가능한 컴포넌트로 분리하여 작성함으로서, 프로젝트의 유지 보수성을 우수하게 해줍니다.

## 1. Component 만들기

리액트와 그 내부의 Component를 불러옵니다. JSX를 사용하려면 꼭 React를 import 해주어야 합니다.

```JSX
import React, { Component } from 'react';
```

### 클래스형 컴포넌트

클래스형 컴포넌트는 반드시 `render()` 함수가 있어야 합니다. 함수 내부에서는 JSX를 return 합니다.

```JSX
class App extends Component {
  render() {
    return (
      <div className="App">
        Hello World
      </div>
    )
  }
}
```

클래스 밖 마지막 줄에는 작성한 컴포넌트를 다른 곳에서 사용할 수 있도록 내보내줍니다.

```JSX
export default App;
```

### 함수형 컴포넌트

함수형 컴포넌트는 **state**와 **LifeCycle**이 빠져있습니다. 그래서 컴포넌트 초기 마운트가 미세하게 빠르고, 메모리 자원을 덜 사용합니다.

```JSX
const App = () => {
  return (
    <div>
      Hello World
    </div>
  );
};

export default App;
```

## 2. 컴포넌트 사용하기

컴포넌트를 불러올 때는 import를 사용합니다.

```JSX
import App from './App';
```

브라우저 상에 리액트 컴포넌트를 보여주기 위해서는 `ReactDOM.render()` 함수를 사용합니다. 첫번째 파라미터는 랜더링할 컴포넌트, 두번째 파라미터는 컴포넌트를 그려 줄 DOM입니다.

```JSX
ReactDOM.render(<App />, document.getElementById('root'));
```

## 3. 컴포넌트 배열 사용하기

작성중...

## 3. 출처

- [Https://velopert.com](https://velopert.com/3626)
