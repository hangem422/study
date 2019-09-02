# Props

> 리액트 컴포넌트에서 다루는 데이터는 **props**와 **state** 두 가지로 나뉩니다. props는 부모 컴포넌트가 자식 컴포넌트에게 주는 값이며, 받아온 **props**는 자식 컴포넌트가 수정할 수 업습니다.

## 1. Props 전달

일반 태그의 속성값을 작성하듯이 작성하면 됩니다.

```JSX
import React, { Component } from 'react';
import MyName from './MyName';

class App extends Component {
  render() {
    return (
      <MyName name="리액트" />
    );
  }
}

export default App;
```

## 2. Props 사용

### 클래스형 컴포넌트

받아온 props 값은 `this.` 키워드를 통하여 조회 할 수 있습니다.

```JSX
import React, { Component } from 'react';

class MyName extends Component {
  render() {
    const { name } = this.props;

    return (
      <div>
        안녕하세요! 제 이름은 {name}입니다.
      </div>
    );
  }
}

export default MyName;
```

### 함수형 컴포넌트

단순히 **props**를 받아와 보여주기만 하는 컴포넌트의 경우 더 간편하게 작성할 수 있습니다.

```JSX
import React from 'react';

const MyName = ({ name }) => {
  return (
    <div>
      안녕하세요! 제 이름은 {name}입니다.
    </div>
  );
};

export default MyName;
```

## 3. Props 기본값 설정

부모에서 실수로 props 전달을 빠트리거나, 특정 상황에 props를 일부러 비워야 할 경우를 대비해 기본값을 설정할 수 있습니다. 클래스형 컴포넌트와 함수형 컴포넌트에서 사용 방법은 같습니다.

```JSX
MyName.defaultProps = {
  name: '기본이름',
}
```

## 4. Props 타입 검증

전달받은 데이터의 유효성을 검증을 통해 다양한 버그를 예방할 수 있습니다. 클래스형 컴포넌트와 함수형 컴포넌트에서 사용 방법은 같습니다.

### 설치하기

```bash
npm install --save prop-types
```

### 사용하기

prop-types를 사용하기 위해서 import 해줍니다.

```JSX
import PropTypes from 'prop-types';
```

기본값이 선언되지 않은 데이터의 타입 뒤에는 `.isRequired`를 붙여야합니다. 사용할 수 있는 타입 종류는 [여기](https://www.npmjs.com/package/prop-types)를 참고해주세요.

```JSX
MyName.propTypes = {
  name: PropTypes.string,  // 기본값이 선언 된 경우
  noDefaultExample: PropTypes.number.isRequired,  // 기본값이 선언되지 않은 경우
}
```

## 5. 출처

- [https://velopert.com](https://velopert.com/3629)