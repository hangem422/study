# State

> 리액트 컴포넌트에서 다루는 데이터는 **props**와 **state** 두 가지로 나뉩니다. **state**는 컴포넌트 내부에서 선언하며 값을 변경할 수 있습니다.

## 1. State 정의

`constructor()`에서 `super(props)`를 호출한 이유는, 상속한 `Component`의 생성자를 미리 실행하기 위함입니다.

```JSX
import React, { Component } from 'react';

class Counter extends Component {
  constructor(props) {
    super(props);
    this.state = {
      number = 0,
    };
  }

  render() {
    const { number } = this.state;

    return (
      <div>
        <h1>카운터</h1>
        <div>값: {number}</div>
      </div>
    );
  }
}
```

## 2. State 변경

sate의 값을 바꾸기 위해서는 `this.setState()`를 무조건 거쳐야합니다. 이 함수가 호출되면 컴포넌트가 리렌더링 됩니다.

```JSX
state = {
  number: 0,
  foo: 'bar',
};
```

```JSX
// 변경
this.setState({ number: 1 });

// 결과
state = {
  number: 1,
  foo: 'bar',
};
```

### 객체 값 변경

`setState()`는 객체 깊숙한 곳 까지 확인하지 못하여, **전개 연산자**를 사용해야합니다. 이러한 작업은 복잡하므로 **immutable.js** 같은 라이브러리를 사용합니다.

```JSX
state = {
  number: 0,
  foo: {
    bar: 0,
    foobar: 1,
  },
};
```

```JSX
// 잘못된 변경
this.setState({
  foo: {
    foobar: 2,
  },
});

// 잘못된 결과
state = {
  number: 0,
  foo: {
    foobar: 2,
  },
};
```

```JSX
// 전개 연산자를 사용한 올바른 변경
const { foo } = this.state;
this.setState = {
  foo: {
    ...foo,
    foobar: 2,
  },
};

// 전개 연산자를 사용한 올바른 결과
state = {
  number: 0,
  foo: {
    bar: 0,
    foobar: 2,
  },
};
```

### 객체 대신 함수 전달하기

큰 차이는 없지만 `this.state`의 중복된 조회를 막을 수 있습니다.

```JSX
// 객체 전달
const { number } = this.sate;
this.setState({
  number: number + 1,
});

// 함수 전달
this.setState(
  state => ({
    number: state.number + 1,
  })
);

// 비구조화 할당 사용
this.setState(
  ({ number }) => ({
    number: number + 1,
  })
);
```

### 콜백 함수 사용하기

리액트는 `setState()`의 콜백 함수가 실행된 후 리렌더링 됩니다.

```JSX
const { number } = this.state;
this.setState({
  number: number + 1,
}, () => {
  console.log('콜백 함수가 실행된 후 리렌더링 됩니다.');
});
```

## 2. 메소드 작성하기

```JSX
import React, { Component } from 'react';

class Counter extends Component {
  constructor(props) {
    super(props);
    this.state = {
      number = 0,
    };
  }

  handleIncrease = () => {
    const { number } = this.state;
    this.setState({
      number: number + 1,
    });
  }

  handleDecrease = () => {
    const {number } = this.state;
    this.setState({
      number: number - 1,
    });
  }

  render() {
    const { number } = this.state;

    return (
      <div>
        <h1>카운터</h1>
        <div>값: {number}</div>
      </div>
    );
  }
}
```

화살표 함수를 사용하지 않고 다음과 같은 형식으로도 선언할 수 있습니다.

```JSX
constructor(props) {
  super(props);
  this.state = {
    number = 0,
  };

  // 함수가 클릭 이벤트 혹은 자식 컴포넌트로 전달되는 과정에서 "this"와 연결이 끊기지 않게 바인딩 해줍니다.
  this.handleIncrease = this.handleIncrease.bind(this);
}

handleIncrease() {
  const { number } = this.state;
  this.serState({
    number: number + 1
  })
}
```

## 3. 이벤트 설정

- 이벤트 이름은 **camelCase**로 설정해야합니다.
    - onclick -> onClick
    - onmousedown -> onMouseDown
    - onChange -> onChange
- 이벤트에 전달해주는 값은 **함수**여야합니다. 만약 `onClick={this.handleIncrease()}` 이렇게 작성하면 랜더링 할 때마다 함수가 호출됩니다.

```JSX
import React, { Component } from 'react';

class Counter extends Component {
  constructor(props) {
    super(props);
    this.state = {
      number = 0,
    };
  }

  handleIncrease = () => {
    const { number } = this.state;
    this.setState({
      number: number + 1,
    });
  }

  handleDecrease = () => {
    const {number } = this.state;
    this.setState({
      number: number - 1,
    });
  }

  render() {
    const { number } = this.state;
    const { handleIncease, handleDecrease } = this;

    return (
      <div>
        <h1>카운터</h1>
        <div>값: {number}</div>
        <button
          type="button"
          onClick={handleIncrease}
        >
          +
        </button>
        <button
          type="button"
          onClick={handleDecrease}
        >
          -
        </button>
      </div>
    );
  }
}
```

## 4. 출처

- [https://velopert.com](https://velopert.com/3629)