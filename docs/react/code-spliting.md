# 코드 스플리팅

> 코드 스플리팅을 하게 되면, 지금 당장 필요한 코드가 아니라면 따로 분리시켜, 나중에 필요할 때 불러와 사용할 수 있습니다. 이를 통하여, 페이지 로딩 속도를 개선 할 수 있습니다.

## 1. 함수 코드스플리팅

`import`를 함수로 사용하면, **Promise**를 반환합니다. `import()` 함수는 아직 표준은 아니지만 stage-3 단계에 있는 dynamic import라는 문법입니다. 현재는, webpack에서 지원해주고 있는 함수이기에 별도의 설정 없이 바로 사용 할 수 있습니다. 이 함수는 모듈을 비동기적으로 **CommonJS** 형태로 불러오니, 따로 `default`를 명시해주어야 합니다.

```JSX
import React, { Component } from 'react';

class App extends Component {
  handleClick = () => {
    import('./notify').then(({ default: notify }) => {
      notify();
    });
  };

  render() {
    return (
      <div>
        <button
          type="button"
          onClick={this.handleClick}
        >
          Click Me
        </button>
      </div>
    )
  }
}
```

개발자 도구엑서 **Network** 탭을 열면 `1.chunk.js`라는 파일을 불러오게 되고, 그 안에 notify 관련 코드가 들어있습니다. 이렇게 분리된 파일을 **청크 파일**이라고 부릅니다. `import`를 함수로 사용해주면, 웹팩이 알아서 코드를 분리하여 따로 저장하고 호풀될 떄 불러와서 사용할 수 잇게 해줍니다.

## 2. 컴포넌트 코드 스플리팅

```JSX
import React, { Component } from 'react';

class App extends Component {
  state = {
    SplitMe: null,
  };

  handleClick = () => {
    import('./SplitMe').then(({ default: SplitMe }) => {
      this.setState({
        SplitMe
      });
    });
  };

  render() {
    const { SplitMe } = this.state;
    return (
      <div>
        <button onClick={this.handleClick}>Click Me</button>
        {SplitMe && <SplitMe />}
      </div>
    );
  }
}
```

## 3. HoC 사용하기

### Hoc 만들기
```JSX
import React, { Component } from 'react';

const withSpliting = getComponent => {
  class WithSplitting extends Component {
    state = {
      Splitted: null,
    };

    constructor(props) {
      super(props);
      getComponent().then(({ default: Splitted }) => {
        this.setState({
          Splitted,
        });
      });
    };

    render() {
      const { Splitted } = this.state;
      if (!Splitted) return null;
      return <Splitted {...this.props} />;
    }
  }

  return WithSplitting;
}

export default withSpitting;
```

### Hoc 사용하기

```javascript
const SplitMe = withSpliting(() => import('./SplitMe'));
```

## 4. 라우터와 함께 사용하기

#### pages/index.js

```javascript
import withSplitting from '../withSplitting';

export const Home = withSplitting(() => import('./Home'));
export const About = withSplitting(() => import('./About'));
```

#### src/App.js

```JSX
<Route exact path="/" component={Home} />
<Route path="/about" component={About} />
```

## 5. 서버 사이드 렌더린과 함께 할 때 문제점

## 서버에서는 리렌더링이 없음
서버사이드 렌더링에서는 우리가 컴포넌트를 문자영 형식으로 렌더링하는데, 도중에 state가 바뀐다고 문자열이 혼자 바뀌지 않습니다. 이에 대한 해결 방법으로는 서버에서는 코드스플리팅을 적용하지 않는 방법이 있습니다. 스플리팅된 페이지 컴포넌트가 들어있는 `pages/index.js`를 두 종류로 만들어서, 프로덕션 프라우저 코드에서만 코드 스플리팅된 `index.js`를 사용하도록 하면 이 문제를 해결할 수 있습니다. 이는 웹팩의 **NormalModuleReplacemetPlugin**를 사용하여 적용할 수 있습니다.

## 페이지 깜박임 현상

위 문제를 해결하고 나면 발생 할 수 있는 문제점은, 페이지 깜박임 현상입니다. 이 현상은 코드 스플리팅과 서버사이드 렌더링을 함께 하게 될 때 다음 흐름으로 진행되기 떄문에 발생합니다.

1. 서버에서 HTML을 생성해서 클라이언트에게 전달해줍니다.
2. 클라이언트는 전달받은 HTML을 페이지에 일단 보여줍니다.
3. Javascript가 실행이 되면, 스플리팅된 코드가 아직 로딩되어있지 않은 상태여서 기존에 보여줬던 것이 사랍니다.
4. 컴포넌트를 로딩 한 다음에 다시 렌더링을 합니다.

이를 방지하기 위해선 사전에 어떤 경로로 들어왔을때 어떤 컴포넌트를 불러와야하는지 따로 정의해놓고, 코드 로딩이 끝난다음에 `ReactDom.render` 함수를 호출 하는 것입니다.

## 6. 출처

- [https://velog.io/@velopert](https://velog.io/@velopert/react-code-splitting)
