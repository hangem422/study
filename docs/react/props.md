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

## 5. 부모 컴포넌트에 정보 전달

부모 컴포넌트에서 메소드를 만들고, 이 메소드를 자식에게 전달한 다음 자식 내부에서 호출하는 방식을 사용합니다.

### 부모 컴포넌트

```JSX
import React, { Component } from 'react';
import Child from './Child';

class Parent extends Component {
  constructor(props) {
    super(props);
    this.state = {
      name: '',
      phone: '',
    };
  }

  // input의 value를 바꾸는 메소드
  handleChnage = event => {
    this.setState({
      [event.target.name]: event.target.value
    });
  }

  // 입력한 데이터를 전송하고 input을 비워즈는 메소드
  handleSubmit = event => {
    event.preventDefault();

    const { name, phone } = this.state;
    console.log(`${name}의 전화번호는 ${phone} 입니다.`);
    this.setState({
      name: '',
      phone: '',
    });
  }

  // Child로 데이터와 그 데이터를 변형시키는 함수를 넘겨줍니다.
  render() {
    const { name, phone } = this.state;

    return (
      <div>
        <Child
          name={name}
          phone={phone}
          handleChange={handleChange}
          handleSubmit={handleSubmit}
        />
      </div>
    );
  }
}

export default Parent;
```

### 자식 컴포넌트

```JSX
import React, { Component } from 'react';
import PropTypes from 'prop-types';

// 함수형 컴포넌트입니다.
const Child = ({
  name,
  phone,
  handleChange,
  handleSubmit,
}) => (
  <form onSubmit={this.handleSubmit}>
    <input
      placeholder="name"
      name="name"
      value={name}
      onChange={handleChange}
    />
    <input
      placeholder="phone"
      name="phone"
      value={name}
      onChange={handleChange}
    />
  </form>
);

// 부모로부터 전달받은 데이터 타입을 검증합니다.
Child.propTypes = {
  name: PropTypes.string.isRequired,
  phone: PropTypes.string.isRequired,
};

export default Child;
```

## 6. 출처

- [https://velopert.com](https://velopert.com/3629)