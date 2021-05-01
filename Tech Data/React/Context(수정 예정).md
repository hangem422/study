# Context API

단순히 전역 상태를 관리하기 위함이라면 **Redux**나 **MobX** 같은 라이브러리보다는 Context API를 활용하는 방법을 추천합니다.

## 1. Context 만들기

**Context**는 `createContext`라는 함수를 사용해서 만들며, 이 함수를 로출하면 **Provider**와 **Consumer**라는 컴포넌트들이 반환됩니다. **Provider**는 Context에서 사용 할 값을 설정할 때 사용되고, **Consumer**는 나중에 우리가 설정한 값을 불러와야 할 떄 사용됩니다.

```javascript
import React, { Component, createContext } from "react";

const Context = createContext();
const { Provider, Consumer: SampleConsumer } = Context;

class SampleProvider extends Component {
  state = {
    value: "기본값입니다",
  };

  actions = {
    setValue: (value) => {
      this.setState({ value });
    },
  };

  render() {
    const { state, actions } = this;
    const value = { state, actions };

    return <Provider value={value}>{this.props.children}</Provider>;
  }
}

export { SampleProvider, SampleConsumer };
```

## 2. Provider 사용하기

**Context**를 프로젝트에 적용하려면, 앱을 **Provider**로 감싸야합니다.

```javascript
import { SampleProvider } from "./contexts/sample";

const App = () => {
  return (
    <SampleProvider>
      <div className="container">
        <Component01 />
        <Component02 />
      </div>
    </SampleProvider>
  );
};
```

## 3. Counsumer 사용하기

**Consumer**는 컴포넌트에서 **Context**를 사용해야 할 때에 사용됩니다.

### 3.1 값 보여주기

```javascript
import React from "react";
import { SampleConsumer } from "../contexts/sample";

const Component01 = () => {
  return (
    <SampleConsumer>
      {({ state }) => <div>현재 설정된 값: {state.value}</div>}
    </SampleConsumer>
  );
};
```

### 3.2 actions 호출하기

**actions**를 통해서 값을 변경해보겠습니다. 이번에 컴포넌트를 구현하기 위해서 단순히 `render()`만 필요할게 아니라, 내부에 있는 메소드도 필요합니다. 따라서 `render()`에서 **Consumer**를 사용하는 형태로 구현하지 않고, `SendsContainer`라는 컴포넌트를 추가적으로 만들어서 `props`로 필요한 값을 전달하는 방식을 사용합니다.

```javascript
import React, { Component } from "react";
import { SampleConsumer } from "../contexts/sample";

class SampleComp extends Component {
  state = {
    inpur: "",
  };

  componentDidMount() {
    this.setState({
      input: this.props.value,
    });
  }

  handleChange = (e) => {
    this.setState({ input: e.target.value });
  };

  handleSubmit = (e) => {
    e.preventDefault();
    this.props.setValue(this.state.input);
  };

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <input value={this.state.input} onChange={this.handleChange} />
        <button type="submit">설정</button>
      </form>
    );
  }
}

const SendsContainer = () => {
  <SampleConsumer>
    {({ state, actions }) => (
      <SampleComp value={state.value} setValue={actions.setValue} />
    )}
  </SampleConsumer>;
};

export default SendsContainer;
```

## 4. Hoc 사용하기

##### context

```javascript
import React, { Component, createContext } from "react";

const Context = createContext();
const { Provider, Consumer: SampleConsumer } = Context;

class SampleProvider extends Component {
  // ...
}

function useSample(WrappedComponent) {
  return function UseSample(props) {
    return (
      <SampleConsumer>
        {({ state, actions }) => (
          <WrappedComponent
            value={state.value}
            setValue={actions.setValue}
            {...props}
          />
        )}
      </SampleConsumer>
    );
  };
}

export { SampleProvider, SampleConsumer, userSample };
```

##### component

```javascript
export default useSample(SampleComp);
```

## 5. Hoc 만드는 함수 만들기

Context를 만들 때 마다 Hoc를 작성하는게 귀찮을 수도 있습니다. 또 같은 Context를 사용하지만 상황에 따라 다른 props를 전달해줘야 할 때도 있을겁니다.

```javascript
import React from "react";

const createUseConsumer = (Consumer) => (mapContextToProps) => (
  WrappedComponent
) => {
  const defaultMapContextToProps = (context) => ({ context });

  function UseConsumer(props) {
    return (
      <Consumer>
        {(context) => {
          const contextProps = (mapContextToProps || defaultMapContextToProps)(
            context
          );
          return <WrappedComponent {...contextProps} {...props} />;
        }}
      </Consumer>
    );
  }

  const displayName =
    WrappedComponent.displayName || WrappedComponent.name || "component";
  UseConsumer.displayName = `UseConsumer(${displayName})`;
  return UseConsumer;
};

export default createUseConsumer;
```

##### Context

```javascript
const useConsumer = createUseConsumer(SampleConsumer);

export { SampleProvider, SampleConsumer, useConsumer };
```

##### component

```javascript
export default useConsumer(({ state, actions }) => ({
  number: state.number,
  increment: actions.increment,
}))(SampleComponent);

// export defualt useConsumer()(SampleComponent)
// this.props.context.number
```

## 6. Context가 여러개

```javascript
const AppProvider = ({ contexts, children }) => context.reduce(
  (prev, context) => React.createElement(context, {}, prev), children);
);

function App() {
  return (
    <AppProvider>
      <div className="container">
        <Component01 />
        <Component02 />
      </div>
    </AppProvider>
  );
}

export default App;
```

## 7. 출처

- [리액트 16.3 에 소개된 새로워진 Context API 파헤치기 - VELOPERT.LOG](https://velopert.com/3606)
